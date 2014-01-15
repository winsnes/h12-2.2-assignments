Tema 17 - Økt 8 - Git Workshop
==============================

Naviger til skrivebordet

    cd Desktop

Lag en tom mappe

    mkdir git-demo

Naviger inn i mappen

    cd git-demo

Lag en enkel readme fil

    subl README.md

    Dette er readme filen for at jeg skal få testet litt Git
    Det blir kjempespennende!
    La oss lagre denne filen

Sjekk at filen ble lagret i mappen

    ls

Da er vi klare for litt Git
Start (Initialize) et Git Repository

    git init .

    Initialized empty Git repository in /Users/...

Du kan nå sjekke status på ditt Git repository

    git status

Her vises informasjon om ditt repository slik det ser ut akkurat nå

Viktige ting å merke seg:

"On branch master"
Master er navnet på repositoriets første branch
Dette navnet er alltid standard

"Initial commit"
Indikasjon om at det ikke finnes noen tidligere commits

"Changes to be committed:"
Lister opp filene som har endret seg / blitt opprettet siden sist

Forskjellige typer endringer kan være:

- new file
- modified
- deleted

Beskrivende melding som også gir et hint on hva vi skal gjøre
Viser hvilke filer som ikke er tatt med enda så langt
La oss legge til README.md til vårt repository

    git add README.md

Sjekk så status igjen

    git status

Filen README.md står nå merket som en "new file"
Vi kan nå pakke sammen denne commiten, og skrive en beskrivelse av hva vi har gjort (siden sist commit)
Vanlig melding på første commit er "Initial commit"

    git commit -m 'Initial commit'

Tilstanden på mappen og filene er nå lagret
En ny sjekk av status er på sin plass

    git status

    # On branch master
    nothing to commit, working directory clean

Dette ser lovende ut!

La oss legge til en fil til

    subl CREDITS.md

    These here are the credits

Sjekk git status igjen

    git status

Denne gangen er det også dukket opp en ny fil
La oss legge til denne filen også

    git add CREDITS.md

    git status

Der ligger den, også merket som "new file"

Jeg kom på at jeg glemte å faktisk gi noen credits i filen, så la oss åpne den igjen

    subl CREDITS.md

Hvordan ser statusen ut nå da?

    git status

WTF??

Hvorfor står CREDITS.md nå to ganger?
Git lagrer tilstanden til filen akkurat i det øyeblikket du forrige gang skrev "git add"
Dersom du ønsker å ta med denne siste endringen i filen så må den derfor legges til igjen

    git add CREDITS.md

    git status

Se der ja, nå står den bare én gang, og på riktig sted
Nå kan vi lagre tilstanden i en commit
Men denne gangen skal vi skrive en mer utfyllende beskrivelse

    git commit

Vi får nå opp Sublime Text 2
Her kan vi skrive en mer utfyllende beskrivelse av hvilke endringer vi har gjort siden sist
Vi _må_ skrive noe, og dersom vi bare lagrer filen uten å skrive noe, så blir ikke commiten opprettet
Skriv inn en dertil egnet tekst, gjerne over flere linjer om nødvendig

    Added a credits file
    Here I will credit the various project participants

Lagre så filen og lukk den
Du kan nå sjekke git status igjen

    git status

Alright!

Kanskje vi har lyst å se litt på historikken til dette repositoriet?

    git log

Herlig!
Nå kan vi få oversikt!
Trykk 'q' for å avslutte loggvisningen

Det var noe snakk om å kunne angre på endringer
Det kan gjøres på liten eller stor skala
La oss starte med den lille

Slett credits filen

    rm CREDITS.md

Og sjekk så status

    git status

CREDITS.md står nå som "deleted"

Dersom det ikke var intensjonen, så er det nå mulig å angre på denne slettingen
Det kan blandt annet gjøres ved å hente tilbake filen fra slik den så ut i forrige commit

    git checkout CREDITS.md

    git status

Og vips, så var filen der igjen

Hva hvis du hadde slettet flere filer?

    rm CREDITS.md README.mr

    git status

Dette er et enkelt eksempel, men tenk deg at du nå hadde slettet 10-15 forskjellige filer ved en feiltakelse
Du kan enten hente tilbake én og én fil, eller du kan kjøre hele tilstanden tilbake til slik den var ved forrige commit
Denne kommandoen må du dog være veldig forsiktig med, da den resetter ALLE filer som har blitt endret eller slettet til forrige commit
(Den sletter likevel ikke nye filer som ikke var der ved siste commit)

    git reset --hard

    git status

    ls

Og der kom filene tilbake igjen

La oss gjøre et par endringer i filene våre

CREDITS.md

    These here are the credits
    Coder:
    Ken Paulsen
    Olivier Klaver
    Designer:
    Ken Paulsen

README.md

    Dette er readme filen for at jeg skal få testet litt Git

Lukk så igjen Sublime Text 2

Hmm, hvilke endringer var det nå jeg gjorde i filene?

    git status

Der står det bare at begge er endret
Kanskje jeg kan se forskjellen mellom filene i forrige commit og i nåværende tilstand?

    git diff

Der fikk vi litt informasjon ja
Her er en liste over hva som er endret og ikke lagt til i committen

    "diff --git a/CREDITS.md b/CREDITS.md"

Indikerer hvilke filer som sammenlignes nedenfor

    "--- a/CREDITS.md"
    "+++ b/CREDITS.md"

Indikerer at endringene har skjedd i overgangen mellom versjon a og b av filen CREDITS.md

Deretter vises utsnitt av linjene hvor det har skjedd endringer
(Alle endringer spores per linje)

    \ foran linjen indikerer at linjen er uendret
    + indikerer lagt til linje
    - indikerer slettet linje

Det er nå mulig å se litt mer av hvilke endringer som er gjort i filene
Dersom alle endringene er riktige, så kan vi "stage" endringene
Når man har endret flere filer, og alle endringene skal være med videre, så trenger vi ikke alltid å legge til én og én fil

    git add .

Dette stager alle nye og endrete filer
(slettede filer kan også taes med $git add -A . )

    git status

    git commit
  
    Moved around people in the credits
    Reduced readme to only necessary text

    git status

Og da var vi klare for å jobbe videre med nye endringer