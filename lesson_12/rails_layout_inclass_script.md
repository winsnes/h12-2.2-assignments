Tema 17 - Økt 12 - Rails Layout Workshop
========================================

Ved å gjøre endringer i application.html.erb så påvirker vi "rammen" for alle andre views

La oss endre strukturen til siden ved å legge fil f.eks. en ny header

```erb
<body>

  <div id="header_wrapper">
    <header>
      <h1>This. Is. PORTFOLIO!</h1>
    </header>
  </div>

  <div id="content_wrapper">
    <%= yield %>
  </div>

</body>
```

Når vi nå navigerer rundt på siden så er vår nye header øverst på alle sidene

Vi kan på denne måten også legge til en footer på siden om vi ønsker det

```erb
<div id="footer_wrapper">
  <footer>
    <p>
      This is just a footer
    </p>
  </footer>
</div>
```

Nå begynner det å bli nyttig å tenke hva som er felles for veldig mange av sidene våre
Det kan være ting som header med logo og navigasjon, footer med sitemap, og kanskje en sidebar

Men spesielt pent er det jo ikke
Det må vi kunne gjøre noe med