# 5. Concurrency - Parallelism & Locks

- Inter Process Communication (IPC)

När olika processer som exekveras genom scheduleraren behöver kommunicera så kallar men det inter process communication.
Det sker genom tre metoder främst:

1: Via en fil

2:Via datakommunikation

3: Via signaler

- Threads, POSIX API

Gör det möjligt med parallella aktiviteter inom ett program/process. 

En tråd som skapas delar samma adressrymd som föräldrar-tråden men har separat stack utrymme. Varje tråd har en egen programräknare & ett eget processorregister.

POSIX Threads → Portable OS interface for UNIX.

Har möjligheter att hantera trådar. pthread_t*. → pthread create. → pthread_join

Mutex → Mutual exclusion.

Condition variables.

Synkronisering.

- Single vs. Multithreaded

Single threaded/single core kör endast föräldrartråden.

Multithreaded är processer med flera trådar där varje tråd har sin egna stackutrymme & TCB (Thread control block) → PCB.

- Race condition

Scheduleraren kan inte med garanti säga att programmet kommer att exekvera i den ordningen som koden är skriven i. Olika trådar kan schemaläggas  i olika sekvenser vilket kallas för race condition.

Deterministiskt skriven kod med en single thread kan skapa oväntat resultat med multithreaded om det finns critical sections. En critical section som: var = var + 1; blir i kompileringen ett flertal rader med assembler som då ett flertal trådar kan interagera med. Ex: en tråd gör första instruktionen men får då ett avbrott som gör att en annan tråd fortsätter på raden efter vilket ger ett icke determinisitikt resultat då var=var+1 ej genomfördes.

- Mutual exclusion

Ett område där endast en tråd kan köra åt gången. Skapas oftast med lås.

- Atomic instruction

Atomära instruktioner eller : single word variables operations. Utför hela sin instruktion i ett och samma steg. Omöjligt för flera trådar att påverka om en interrupt sker innan eller efter.

- Test-and-set

Mutual Exclusion för critical sections med locks.

- Compare-and-swap/exchange

Atomär instruktion inom multithreading för att jämföra en minnesplats mot ett värde & om den är samma så laddas det in ett nytt värde i den platsen.

- Load-link

Atomär instruktion som returnerar det nuvarande värdet för en minnesplats.

- Store-conditional

Laddar in ett värde i platsen som en load-link returnerat.

- Fetch-and-add

Tar en minnesplats & adderar den med 1.

- Lock & Conditional Variables

MUTEX → Lock & unlock med den kritiska sektorn mellan.

Implementeringen av låsen måste vara:

1: Fungerande.

2: Rättvis - ingen tråd försummas.

3: Effektiv - inte ta processorkraft i onödan.

En lock variabel som deklarerats med MUTEX kan vara tillgänglig/ledig eller använd/låst.

Spinwait är en funktionalitet som uppstår när flag variable sätts till lås i en kritisk sektion när en tråd kör dess instruktioner. Medans detta sker så sätts alla andra trådar till spin inom ett spinlock tills dess att flag varablen sätts till 0 + att låsets är unlocked. Detta skapar två problem: Correctness problem & effektivitet. Tiden som trådarna bara står och snurrar i en while loop är inte effektivt.

- Spins med test-and-set

Test and set gör tre saker:

1: Den returnerar det gamla värdet till ett värde.

2: Den uppdaterar det gamla värdet till det nya värdet.

3: Returnerar det gamla värdet.

Detta gör det möjligt att jämföra det gamla värdet mot det nya. 

Vilket man använder med flaggan för att jämföra om den är 1/0 med flera trådar, ex:

Om en tråd tar låset och kallar på test and set med 1 (alltså att den tagit låset) blir test and set 1 men det gamla värdet 0. Så när tråden har lock & en annan tråd kör så kör den test and set men då är det gamla värdet 1 vilket säkerställer att en tråd är i låset.

- Total Store Order

Ordningen av minnesacc som en annan kärna har tillgång till. Men det kanske inte är den faktiska ordningen. När flera trådar ska spara undan ett värde för en instruktion kan ordningen skapa problem då trådarna inte nödvändigtvis gör det samtidigt. 

- Priority Inversion

Priority inversion problem sker inom schedule-ringen av högt prioriterade tasks när en lågt prioriterad task tar över och då effektivt inverterar prioriteringarna för alla tasks. Ex: Om jobb A & B med låg respektive hög prioritet delar på samma resurser C. Om A försöker ta resurs C efter att B har tagit den så kommer A att blockeras innan B är klar. 

- Spinlock 

Spinlock med flaggor.

Test-and-set med flaggor & flera trådar.

Spinlock med atomära instruktioner:

compare-and-swap/exchange

Load-link + store conditional

Fetch and add

- Sloppy Counter

En global räknare fylls på vart efter att respektive tråd/kärna räknat upp ett visst antal steg så blir effektiviteten bättre.

# 6. Concurrency - Conditional variables & Semaphores

- Condition Variables

Avser att lösa problemet som uppstår med flera trådar då de måste vänta på varandra innan det går att fortsätta. En condition variable är en variabel med ett flertal funktioner som behandlar detta.

Wait: väntar på klartecken.

Signal: Signalerar klartecken så wait släpps.

- Wait & Signal

Genom att använda wait så kan man styra hur trådarna behöver vänta. Om wait & signal ligger mellan ett lås så kan man garantera att antingen en av trådarna (föräldern eller barnen) kör tills dess att den hamnar på wait eller signal för att väcka de på wait. Genom att ha en flagga som kollar om den är 0 → startvärdet eller 1 → om en barntråd har kört så kan man garantera i vilken ordning trådarna kör.

- Spurious wakeups

Om condition variabeln vaknar upp pga av interrupt innan variabeln är satt kallar man det för en spurious wakeups. Därav implementerar man lås när variablen ska sättas eller kollas.

- Bounded Buffer

Producent/konsument (begränsad buffert). Där en funktion producerar ett värde & en annan funktion konsumerar det värdet. Kan också implementera flera producenter och konsumenter. Bounded buffer kommer från att värdena lagras i en buffer tillsammans med en räknare som håller reda på hur mycket buffern fyllts på. 

Utan condition variables dock så sker två saker: Med flera trådar kan ett antal kritiska sektorer bli interrupted & race condition kan uppstå. Samt att trådarnas sekvens kan bli helt fel utan en condition variable. 

Producenten måste köra först så genom att implementera en condition variables inom ett lås som kollar om producenten har fått köra (count==1) → wait eller i konsumenten om producenten inte har kört count==0 → wait så kan man styra sekvensen.

När man dock implementerar ett flertal konsumenter så kan ena konsumera innan den andra konsumenten i vilket fall alla hamnar på wait → deadlock.
Detta löser man genom att implementera buffern som en array med ett MAX antal platser. Endast när alla platser är fulla/tomma så hamnar trådarna på wait vilket gör en thead safe bounded buffer. Väcker alla genom broadcast.

- Semaphores

En utveckling från condition variables. Man initierar en semaphore med sem_init(&s(semaphoren), 0 (lokal till den här processen), 1 (startvärdet på semaforen)).

Med sem_wait minskar man värdet på startvärdet med 1. 

Med sem_post ökar man startvärdet med 1.

När värdet hamnar under 0 så väntar tråden där.

När värdet är över 0 så kan trådarna fortfarande köra.

Semaphrer kan utvecklas att fungera som ett lås.

Genom att först sätta det initiala värdet till ett så kan man:

Sem_wait → 0 (låst om en till kör)

Critical section 0 (låst om en till kör)

Sem_post 1 (upplåst för nästa)

Kan skapa synkronisering också genom att:

I början av exekveringen sätta semaforen till 0 & när en till tråd skapas kan två saker hända. Antingen exekverar main tills dess att den når wait & blir låst tills dess att barnet exekveras och kör post i vilket fall föräldern väcks & kan köras. Annars om barnet kör först & kör post vilket ökar värdet till 1 så kan både föräldern & barnet föra igenom wait.

- Reader-Writers Lock

Ett lock som anpassas för att kunna hantera flera olika typer av datatyper som ska låsas. Dessa har funktionerna lock_aquire_writelock & lock_relesase_writelock som tar låset och sedan ger ifrån låset. Dessa fungerar tillsammans med semaforen writelock. Dessa fungerar så att ett flertal trådar kan vara en reader men endast en write kan uppstå när alla trådar som gör en read är klar.

- Deadlock

Deadlock uppstår när alla trådar som inväntar på en post eller att få fortsätta i sitt lås håller varandras upplåsning. Uppstår tex om två trädar båda kallar på lock på varandra & båda blir låsta.

- Mutual exclusion

När trådar håller kontroll över en resurs inom lås. tex inom en kritisk sektor.

- Hold-and-wait

Trådar som håller en resurs som är allokerade till de men väntar också på ytterligare värden.

- No preemption

Resurser kan inte tas bort från trådar som håller i de eftersom de håller i ett lås.

- Circular Wait

Det finns ett cirkulärt system av trådar som håller lås. Där olika lås är beroende av varandra & deras trådar.

- Prevention,
Avoidance,
Detection &
Recovery

- Circular wait prevention: Sker genom att fråga efter lås efter varandra i en sekvens beroende på i vilken ordning trådarna kör.

- Prevention hold and wait: Man skriver kod som aldrig får hålla alla lås vid samma tillfälle. Man skapar ett prevention lås som är utanför resterande lås. 

- Prevention no preemption: Koden skapas så att det aldrig hålls ett lås om man är blocked. Använder test and set metoden för att kolla detta.

- Prevention mutual exclusion: Kod som helt enkelt inte behöver lås alls. Atomäta instruktioner endast.
