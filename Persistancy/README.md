# 7. Persistence - Lagring/Storage

- Persistens, Storage

Access till kringutrustning som kan erbjuda ett icke flyktigt minne & kommunikation i form av device drivers → hårddisk. 
OS ger access till kringutrustningen via dessa device drivers.

- Device Drivers

På den lägst nivån så måste en programvara veta i detalj hur kringutrustning fungerar. Dessa kallas för device drivers.
Device drivers existerar ofta i flera lager av kod emot applikationen som går igenom filsystem innan de når dessa. Med en hårddisk ex så ligger filsystem emot en laddad device driver som kommunicerar med hårddisken genom ett interface. Detta interface består av tre register: Status, Command & Data.

Status: Ifall hårddisken är redo eller används.

Command: Ifall man vill skriva eller läsa.

Data: Varifrån data är & vart den ska någonstans som en buffer.

- Memory mapped I/O vs.

Memory mapped I/O är en input/output metod för att kommunicera mellan CPU & kringutrustning. Med MM I/O Så finns alla instruktioner i samma adressrymd för minnet & I/O.

- Dedicated Instructions

Till skillnad från memory mapped I/O så kör dedicated instruktions unika instruktioner för att hantera kommunikationen mellan en CPU & kringutrustning. Dessa sker främst med IN/OUT instruktioner som läsa eller skriva till kringutrustningen. 

Med en hårddisk så finns det också tre andra:

Status: Om enheten är upptagen eller ledig

Command: Om man ska läsa eller skriva till enheten.

Data: Vart/Till data ska i form av en buffer.

- Polled vs. Interrupt driven I/O

Polled eller interrupt driven I/O är två sätt för OS att veta när en I/O förfrågan är klar. Om man initerar en Polled driven I/O så kollar OS om ett värde har uppfyllts när I/O är klar. Medans en interrupt driven I/O initerar ett avbrott/context switch när I/O är klar. 

- Direct Memory Access (DMA)

Direct memory access är en extern enhet som avser att förbättra I/O kommunikation med kringutrustningen som då hanterar den istället för CPU. 
DMA fungerar som följande: För att överföra data till kringutrustning så programmerar OS DMA genom att berätta för den vart data ligger i minnet, hur mycket data att kopiera & till vilken enhet det ska till. När DMA är klar initerar den en interrupt som då berättar för OS att uppgiften är avklarad.

- Seek vs. Rotation Time. / Harddrive

En hårddisk består av ett antal sektorer i from av 512B blocks. Dessa block befinner sig på spår i en cirkulär platta & läses oftast av 4KB i taget med en magnetisk avläsning. Dessa block kan både läsas till eller skrivas till. Adressrymden för en hårddisk beskrivs i sektorerna som 0 → n-1. (n → number of sectors). 

För att läsa av eller skriva till en hårddisk så används en arm med ett huvud. Den sektorn som huvudet är på är den sektorn som används.

Rotation time/ Rotational delay/time: Är begreppet som omfattar i vilken mån en hårddisk läser av sektorer. Eftersom en hårddisk snurrar i höga hastigheter så kan huvudet på armen vänta tills rätt sektor har snurrat sig fram. Aka Rotation time.

Seek: En operation för att nå rätt sektor som befinner sig på en annan bana än den som armen befinner sig över nuvarande. Seek har flera faser som omfattar:

Acceleration: En ökning av hastighet hos armen

Coasting: Armens fulla hastighet

Deceleration: När armen närmar sig sektorn och saktar ner.

Settling: När armen nått sektorn & huvudet befinner sig över den.

- RAID

En teknik för att bygga upp ett flertal diskar i samarbete för att jobba snabbare/ större och mer pålitligt. Man tar då filer & dividerar på antalet diskar så när en fil ska läsas så blir den Hastighet*n snabbare. Dessutom kan flera kopior av filen spridas ut över flera diskar vilket ökar säkerheten.
Består av ett antal nivåer:

0: Sprid ut filen på alla diskar

1: Alltid en kopia på en annan disk

2: Sprid ut filen på flera diskar

3:Sprid ut filen på flera diskar

4: Återskapningsinformation

5: i form av paritetsinformation

6:Sprid ut filen på flera diskar

- File vs. Directory, (Un-)Mount

File: En linjär array av bytes som man antingen kan skriva till eller läsa ifrån. Varje fil har ett lågnivå namn, oftast i form av ett nummer. Namnet för filen brukar kallas för ett inode nummer. I ett directory så finns det filer eller andra directory. 

Directory: Har också ett lågnivå namn men innehållet är väldigt specifikt. Det innehåller en lista av användarnamn → lågnivå namn par. Ex om en fil har namnet 10 och är refererad med användarrelaterade namnet foo så är foo i directory (foo , 10). 

- Track (#0), Block & Sector in a file system

Ett filsystem har ett antal block med ett antal funktioner. Flertalet av dessa block är lagrade med så kallade datastrukturer. Den andra delen i ett filsystem är accessmetoder, alltså hur den mappar olika instruktioner från processer till dess strukturer. 

Data structure: Disken delas upp i ett antal block beroende på storlek. Oftast i storlekar av 4 KB. 

Flertalet av blocken går till användardata som lagras i dalaregionen som benämns D. 

Filsystemet håller också koll på metadata om dess filer, vilket inkulderar: Vilken del utav data regionen som innehåller en fil, storleken på filen, ägaren och accessrättigheter. 

Denna information lagras i så kallade inoder. 

- iNode, Superblock, Bit Map Block

Inode: En datastruktur som lagrar information om datablocken i dataregionen. De innehåller information i vilken dataregion det finns en fil, vilken storlek dessa datablock har, vilken access rätt, vem som är ägaren. Dessa block lagras i inode-table. Som är en array av inode block. Inode har typiskt en storlek på 128-256B storlek, så om blocken är 4 KB så finns det plats för 16-32 inode per block. 

Inodeblocken indikeras med sitt i-nummer som är ett värde mellan 0-n(inode). Med ett visst i-nummer kan berökna vart i disken inodeblocket är i block & sektor:

block = (i-nummer*sizeof(inode_table)) / blocksize.

Sektor = ((block * blockSize) + inodeStartAddress) / sectorsize.

Bitmap: Block som pekar ut om inode eller datablock är lediga eller allokerade. Dessa block kallas för en bitmap. en för datablocken: data bitmap & en för inode, inode bitmap. Bitmap pekar på blocken med en 0 → om den är ledig, eller en 1 → allokerad.

Superblock: Innehåller specifik information om just detta filsystem. Som inkulderar antalet inode block, antalet datablock, vart inode table börjar och vart data regionen slutar. Samt ett magiskt nummer som pekar ut just detta filsystem.

. vs. ..

- Disk Aware File Systems
- 
Består utav ett antal cylindrar som alla har sitt egna spår. Den spårar från centrat för att inkuldera all kunskap kopplat till den ansultna diskens fyfiska konstruktion.

- Crash-consistency problem

Filsystemets datastrukturer måste vara persistent till skillnad från de i minne eller i en running process. De får ej termineras vid enhets terminering/crash vilket skapar utmaningar som benämns: crash-consistency problem. Hur hanterar man crashar/termineringar hos en enskild disk & hur hanterar man multipla diskar samt deras innehåll/kommunikation vid terminering hos en/flera av de. 

- fsck vs. Journaling (write-ahead logging)

Fsck - file system checker

Ett verktyg som låter inconsistencies hända. Alltså att olika diskar är osynkroniserade. Den hanterade detta vid omstart av systemet. 
Fsck arbetar i ett antal stadier som beskrivs enligt:

Superblock: Sanity check på superblocken, 

Lediga block: Kör en jämförning mellan inode blocken & bitmap blocken. Om mängeden inode som är allokerade skiljer sig från bitmap blocken så har det blivit fel → korrigera.

Inode states: Kollar igenom inodeblocken & ser att dess länkning är giltig.

Inode links: Kollar igenom länkningen för inodeblocken & jämför det mot directory för att se om det skiljer sig.

Duplicering: Finns det duplicering av block/inode hantera det.

Dåliga block: Om något block pekar utanför filsystemets storlek eller sin bestämde bound.

Directory koll: Directories som inode blocken refererar till existerar inom filsystemet.

Journaling - write ahead logging

Fungerar enligt följande: När disken uppdateras & innan man skriver över strukturerna som finns på plats så skrivs det ner en anteckning någon annanstans på disken vad som görs. Detta är write ahead som skrivs ner till en log. Genom att göra detta kan man vid en crash eller terminering gå tillbaka till loggen & jämföra mot det som skrev ner/ inte skrev ner. 

Skippa transaktioner utan korrekt TxE

Utför alla fullständiga transaktioner.

Transaktioner:

Detta hanteras ofta i en journal array med ett flertal funktioner:

1: Data write - skriver data till den slutgiltiga platsen & väntar på att bli klar.

2. Journal metadata write - skriver börjar platsen för blocket och metadata till den loggen, väntar tills klar. 
3. 
3: Journal commit - Skriver transaktions commit blocket till loggen, när det är klart så är transaktionen committed.

4: Checkpoint metadata - skriver innehållet av metadatans uppdatering till dess slutgiltiga plats. 

5: Free - Lägger transaktionen som klar i journal superblocket. 

- Log-structured file systems

Som journaling (write ahead logging) fast med en cirkulär log.

# 8. Persistence - Kommunikation

- IPC

Inter process communication med sockets. Sockets är en abstraktion av förbindelse mellan två ändpunkter som kan vara i samma eller en annan enhet.
Genom att applicera sockets kan man dela upp device drivers såsom tangentbord,skärm,disk och tjänster till andra platser.

- Shared memory

Shared memory tillåter processer att kommunicera genom att läsa & skriva till en viss minnesplats. Access måste dock koordineras via lock & undvika kärnanrop.

- Mapped memory

Liknar shared memory men den är också associerad till en fil i filsystemet. 

Läser in en namngiven fil till processens adressrymd. 

mmap → läser in en fil till önskad plats i minnet

msync → säkerställer att alla tar del av en write

munmap → släpper kopplingen till filen

- Pipes

Tillåter sekventiell kommunikation mellan en process till en relaterad process.

Pipes är en seriell buffert för enkelriktad kommunikation. Bufferten ser till så att ordningen bibehålls, blockerar skriv/läs om full/tom. Fungerar enbart mellan trådar och relaterade processer. 

- FIFO
First in first outs liknar pipes med skillnaden att orelaterade processer kan kommunicera då FIFO är given ett namn i filsystemet. 

- Socket
Stödjer kommunikation mellan orelaterade processer även på andra enheter.

Dubbelriktad kommunikation genom en port → en ip → kommunikationskälla (ethernet/wifi… osv). 

- TCP/UDP, Port number
- 
Transmission control protocol: Protokoll för överföring av paket över ett nätverk. I TCP så finns det en inbyggd garanti för att paketen kommer fram. Detta då TCP agerar enligt handskaknings-modellen med ACK flaggor. När ett paket skickas så initerar sändaren en SEND flagga i vilket motsvararen svarar ACK om paketet kommit fram. Om inte så skickar sändaren paketet igen.

User datagram protocol: Sänder paket utan någon form av garanti att alla paket kommer fram med rätt innehåll eller i rätt ordning. Sänder de oavbrutet i en ström av paket.

Port number: Identifikationsnummer mellan olika nätverk. Diverse portar berättar vilken typ utav tjänst som erbjuds. Portnummer 80 tex → HTTP.

- IP, IP Address, URL, DNS

IP - Internet protocol. 

IP Address - Enskild adress som ges till alla enheter så fort de kopplas upp mot internet.

URL - Uniform resource locator: är en teckensträng som identifierar en viss resurs på internet.

DNS - Domain name system: System på internet som förenklar ip-adresser till namn. 

- Socket(), Bind() & Listen() → Server

Socket: Skapar en socket till en viss port med systemets ip-adress.

Bind: Binder socketen till en specifik dator/tjänst.

Listen: Lyssnar efter inkommande paket/förfrågningar.

- Connect() & Accept() → Klient

Connect: Etablerar en kontakt från en klient till en server.

Accept: Accepterar den kontakten & etablerar flervägskommunikation.

- Read(), Write() & Close()

Read: Läser information från källan som skickar den.

Write: Skriver information till källan man etablerat kontakt med via accept.

Close: Stänger ner kontakten samt avsultning av socket.

- Distributed Shared Memory

Gör det möjligt för processer i olika enheter att dela på en stor virtuellt adressrymd. Skapar ett system där ett flertal “trådar” kan köra på olika enheter istället för olika processer. Hanteras inom ett OS minnessystem där sidorna nås på två sätt:

Best case - Sidan finn redan på maskinen och data kan snabbt hämtas upp.

On another machine: Annars om sidan inte finns utan den är på en annan enhet så måste den hämtas. Detta sker då ett page-fault skapas och skickas till andra enheter som ber de att hämta sidan. 

- Distributed File System

Göra det icke-flyktiga minnets plats transparent.

Genom ett centralt nätverk med ett flertal klienter som kommunicerar via en server som är uppkopplad mot ett RAID. Så kan man skapa platsoberoende access. Filerna kan delas. Förenklad administration. Bättre säkerhet & om möjligt mer transparent.

- Remote Invocation

För att göra tjänst/funktions plats transparent. Man använder det för att nå programvara på andra enheter som driver ens egna enhet. 

- Statelessness & Idempotent

Statelessness: En server ska inte behöva komma ihåg vad som hänt. Detta för att möjliggöra en snabb omstart & enkel hantering. Varje anrop måste alltså innehålla all information som krävs för att utföra det man kan tänkas önska. Istället så hanterar klienterna det genom att skicka dold information om tex filpekaren. 

Idempotent innebär att förfrågor säkerställs att de kommer fram. Då en klients förfrågan skickas så skapas en timer. Om inte svaret kommer fram så skickas förfrågan igen osv. Detta används med ett flertal andra systemanrop som write, read, lookup etc.

- Update visibility & stale cache

Update visibility innebär att klienten uppdateras sina interna filer som olika data. När data sedan förfogas så kanske man först får den filen man vill ha & sedan så skrivs den över med en äldre version från en annan klient. 

Stale cache är då servern har blivit uppdaterad men i någon av klienterna finns fortfarande en äldre version som cacherat och ligger kvar där. 

- RPC

RPC (remote procedure call) är ett protokoll som kan användas av ett givet program för att förfråga om en tjänst från ett program i en annan enhet.

- Web Services

HTTP - hypertext transfer protocol. Kommunicerar genom webben med: Protocol :// host : port / path. Finns diverse kommandon som beskriver vilken typ av kommunikation man vill utföra. 

Där de viktigaste är.

Get: För att hämta en viss resurs.

Post: För att skicka en viss resurs.
