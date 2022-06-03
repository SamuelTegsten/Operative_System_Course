32 bitar lock - (kö) 0xXXXXXXXX
Linux OS - Modulärt, hårdvaran i centrala kärnan med Kernal som kommunikation. Resterande programvara i C (rutiner), inga grafiska gränssnitt. → Alla kommandon har korta direktiv. Bra prestation, inga options vid 1969. → Automatisera processer (scripts/batch).

Windows → Vt box med Linux inbyggt.
Eller via bellard i en web-browser som har linux cmd.

Mnemonics -flaggor argument
Prompt vart man är i filsystemet
whoami - användare
users - multipla användare i systemet
who - 
w
ls - list directory content
man - listning på flaggor
ls -l long - filer på varje rad
ls -a all - visar alla gömda filer
d - directory (beskrivande inte flagga)
.. → folder ovanför
. → dold fil
cd → change directory
cd .. flytta upp
cd ~ hemfolder
cd / rootfolder
cd/dir/dir/.. absolut given adress
cd dir/dir/.. där ifrån man är
pwd - path working directory → hemfolder
mkdir - make directory
mkdir lab  → skapar en folder lab i den folder man är i
mkdir /lab → skapar under root
rmdir → remove directory (tar bort allt utan specificera vart!!!)
cat → cat “filname” → visar filnamnet på fönstret
wc → word count: räknar rader + ord + bytes + “filnamn” 
VI → editor för unix cmd, ordbehandlare
Emacs → editor för unix cmd, ordbehandlare
# UNIX COMMANDS

cp → copy (cp källa + namn)
mv → move (för att byta namn på en fil, (gamla filen + nya filen))
rm → remove (ta bort en fil (“namn”))
rwxr - read write execute read
chmod → change mode__
chmod u = rwx, g=rx, o=r
u → user, g → group, o → other
sudo → super user do “command” → returneras som en vanlig användare
password → rotlösenordet
stdin - standard input → 0 (filID) indata
stdout - standard output → 1 (filID) utdata
stderr - standard error → 2 (filID) felmedelande
<<|>> ändra fil
who > hamnar i filen users
who >> users (läggs till i slutet av filen)
wc < tar indata från filen users
who | wc ta utputten från who och styr det till wc (filflöde via pipe)
head - [-<lines>] “file” första 10 raderna i filen
tail - [-<lines>] “file” sista 10 raderna i filen
more eller less
grep ‘reg.exp’ filename visar alla rader i filen med match för uttrycket.
0 – 1 flera förekomster

Filsystem API 
alias för den fysiska filen → LINUX håller en räknare över länkar (referenser) riktigt refernes till filen. Alias är en path till vart den är. I LINUX kan man ha flera hardlinks till filer. När den först är 0 så finns inga fler.
Om man raderar en fil så ska den inte kunna nås på annat sätt.
ps - f process status!
Man får se den process som användaren äger, som root får man se alla.
ctrl c/z slår av en process.
Processer → skripts skapas i cmd och kör en stund, men cmd ska tillbaka.
& → processen körs i bakgrunde.
kill pid (processID) kill -9 → slår ihjäl utan säkerhet
Trådar kan termineras men måste ha kvar sitt returvärdet tills den lämnat tillbaka det till huvudtråden. 
zombies → processer som är döda men inte kan termineras.
init → skapar process som kan skapa processer.
init = reboot → tar bort processen
Man skapar trådar tyst
housekeeping → skrivarkö, disken, städa i virt minne…
Deamon → process som bistår och går förevigt i bakgrunden.

c → som skapar processer + init flera trådar.__
pid_t fork(void); skapar en ny process. (c-kod)
fork → skapar en ny PCB & kopierar hela minnesutrymmet för funktionen i ett nytt minnesutrymme. 
Returnerar den 0 → är det barnet
1 → är det föräldern och ett id för barnet
Negativt tal → kan inte skapa en ny process
wait, (waitpid)
wait NULL → vänta här tills alla underprocesser är klara
exec(vp)() → execute with parameters
__ Kör ett kommando med paramentrar
