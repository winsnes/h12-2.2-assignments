Tema 17 - Økt 12 - Git Commit Often Workshop
============================================

Sjekk hvilke endringer som er gjort siden sist

    git status

Her har vi tre forskjellige tilstander:

- modified
- deleted
- untracked

Det er også endringer gjort på tre forskjellige oppgaver

Hvilke ting har vi gjort i dag?

1. Lagt til header i en felles layout
2. Endret CSS
3. Satt ny root path

La oss følge samme rekkefølgen

Vi må nå legge til kun de filene som var en del av første oppgaven (layout-endringen)

    git add app/views/layouts/application.html.erb

    git status

Nå er kun vår layouts fil lagt til i denne committen

Da kan vi pakke sammen denne

    git commit -m 'Added basic structure to layout'

    git status

Da var vi ferdig med første oppgaven, gå så videre til neste (CSS)

Her både endret, slettet og opprettet vi filer

Alle filene vi har berørt ligger i app/assets/ mappestrukturen

Om vi bruker

      git add app/assets/

så vil kun den nye og de endrede filene lagres i committen

Vi kan derfor bruke et av triksene til Git for å ta med både endringer, nye filer og slettede filer med én kommando

    git add -A app/assets/

    git status

Nå kan vi se at vi har fått med alle tre tilstandene:

- modified
- new file
- deleted

Disse tre endringene var en del av samme prosessen, så vi kan nå lage en commit av dette

    git commit -m 'Started building the CSS for the layout. Also removed the default scaffold CSS.'

    git status

Da gjenstår bare den siste endringen

Ettersom det bare er én fil som er endret, og den skal være med i denne committen, så trenger vi ikke å skrive hele stien til filen

    git add .

    git commit -m 'Set the clients#index action to root for the app'

    git status

Da var vi tilbake til en "clean state"

Takk for i dag
