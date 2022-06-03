# 3. Virtualisering av minnet segmentering

- Memory virtualization
Det fysiska minnet ger en viss process en del av sitt minnesutrymme & skapar en adressrymd som då är den virtualiserade minnes-rymden som processen har tillgång till.

- Physical vs. Virtual Address
Dess plats i minnet är egentligen i det fysiska minnet med en viss storlek men processen kan bara utnyttja adress-rymden.

- Transparent/Effective/Robust
Transparent: Man bör kunna hantera & synliggöra hanteringen mellan den fysiska adress platsen till den virtuella adressrymden.

Effektive: Konverteringen bör ta så få resurser/tid som möjligt.

Robust: Säker & isolerande från resterande processer & OS.

- Emulation vs. static relocation
Emulation: Arbetar med en emulator som börjar i första platsen i det fysiska minnet. Den tar data som kommer in och flyttar/justerar den fysiska adressen manuellt.

- Static relocation:  Programmet som laddas in hanterar själv vart i minnet den ska placeras. Dock inkluderar detta också adresser som är förbjudna dvs OS,

- vs. dynamic relocation
Med en MMU memory management unit kan en virtuell adress hanteras med base & bound register för att konvertera adresserna korrekt.

- Memory Management Unit (MMU)
Fysisk hårdvara som kombineras med base & bound register för att först addera base adressen mot den virtuella adressen & sen tar MMU det värdet & jämför mot bound registret så den fortfarande har rätt storlek. Då är det den fysiska adressen som används. 

- Base & Bound Registers
Base: Bas platsen som den virtuella adressen ska adderas med för att få den fysiska minnesadressen.

Bound: Registret som indikerar om den virtuella minnesadressen har rätt storlek eller om den är för stor.

Både lagras för varje process i sin PCB (process control block) så de kan flyttas vid varje context switch.

Segmentation Model
Segmentation model:
Adressrymd
Base & bound register
En lista med fritt minne (free list)
Code/Text/Heap/Stack tilldelas eget minne i adressrymden för att minimera andelen minne som inte används. Dessa tilldelas egna base & bound register.
Heap & Stack tilldelas också ett värde för att indikera i vilken riktning de växer i adressrymden.

Internal & External fragmentation
External fragmentation: När en process är klar & återlämnas till det lediga minnet kan det fragmenteras. 
Internal fragmentation: När en process ska tilldelas minne så kan delar av free list bli splittrad för att hitta en plats.

Free List, Splitting & Coalescing
Free List: En lista av ledigt minne i adressrymden med en indikator för storlek & ett magiskt nummer som ID/adress.
Splitting: Dela upp minnet i mindre segment för att kunna utnyttja det,
Coalescing: Slå ihop segment i större delar för att kunna utnyttja det.

Malloc
När malloc (memory allocation) anropas för en datatyp hanterar OS den minnes förfrågan genom att göra plats i free list för den. Men med det anropet så sparas en plats i form av en pekare till platsen. Men på plats -4/-8 läggs information om datatypens storlek & ID också.
Size → hptr (head pointer)
pdatatype → första platsen för datatypens minnesallokering.



Best, worst, first or next fit alg.
Best fit: OS söker igenom hela free list för att leta efter det block som mest passar det efterfrågade minnes-allokeringen. Skapar ett ineffektivt utnyttjande av tid & blir mycket intern fragmentering.
Worst fit: Samma som best fit men man tar alltid av det största blocket som finns.
First fit: Söker igenom listan tills ett block har hittats som är av tillräcklig storlek. 
Nest fit algoritm: Samma som first fit, men en pekare läggs i den senaste adressen ett minnesblock utnyttjades för att sedan fortsätta på den platsen.

Garbage collection
En idle task som framförallt coalesce block utav minne i free list som blivit fragmenterade.

Finding size of free() mem. Block?
Free listan använder en så kallad länkad lista. Vilken är en struct som håller reda på två saker: En indikator för storlek & en indikator för next. Alltså nästa platsen som minnesadressen fortsätter på. 
Så när malloc kallas för att allokera plats kollar OS igenom free listan, Splittar en del utav free listan och allokerar den platsen om möjligt med sina bytes + 8. (bytes av free list - allokerade bytes + 8). 
När free() tillslut kallas för respektive process så görs en plats i free list ledigt med samma storlek & en pekare (next) som indikerar platsen till nästa free list plats.

Segregated lists
Om vissa blockstorlekar är mer efterfrågade än andra kan algoritmer implementeras för att snabba på free list hanteringen. Allokering hanterar då ett antal free list där alla utom en består av block med en fixad storlek. & en annan lista som tar hand om övriga förfrågningar.

Buddy allocation
I free list allokeras en minnesplats ett ID med en fixad storlek i 4B var. När malloc & coalescing ska köras hanteras detta enkelt då blocken som ligger bredvid lätt kan slås ihop vid behov.


# 4. Virtualisering av minnet Paging
Paging model
Delar upp minnet i olika sidor i bestämmda storlekar som alla är lika stora. 
Adressens MSB blir ett sidnummer (page address) med resterande bitar en offset för att nå bytsens minnesplats. 
I adressrymden ligger sidorna med en total storlek som bestämmer VPN (virual page number) & offsetsen. Tex 64 byte → 2^6 → 2 bytes för VPN & 4 Bytes för offseten. Dessa pekar ut en plats i det fysiska minnet (page frame) som sidorna hamnar på.
Den virtuella page översätts mha en så kallad page table för att få korrekt värde mot page frame. I översättning är offseten densamma men VPN görs om till en PFN (physical frame number). Varje process har sin egna sidtabell för översättningarna.

Linear Page Table, Address translation
Linear page table: 32 bitar för en översättning mellan VPN → PFN. 20 bitar för VPN→PFN & 12 bitar för offseten → 4KB page offset & 4MB för varje översättning. Har ett Page table entry som gör två saker: 
1: Kollar om VPN är giltig för översättning, dvs om den är 0 eller 1.
2: Ett register med de 32 bitarna, där de första 20 är ämnade för PFN översättningen & de resterande 12 för olika register funktionaliteter.

Page Table Base Register
En cache som pekar mot page table för varje översättning.

Page, Segmentation, Protection fault
Page fault: Uppstår när page table eller TLB ska översätta en page som inte är närvarande. Om detta sker så sköter page-fault handler inom OS den uppgiften. Då hanterar OS det genom att skicka en förfrågan till disken om PTE för den page där den skickas tillbaka & sedan uppdateras page tablen.

Translation Look-aside buffer (TLB)
Är en del utav enhetens MMU (memory management unit) som är en minnes cache av tidigare/mest populär översättningar i page table. Genom att kolla igenom TLB först kan man förbigå page table om översättningen har skett förut → HIT. Men om den inte har skett då sker en MISS & översättningen måste gå igenom page table. 
Detta sker genom att först extrahera VPN från en page & se om TLB har den översättningen. → PFN kan då snabbt extraheras från TLB & dp är översättningen klar. Om det blir en miss så hanterar page table översättningen & den översättningen lagras samtidigt i TLB cashe.
Dock så uppstår ett problem då varje process översättningen är unik för den översättningen men TLB sparar undan alla översättningar. Så i ett context switch måste en ASID implementeras vilket är ett nummer som indikerar om processens översättning är valid för den översättningen. 

Spatial & temporal locality
Spatial locality: Betyder att data som används flera gånger ligger nära varandra i minnet.
Temportal locality: Byter att en viss data som används flera gånger inom en kort tid.

Least recently used vs. Random


Multi-level Page Table
Genom att ta en linear page table & dela upp den i mindre “träd” kan man ta bort alla invalida regioner som annars skulle vara lagrade i minnet. Detta gör man genom att dela upp page table i mindre segment i page storlekar. 
Om page table pagen sen är invalid så laddas inte de in i minnet alls. För att kolla om page table pages är valid implementeras ett page directory.
Page directory har två funktioner: Kolla vart page table page är eller om hela page för age table ens är valid.

Linear vs multi level page table:
I ett linjärt page table så måste fortfarande alla invalida regioner är minne för page table minnet. I Multi level page tabel så pekar page directory mot de valida platserna. 
Page directory har två delar: en page directory entry & PFN. Där PDN om valid biten är satt indikerar att minst en utav platserna i pt är valid.

Storage Hierarchies
Hur lagras minne i förhållande till kapacitet & hastighet, som ofta har ett motsatt förhållande. Detta kan ses i storage hierarchies där de med störst lagringskapacitet har lägst hastighet och vice versa. I storleksordning från största minne/långsammast så är det hårddiskar/cloudstorage → Flash → RAM/DRAM → CPU (Cache → register → alu)

Swap space
Är ett minne i hårddisken som OS refererar pages till. När de ska refereras har de än disk adress som indikerar minnesplatsen. 

Page replacement policies
När minnet är fullt & en del page behöver bytas ut. 

FIFO vs. Random vs. LRU/LFU
FIFO: Cachar de första miss i minnet (cold start) där den första missen efter det slänger ut den första minne refereringen & lägger den första minne refereringen som var en MISS sist. 

Random: Kastar ut en slumpad referering ur cachen när en miss uppstår efter cold start.

LRU: Least recently used/ least frequently user: Sparar undan de träffar/missar som oftast händer. Sen kollar den på vilka som mest åkallas & har då minst chans att kasta ut den page.

The Clock Algorithm
En cirkulär kö av ordnade sidreferenser som initialt är satt till värdet 0. Varje gång en sida refereras adderas biten som är 0 till 1. När en sida ska slängas ut så indikerar visaren i samma riktning en träff som 1 → 0 men när en miss sker så slänger den ut sidreferenser & tar en nästa.
