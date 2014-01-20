Tema 17 - Økt 12 - Rails Assets Workshop
========================================

Vi la til en header i layout-filen tidligere

Nå kan vi stilsette den på en veldig rudimentær måte

Ettersom vi nå skal stilsette det som har med layouten å gjøre, så kan vi opprette en egen .css.scss fil for dette

Lag derfor en ny fil i i mappen app/assets/stylesheets/ og kall den layouts.css.scss

Vi ønsker nå å ha full kontroll over vår CSS

Derfor sletter vi også vår scaffold.css.scss

Ettersom denne filen ligger i samme mappen som de andre .css.scss filene, så blir den med i Rails sin "asset pipeline"

Start serveren og se hvordan siden ser ut så langt

Vi skal nå fylle vår layouts.css.scss med stilene vi ønsker:

    layouts.css.scss

```sass
body {
  margin: 0;
  font-family: 'PT Serif', serif;

  h1 {
    font-family: 'PT Sans Narrow', sans-serif;
  }
}

#header_wrapper {
  background-color: rgba(128, 128, 128, .5);
  border-bottom: 2px solid rgba(64, 64, 64, .5);
  box-shadow: 0 0 10px rgba(64, 64, 64, .5);

  header {
    width: 90%;
    margin: 0 auto;
    height: 5em;

    h1 {
      font-size: 2.5em;
      line-height: 2em;
      margin: 0;
      color: #333;
      font-family: 'PT Sans', sans-serif;
    }
  }
}

#content_wrapper {
  width: 90%;
  margin: 2em auto;

  a {
    text-decoration: none;
    font-weight: 700;
    color: rgba(21, 91, 118, 1);

    &:hover {
      background-color: transparent;
      text-decoration: underline;
    }
  }
}

#footer_wrapper {
  footer {
    width: 90%;
    margin: 2em auto 0 auto;

    p {
      text-align: center;
    }
  }
}
```

    application.css

```css
@import url(http://fonts.googleapis.com/css?family=PT+Sans:400,700|PT+Sans+Narrow:400,700|PT+Serif:400,700);
```

Nå begynner vi å få litt kontroll over utseendet

Da skal vi se litt nærmere på et sentralt aspekt ved Rails igjen
