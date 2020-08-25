# Muster und Kennzeichen

Reguläre Ausdrücke sind Muster, die eine leistungsfähige Möglichkeit zum Suchen und Ersetzen im Text bieten.

In JavaScript sind sie sowohl über das Objekt [RegExp](mdn:js/RegExp) verfügbar als auch in Methoden von Zeichenketten integriert.

## Reguläre Ausdrücke

Ein regulärer Ausdruck (auch "regexp", oder einfach "reg") besteht aus einem *Muster* und optionalen *Kennzeichen*.

Es gibt zwei Syntaxen, die verwendet werden können, um ein Objekt für reguläre Ausdrücke zu erzeugen.

Die "lange" Syntax:

````js
regexp = new RegExp ("Muster", "Kennzeichen");
````

Und das "kurze", mit Schrägstrichen `"/"/"`":

```js
regexp = /muster/; // keine Kennzeichen
regexp = /muster/gmi; // mit den Kennzeichen g,m und i (wird demnächst behandelt)
```

Schrägstriche `muster:/.../` teilen JavaScript mit, dass wir einen regulären Ausdruck erstellen. Sie spielen die gleiche Rolle wie Anführungszeichen für Zeichenketten.

In beiden Fällen wird `regexp` eine Instanz der eingebauten Klasse `RegExp`.

Der Hauptunterschied zwischen diesen beiden Syntaxen besteht darin, dass Muster mit Schrägstrichen `/.../` es nicht erlauben, Ausdrücke einzufügen (wie String-Template-Literale mit `${...}`). Sie sind vollständig statisch.

Schrägstriche werden verwendet, wenn wir den regulären Ausdruck zum Zeitpunkt des Schreibens des Codes kennen -- und das ist die häufigste Situation. Während `neue RegExp` häufiger verwendet wird, wenn wir "on the fly" einen regulären Ausdruck aus einer dynamisch generierten Zeichenkette erzeugen müssen. Zum Beispiel:

```js
let tag = prompt ("Welchen Tag möchten Sie finden?", "h2");

let regexp = new RegExp(`<${tag}>`); // wie /<h2>/, wenn in der obigen Eingabeaufforderung mit "h2" geantwortet wurde
```

## Kennzeichen

Reguläre Ausdrücke können Flags haben, die die Suche beeinflussen.

Es gibt nur 6 davon in JavaScript:

`muster:i`
: Mit diesem Flag ist die Suche unabhängig von Groß- und Kleinschreibung: kein Unterschied zwischen `A` und `a` (siehe das Beispiel unten).

`muster:g`
: Mit diesem Flag sucht die Suche nach allen Übereinstimmungen, ohne dieses Flag -- nur die erste Übereinstimmung wird zurückgegeben.

`muster:m` 
: Mehrzeilenmodus (behandelt im Kapitel <info:regexp-mehrzeilenmodus>).

`muster:s` : Mehrzeilenmodus
: Aktiviert den "dotall"-Modus, der es einem Punkt `muster:.` erlaubt, mit einem Zeilenumbruchzeichen `\n` übereinzustimmen (behandelt im Kapitel <info:regexp-character-classes>).

`muster:u`
: Aktiviert volle Unicode-Unterstützung. Das Flag ermöglicht die korrekte Verarbeitung von Surrogatpaaren. Mehr dazu im Kapitel <info:regexp-unicode>.

`muster:y`
: "Sticky"-Modus: Suche an der genauen Position im Text (mehr dazu im Kapitel <info:regexp-sticky>)

```smart header="Colors"
Von hier an ist das Farbschema:

- regexp -- `muster:rot`
- Zeichenkette (wo wir suchen) -- `betreff:blau`
- Ergebnis -- `Übereinstimmung:grün`
```

## Searching: str.match

As mentioned previously, regular expressions are integrated with string methods.

The method `str.match(regexp)` finds all matches of `regexp` in the string `str`.

It has 3 working modes:

1. If the regular expression has flag `pattern:g`, it returns an array of all matches:
    ```js run
    let str = "We will, we will rock you";

    alert( str.match(/we/gi) ); // We,we (an array of 2 substrings that match)
    ```
    Please note that both `match:We` and `match:we` are found, because flag `pattern:i` makes the regular expression case-insensitive.

2. If there's no such flag it returns only the first match in the form of an array, with the full match at index `0` and some additional details in properties:
    ```js run
    let str = "We will, we will rock you";

    let result = str.match(/we/i); // without flag g

    alert( result[0] );     // We (1st match)
    alert( result.length ); // 1

    // Details:
    alert( result.index );  // 0 (position of the match)
    alert( result.input );  // We will, we will rock you (source string)
    ```
    The array may have other indexes, besides `0` if a part of the regular expression is enclosed in parentheses. We'll cover that in the chapter  <info:regexp-groups>.

3. And, finally, if there are no matches, `null` is returned (doesn't matter if there's flag `pattern:g` or not).

    This a very important nuance. If there are no matches, we don't receive an empty array, but instead receive `null`. Forgetting about that may lead to errors, e.g.:

    ```js run
    let matches = "JavaScript".match(/HTML/); // = null

    if (!matches.length) { // Error: Cannot read property 'length' of null
      alert("Error in the line above");
    }
    ```

    If we'd like the result to always be an array, we can write it this way:

    ```js run
    let matches = "JavaScript".match(/HTML/)*!* || []*/!*;

    if (!matches.length) {
      alert("No matches"); // now it works
    }
    ```

## Ersetzung: str.replace

Die Methode `str.replace(RegAusdrk, Ersetzung)` ersetzt Übereinstimmungen, die mit `RegAusdrk` in der Zeichenkette `str` gefunden wurden, durch `Ersetzung` (alle Übereinstimmungen, wenn es das Kennzeichen `muster:g` gibt, ansonsten nur die erste).

Zum Beispiel:

```js run
// keine Kennzeichen g
alert( "Wir werden, wir werden".replace(/we/i, "Sie") ); // Sie werden, sie werden

// mit Kennziechen g
alert( "Wir werden, wir werden".replace(/we/ig, "Sie") ); // Sie werden, sie werden
```

Das zweite Argument ist die Zeichenkette `Ersatz`. Wir können darin spezielle Zeichenkombinationen verwenden, um Fragmente der Übereinstimmung einzufügen:

| Symbole | Aktion in der `Ersetzungszeichenkette |
|--------|--------|
|`$&`| fügt die ganze Übereinstimmung ein
|<<code>$&#096;</code>| fügt einen Teil der Zeichenkette vor der Übereinstimmung ein|
|`$'`| fügt einen Teil der Zeichenkette nach der Übereinstimmung ein
|`$n`||wenn `n` eine 1-2-stellige Zahl ist, dann fügt sie den Inhalt der n-ten Klammer ein, mehr dazu im Kapitel <info:regexp-groups>|
|`$<name>`| fügt den Inhalt der Klammern mit dem angegebenen `Namen` ein, mehr dazu im Kapitel <info:regexp-groups>|
|`$$`| fügt das Zeichen `$` | ein

Ein Beispiel mit `Muster:$&`:

```js run
alert( "Ich liebe HTML".replace(/HTML/, "$& und JavaScript") ); // Ich liebe HTML und JavaScript
```

## Testen: regexp.test

Die Methode `regexp.test(str)` sucht nach mindestens einer Übereinstimmung, wenn sie gefunden wird, liefert `true` (wahr), andernfalls `false` (falsch).

```js run
let str = "Ich liebe JavaScript";
let regexp = /LIEBE/i;

alert( regexp.test(str) ); // true (wahr)
```

Später in diesem Kapitel werden wir mehr reguläre Ausdrücke studieren, durch weitere Beispiele gehen und auch andere Methoden kennen lernen.

Vollständige Informationen über die Methoden finden Sie im Artikel <info:regexp-methods>.

## Zusammenfassung

- Ein regulärer Ausdruck besteht aus einem Muster und optionalen Kennzeichen: muster:g", "muster:i", "muster:m", "muster:u", "muster:s", "muster:y".
- Ohne Kennzeichen und Sonderzeichen (die wir später untersuchen werden) ist die Suche mit einem Regexp dasselbe wie eine Teilzeichenfolgesuche.- Die Methode `str.match(regexp)` sucht nach übereinstimmenden Zeichenfolgen: alle, wenn es das Kennzeichen `pattern:g` gibt, andernfalls nur das erste.
- Die Methode `str.replace(regexp, replacement)` ersetzt gefundene Treffer, die mit `regexp` gefunden wurden, durch `replacement`: alle, wenn das Kennzeichen `pattern:g` vorhanden ist, andernfalls nur die erste.
- Die Methode `regexp.test(str)` ergibt `true`, wenn es mindestens eine Übereinstimmung gibt, andernfalls wird `false` ausgegeben.
