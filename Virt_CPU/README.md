# 1. Virtualisering av processorn I

- Bare metal, Batch Job
OS exekverar direkt mot CPU med applikationsspecifik och kärn-kod i samma. Batch job är en utveckling där olika “batch” kunde köras i sekvens.

- Command Line Interface (CLI)
En lista av kontinuerliga instruktioner som skulle utövas av datorn.

- Kernel, Limited Direct Execution
Kernel är kärnan i OS, aka OS alla möjliga instruktioner. Direct Execution innebär att OS kör direkt mot CPU med minneshantering/parallelism. Limited är begränsningen som OS skapar för känsliga instruktioner.

- Supervisor/User mode
Limited direct Execution använder sig utav 2 moder: USER MORE & KERNEL MODE.
Där User mode är begränsad i dess förmåga att kalla på “känslig/farlig/skadliga” instruktioner. Endast Kernel mode, som OS kör i kan köra dessa instruktioner.

- System Call, Trap
Systemanrop är delar som OS gör möjligt för USER att implementera då användaren måste ha tillgång till Kernel delar. Den gör detta genom ett systemanrop som kallas för trap. När trap körs genereras ett skifte av OS som ger usermode högre access. Sedan genererar OS ett interrup: return from trap som sänker access-nivån för user-mode till det vanliga.

- Mechanisms & Policies
Mekanismer: Är implementationen av dessa policies. 
Policies: Avgör vilken aktivitet som ska köras. 

- Time Sharing/Multiprogramming
Genom att virtualisera processer i mindre komponenter i den applikationsspecifika koden är det möjligt att dela på dessa processer så man ger upphov av flera användare.

- Text, Data, Stack & Heap
Text: Instruktioner 
Data: Globala variabler
Stack: Lokala variabler, returvärden, funktioner…
Heap: Minnesallokering via malloc

- Stack Frame (in depth)
Växer nedåt från stacken med värden i rekursiva funktioner som uppträder reentrant.

- Program, Process & Machine State
Program: Innehållet i en exekverbar fil.
Process: När programmet är i datorns minne och exekveras, eller är redo att exekveras.

- Machine state: Allt som tillhör en process.

- Process Control Block (PCB)
Är ett minne i varje process som ger processen ett id, vilken typ utav schemaläggning processen befinner sig i osv. Sparas undan vid varje context switch.

- Context Switch & Scheduler
För ett byte tillstånd mellan processen finns det en så kallad scheduler. Den har som uppgift att schemalägga vilken process som ska exekveras och byter genom ett så kallat context switch. När ett systemanrop context switch genereras så sparas processens minne i PCB och ready → running.

- Cooperative vs. Preemptive
Cooperative ger tillbaka Kernel vid ett yield();
Preemptive har en timer som skapar en interrupt/return from trap efter en viss tidskvanta. 

- Process Stat
Process status är olika tillstånds som en process kan befinna sig i. →

- Ready, Running & Blocked
Ready → Redo att exekveras
Running → Exekveras
Blocked → Blockerad & väntar på OS 

# 2. Virtualisering av processorn II

- Throughput vs. Fairness
Effektivitet vs hur många som får köra & när de får köra.

- Turnaround time
Turnaround-time = Tcompletion - Tarrival (T = Time in ms)
Tcompletion → Är när processen är helt klar
Tarrival → Är när processen först kommer in i scheduleraren

- First Come, First Served
FIFO → First in First out
Man tar de första som kommer in och adderar de tillsammans.
Turnaroundtime = Tcompletion*N+Ncompletion/N


- Shortest Job First
Man tar de kortaste jobben först i sekvensiering & adderar de med varandra.
Turnaroundtime = Tcompletion(*N(Shortest first)/N)

- Shortest time to completion first
Man tar de kortaste jobben först som kommer in i scheduleraren.
Turnaroundtime = Tcompletion*N-Tarrival*N(Time of arrival)

- Response time
Response time = Tfirst_run-Tarrival
Alltså tiden från att de först kör minus tiden de kommer in.

- Round Robin
Delar upp jobben i lika stora delar så kallade time slice/quanta.
Turnaroundtime = Sista blocken som blir klara.
Tcompletion = Först blocken som börjar köras.

- Multi-level Feedback Queue
MLFQ:
Består utav 5 olika regler i x olika nivåer av prioritet:
1: Om jobb A > än job B → exekvera job A.
2: Om jobb A = job B → exekvera både med round robin.
3: När ett jobb kommer in i kön, placera den alltid längst upp i prioritering
4: Ett jobb får endast exekveras så länge innan den flyttas ner i prioritering
5: Efter en viss tidskvanta förflyttas alla job längst upp i prioritering 

- Proportional Share
När ett antal jobb ska köra så får de så kallade tickets utav OS. Antalet ticket bestämmer chansen för exekvering: Ex jobb A → 75 tickets & jobb B → 25 tickets. OS genererar sedan ett slumptal mellan 0-100 & talet det landar på bestämmer exekveringen. Ex: 23 → B, 82 → A osv…

- Stride Scheduling
Varje process som ska scheduleras ges ett stride värde. Jobbet med lägst stride värde är den som exekveras. Vid varje exekvering så adderas det nuvarande stride värdet mot totalen. Ex: A → stride 40 , B → stride 80, C → stride 120. När alla börjar på 0 så kör scheduleraren den först som kommer in A → A += 40, sen kör den B → B+=80, sen C → C +=120. A har lägst så den kör A sen A sen B sen C osv.

- Hard vs. Soft Real Time System
Hard: Alla deadlines utav completion måste mötas annars är det ett misslyckande.
I hard real time system så vet man om alla tasks på förhand de bsrivs i triplets:
e: the worst case execution for the task
d: the deadline, when in the future we need to finish
p: the period, how often should the task be scheduled

Soft: Deadlines kan missas men applikationen ska notifieras & ta handling.

- Idle Task
När ett jobb saknas i scheduleraren så initierar den idle task. Som är en städar-funktion OS kör för att samla ihop coalesce free list bla. Fastnar inte där då efter en viss tidskvanta OS skapar ett interrupt.

