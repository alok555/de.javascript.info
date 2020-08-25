# Muster und Flaggen

Reguläre Ausdrücke sind Muster, die eine leistungsfähige Möglichkeit zum Suchen und Ersetzen im Text bieten.

In JavaScript sind sie sowohl über das Objekt [RegExp](mdn:js/RegExp) verfügbar als auch in Methoden von Zeichenketten integriert.

## Reguläre Ausdrücke

Ein regulärer Ausdruck (auch "regexp", oder einfach "reg") besteht aus einem *Muster* und optionalen *Flags*.

Es gibt zwei Syntaxen, die verwendet werden können, um ein Objekt für reguläre Ausdrücke zu erzeugen.

Die "lange" Syntax:

````js
regexp = neue RegExp ("Muster", "Flags");
````

Und das "kurze", mit Schrägstrichen `"/"/"`":

```js
regexp = /muster/; // keine Flags
regexp = /muster/gmi; // mit den Flags g,m und i (wird demnächst behandelt)
```

Schrägstriche `muster:/.../` teilen JavaScript mit, dass wir einen regulären Ausdruck erstellen. Sie spielen die gleiche Rolle wie Anführungszeichen für Zeichenketten.

In beiden Fällen wird `regexp` eine Instanz der eingebauten Klasse `RegExp`.

Der Hauptunterschied zwischen diesen beiden Syntaxen besteht darin, dass Muster mit Schrägstrichen `/.../` es nicht erlauben, Ausdrücke einzufügen (wie String-Template-Literale mit `${...}`). Sie sind vollständig statisch.

Schrägstriche werden verwendet, wenn wir den regulären Ausdruck zum Zeitpunkt des Schreibens des Codes kennen -- und das ist die häufigste Situation. Während `neue RegExp` häufiger verwendet wird, wenn wir "on the fly" einen regulären Ausdruck aus einer dynamisch generierten Zeichenkette erzeugen müssen. Zum Beispiel:

```js
let tag = Eingabeaufforderung ("Welchen Tag möchten Sie finden?", "h2");

let regexp = new RegExp(`<${tag}>`); // wie /<h2>/, wenn in der obigen Eingabeaufforderung mit "h2" geantwortet wurde
```

## Flags

Regular expressions may have flags that affect the search.

There are only 6 of them in JavaScript:

`pattern:i`
: With this flag the search is case-insensitive: no difference between `A` and `a` (see the example below).

`pattern:g`
: With this flag the search looks for all matches, without it -- only the first match is returned.

`pattern:m`
: Multiline mode (covered in the chapter <info:regexp-multiline-mode>).

`pattern:s`
: Enables "dotall" mode, that allows a dot `pattern:.` to match newline character `\n` (covered in the chapter <info:regexp-character-classes>).

`pattern:u`
: Enables full unicode support. The flag enables correct processing of surrogate pairs. More about that in the chapter <info:regexp-unicode>.

`pattern:y`
: "Sticky" mode: searching at the exact position in the text  (covered in the chapter <info:regexp-sticky>)

```smart header="Colors"
From here on the color scheme is:

- regexp -- `pattern:red`
- string (where we search) -- `subject:blue`
- result -- `match:green`
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

## Replacing: str.replace

The method `str.replace(regexp, replacement)` replaces matches found using `regexp` in string `str` with `replacement` (all matches if there's flag `pattern:g`, otherwise, only the first one).

For instance:

```js run
// no flag g
alert( "We will, we will".replace(/we/i, "I") ); // I will, we will

// with flag g
alert( "We will, we will".replace(/we/ig, "I") ); // I will, I will
```

The second argument is the `replacement` string. We can use special character combinations in it to insert fragments of the match:

| Symbols | Action in the replacement string |
|--------|--------|
|`$&`|inserts the whole match|
|<code>$&#096;</code>|inserts a part of the string before the match|
|`$'`|inserts a part of the string after the match|
|`$n`|if `n` is a 1-2 digit number, then it inserts the contents of n-th parentheses, more about it in the chapter <info:regexp-groups>|
|`$<name>`|inserts the contents of the parentheses with the given `name`, more about it in the chapter <info:regexp-groups>|
|`$$`|inserts character `$` |

An example with `pattern:$&`:

```js run
alert( "I love HTML".replace(/HTML/, "$& and JavaScript") ); // I love HTML and JavaScript
```

## Testing: regexp.test

The method `regexp.test(str)` looks for at least one match, if found, returns `true`, otherwise `false`.

```js run
let str = "I love JavaScript";
let regexp = /LOVE/i;

alert( regexp.test(str) ); // true
```

Later in this chapter we'll study more regular expressions, walk through more examples, and also meet other methods.

Full information about the methods is given in the article <info:regexp-methods>.

## Zusammenfassung

- Ein regulärer Ausdruck besteht aus einem Muster und optionalen Flags: muster:g", "muster:i", "muster:m", "muster:u", "muster:s", "muster:y".
- Ohne Flags und Sonderzeichen (die wir später untersuchen werden) ist die Suche mit einem Regexp dasselbe wie eine Teilzeichenfolgesuche.- Die Methode `str.match(regexp)` sucht nach übereinstimmenden Zeichenfolgen: alle, wenn es das Flag `pattern:g` gibt, andernfalls nur das erste.
- Die Methode `str.replace(regexp, replacement)` ersetzt gefundene Treffer, die mit `regexp` gefunden wurden, durch `replacement`: alle, wenn das Flag `pattern:g` vorhanden ist, andernfalls nur die erste.
- Die Methode `regexp.test(str)` ergibt `true`, wenn es mindestens eine Übereinstimmung gibt, andernfalls wird `false` ausgegeben.
