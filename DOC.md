# Docs `magicWysywig`

Prosty edytor WYSIWYG `What You See Is What You Get` napisany przy użyciu HTML, CSS (Bootstrap) i jQuery. Pozwala na edycję i formatowanie tekstu w polu tekstowym oraz podgląd formatowanego wyniku w czasie rzeczywistym.

# Opis działania kodu

Kod składa się z:

## Sekcji HTML:

### Nagłówek (<head>), który zawiera:
* Linki do Bootstrap 5, 
* Bootstrap Icons, 
* jQuery 3.6.0 
* oraz dodatkowy styl CSS.
 
### Body, która zawiera:

#### Przyciski do formatowania tekstu:
* pogrubienie, 
* akapit, 
* listy, 

#### Pola edycji i podglądu
* Pole tekstowe `<textarea>`, gdzie użytkownik wpisuje tekst.
* Podgląd `<div id="preview">`, który dynamicznie pokazuje sformatowany tekst.

### CSS:
Prosta klasa .full-height, która ustawia minimalną wysokość dla pól edycji i podglądu.

### jQuery:

* Obsługuje dynamiczne formatowanie tekstu. 
* Reaguje na kliknięcia przycisków i zmiany w polu tekstowym.
* Tworzy podgląd formatowanego tekstu w czasie rzeczywistym.

# Szczegółowy opis metod `jQuery`

## autoLink(text) - Automatyczne wykrywanie linków

Ta funkcja znajduje wszystkie linki w tekście i automatycznie zamienia je na znaczniki `<a href="URL">URL</a>`, co pozwala kliknąć na nie w podglądzie.

```js
function autoLink(text) {
    var urlPattern = /(https?:\/\/[^\s<]+)/g;
    return text.replace(urlPattern, '<a href="$1" target="_blank">$1</a>');
}
```

## $('#content').on('input', function() {...}) - Dynamiczny podgląd

Nasłuchuje zmian w polu tekstowym (`<textarea>`) i:
* Zamienia \n na <br> (przekształca nowe linie na HTML).
* Automatycznie wykrywa linki i formatuje je.
* Wstawia sformatowany tekst do #preview.

```js
$('#content').on('input', function() {
    var content = $(this).val();
    content = autoLink(content).replace(/\n/g, '<br>');
    $('#preview').html('<span style="font-family:Verdana; font-size:12pt; color:#444444;">' + content + '</span>');
});
```

## Usuwanie HTML z zaznaczonego tekstu (stripHtmlTags)

Tworzy ukryty element `<div>`, wstawia do niego tekst z HTML, a następnie pobiera go jako czysty tekst bez znaczników HTML.

```js
function stripHtmlTags(html) {
    var div = document.createElement("div");
    div.innerHTML = html;
    return div.innerText || div.textContent;
}
```

## #clearHtmlBtn - Usunięcie formatowania HTML

Po kliknięciu usuwa HTML ze zaznaczonego fragmentu tekstu w `<textarea>`.

```js
$('#clearHtmlBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;

    if (start !== end) {
        var text = $('#content').val();
        var before = text.substring(0, start);
        var selected = stripHtmlTags(text.substring(start, end));
        var after = text.substring(end);

        $('#content').val(before + selected + after);
        $('#content').trigger('input');
    }
});
```

## #pBtn - Wstawianie akapitu

Owijanie zaznaczonego tekstu w `<span>` o stylu akapitu `<span style="font-size:12pt; font-family:Verdana; color:#444444;">`

```js
$('#pBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;
    if (start !== end) {
        var text = $('#content').val();
        var before = text.substring(0, start);
        var selected = stripHtmlTags(text.substring(start, end));
        var after = text.substring(end);

        $('#content').val(before + `<span style="font-size:12pt; font-family:Verdana; color:#444444;">${selected}</span>` + after);
        $('#content').trigger('input');
    }
});
```

## #headerBtn - Nagłówek

Konwertuje zaznaczony tekst w nagłówek poprzez dodanie `<span style="font-size:16pt; font-family:Verdana; color:#444444; font-weight:bold;">` z dużą czcionką i pogrubieniem.

```js
$('#headerBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;
    if (start !== end) {
        var text = $('#content').val();
        var before = text.substring(0, start);
        var selected = stripHtmlTags(text.substring(start, end));
        var after = text.substring(end);

        $('#content').val(before + `<span style="font-size:16pt; font-family:Verdana; color:#444444; font-weight:bold;">${selected}</span>` + after);
        $('#content').trigger('input');
    }
});
```

## #boldBtn - Pogrubienie

Obejmuje zaznaczony tekst w `<b>` (pogrubienie).

```js
$('#boldBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;
    if (start !== end) {
        var text = $('#content').val();
        var before = text.substring(0, start);
        var selected = text.substring(start, end);
        var after = text.substring(end);

        $('#content').val(before + '<b>' + selected + '</b>' + after);
        $('#content').trigger('input');
    }
});
```

## #linkBtn - Dodanie linku

Po zaznaczeniu tekstu pyta o URL i zamienia go na klikalny link.

```js
$('#linkBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;
    if (start !== end) {
        var url = prompt("Podaj adres URL:");
        if (url) {
            var text = $('#content').val();
            var before = text.substring(0, start);
            var selected = text.substring(start, end);
            var after = text.substring(end);

            $('#content').val(before + `<a href="${url}" target="_blank">${selected}</a>` + after);
            $('#content').trigger('input');
        }
    }
});
```

## #ulBtn i #olBtn - Listy punktowane i numerowane

Zamienia zaznaczony tekst na listę (`<ul>` lub `<ol>`), gdzie każda linia staje się `<li>`.

```js
$('#ulBtn').click(function() {
    var textarea = $('#content')[0];
    var start = textarea.selectionStart;
    var end = textarea.selectionEnd;
    if (start !== end) {
        var text = $('#content').val();
        var before = text.substring(0, start);
        var selected = text.substring(start, end);
        var after = text.substring(end);

        var listItems = selected.split('\n').map(item => `<li>${item}</li>`).join('');
        var listHtml = `<ul>${listItems}</ul>`;

        $('#content').val(before + listHtml + after);
        $('#content').trigger('input');
    }
});
```

# Podsumowanie

Ten kod przekształca zwykłe `<textarea>` w dynamiczny edytor tekstu, dodając formatowanie za pomocą `jQuery`. 
Wykorzystuje zdarzenia `click` i `input`, aby modyfikować treść w czasie rzeczywistym.