# Browser-Umgebung, Spezifikationen

Die Sprache JavaScript wurde ursprünglich für Webbrowser entwickelt.  Seitdem hat sie sich weiterentwickelt und ist zu einer Sprache mit vielen Einsatzmöglichkeiten und Plattformen geworden.

Eine Plattform kann ein Browser sein, oder ein Web-Server oder ein anderer *Host*, sogar eine intelligente Kaffeemaschine, wenn sie JavaScript ausführen kann.  Jede von ihnen bietet plattformspezifische Funktionalität.  Die JavaScript-Spezifikation nennt das eine *Host-Umgebung*.

Eine *Host-Umgebung* stellt zusätzlich zum Sprachkern eigene Objekte und Funktionen zur Verfügung.  Web-Browser bieten die Möglichkeit, Webseiten zu kontrollieren.  Node.js bietet serverseitige Funktionen usw.

Hier sehen Sie aus der Vogelperspektive, was wir haben, wenn JavaScript in einem Webbrowser ausgeführt wird:

![](windowObjects.svg)

Es gibt ein "Wurzel"-Objekt namens `Fenster`. Es hat zwei Rollen:

1. Erstens ist es ein globales Objekt für JavaScript-Code, wie im Kapitel <info:global-object> beschrieben.
2. Zweitens repräsentiert es das "Browser-Fenster" und stellt Methoden zu dessen Steuerung zur Verfügung.

Hier verwenden wir es zum Beispiel als globales Objekt:

```js run
function sagHi() {
  alert ("Hallo");
}

// Globale Funktionen sind Methoden des globalen Objekts:
fenster. sagHi();
```

Und hier benutzen wir es als Browserfenster, um die Fensterhöhe zu sehen:

```js run
alert(window.innerHeight); // innere Fensterhöhe
```

Es gibt weitere fensterspezifische Methoden und Eigenschaften, auf die wir später eingehen werden.

## DOM (Dokument-Objektmodell)

Document Object Model, kurz DOM, stellt den gesamten Seiteninhalt als Objekte dar, die modifiziert werden können.

Das "Document"-Objekt ist der wichtigste "Einstiegspunkt" zur Seite. Mit ihm können wir alles auf der Seite ändern oder erstellen.

Zum Beispiel
```js run
// ändern Sie die Hintergrundfarbe in rot
document.body.style.background = "red"; 

// ändern Sie es nach 1 Sekunde zurück
setTimeout(() => document.body.style.background = "", 1000);
```

Hier haben wir `document.body.style` verwendet, aber es gibt noch viel, viel mehr. Eigenschaften und Methoden sind in der Spezifikation beschrieben: [lebender Standard des DOM](https://dom.spec.whatwg.org).

### DOM ist nicht nur für Browser
Die DOM-Spezifikation erklärt die Struktur eines Dokuments und stellt Objekte zur Verfügung, mit denen es manipuliert werden kann.  
Es gibt auch Nicht-Browser-Instrumente, die DOM verwenden.

Beispielsweise können serverseitige Skripte, die HTML-Seiten herunterladen und verarbeiten, ebenfalls DOM verwenden. 
Sie unterstützen jedoch möglicherweise nur einen Teil der Spezifikation.


### CSSOM für das Styling
Es gibt auch eine separate Spezifikation, [CSS Objektmodell (CSSOM)](https://www.w3.org/TR/cssom-1/) für CSS-Regeln und Stylesheets, 
die erklärt, wie sie als Objekte dargestellt werden und wie sie gelesen und geschrieben werden können.

CSSOM wird zusammen mit DOM verwendet, wenn wir Stilregeln für das Dokument ändern. 
In der Praxis wird CSSOM jedoch nur selten benötigt, da wir CSS-Regeln nur selten von JavaScript aus ändern müssen 
(normalerweise fügen wir nur CSS-Klassen hinzu/entfernen und ändern nicht ihre CSS-Regeln), aber auch das ist möglich.

## BOM (Browser Object Model)

The Browser Object Model (BOM) represents additional objects provided by the browser (host environment) for working with everything except the document.

For instance:

- The [navigator](mdn:api/Window/navigator) object provides background information about the browser and the operating system. There are many properties, but the two most widely known are: `navigator.userAgent` -- about the current browser, and `navigator.platform` -- about the platform (can help to differ between Windows/Linux/Mac etc).
- The [location](mdn:api/Window/location) object allows us to read the current URL and can redirect the browser to a new one.

Here's how we can use the `location` object:

```js run
alert(location.href); // shows current URL
if (confirm("Go to Wikipedia?")) {
  location.href = "https://wikipedia.org"; // redirect the browser to another URL
}
```

Functions `alert/confirm/prompt` are also a part of BOM: they are directly not related to the document, but represent pure browser methods of communicating with the user.

```smart header="Specifications"
BOM is the part of the general [HTML specification](https://html.spec.whatwg.org).

Yes, you heard that right. The HTML spec at <https://html.spec.whatwg.org> is not only about the "HTML language" (tags, attributes), but also covers a bunch of objects, methods and browser-specific DOM extensions. That's "HTML in broad terms". Also, some parts have additional specs listed at <https://spec.whatwg.org>.
```

## Summary

Talking about standards, we have:

DOM specification
: Describes the document structure, manipulations and events, see <https://dom.spec.whatwg.org>.

CSSOM specification
: Describes stylesheets and style rules, manipulations with them and their binding to documents, see <https://www.w3.org/TR/cssom-1/>.

HTML specification
: Describes the HTML language (e.g. tags) and also the BOM (browser object model) -- various browser functions: `setTimeout`, `alert`, `location` and so on, see <https://html.spec.whatwg.org>. It takes the DOM specification and extends it with many additional properties and methods.

Additionally, some classes are described separately at <https://spec.whatwg.org/>.

Please note these links, as there's so much stuff to learn it's impossible to cover and remember everything.

When you'd like to read about a property or a method, the Mozilla manual at <https://developer.mozilla.org/en-US/search> is also a nice resource, but the corresponding spec may be better: it's more complex and longer to read, but will make your fundamental knowledge sound and complete.

To find something, it's often convenient to use an internet search "WHATWG [term]" or "MDN [term]", e.g <https://google.com?q=whatwg+localstorage>, <https://google.com?q=mdn+localstorage>.

Now we'll get down to learning DOM, because the document plays the central role in the UI.
