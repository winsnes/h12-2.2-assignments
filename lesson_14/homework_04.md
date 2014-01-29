Tema 17 - Økt 14 - Database / Rails Lekse
=========================================

## Informasjon om oppgavesett og krav

- **Antall oppgaver:** 9
- **Antall svar påkrevd:** 4

**Skriv inn svar på oppgavene i en tekstfil med navn homework_04.txt og last opp denne på It's Learning**

## 1.0 Database

1.1 Relasjonsdatabaser har mulighet for å knytte en relasjon mellom to entiteter i hver sin tabell. For å gjøre dette brukes det spesielle verdier i hver tabell. Hva heter disse to verdiene?

1.2 Det finnes en rekke mulige relasjonstyper mellom entiteter i to tabeller. Hva heter de fire vanligste?

1.3 Relasjonen mellom tabellen Client og Project innebærer at den ene tabellen selv må vite hvilken entitet i den andre tabellen den tilhører. Hvilken tabell skal dette ansvaret ligge hos, og hva vil den nødvendige egenskapen hete i dette tilfellet?

1.4 Hvilken verditype brukes i feltet som unikt identifiserer én entitet i en tabell?

## 2.0 Rails

2.1 Rails strukturerer sin applikasjonskode på et velkjent software pattern. Hva er det forkortet til, og hva står forkortelsen for?

2.2 Rails bygger på tankegangen "convention over configuration". For å gjøre det enkelt å komme i gang tilbyr Rails derfor en rekke generatorer. En av disse bygger opp den grunnleggende strukturen for CRUD-handlinger på en modell. Hvilken generator er dette?

2.3 Skriv kommandoen for å bruke generatoren fra forrige oppgave dersom du skulle opprettet den grunnleggende CRUD-funksjonaliteten for følgende modell:

Person
  - First name
  - Last name
  - Birthday
  - Sex

2.4 Etter at generatoren fra de foregående oppgavene har blitt kjørt, så er koden på plass, men applikasjonen vil likevel ikke kjøre. Hvilken feilmelding får du, hva betyr den, og hvordan kan du løse dette?

2.5 Rails generatoren gir oss en del filer, blandt annet filen scaffolds.css.scss. Hva slags filtype er dette, og hvilken annen filtype kan vi enkelt bytte til for å fylle samme behov?