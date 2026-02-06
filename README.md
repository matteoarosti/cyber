# JavaScript – Guida base + Esercizio finale “Profile + Quiz”

Obiettivi:
- capire come JS **modifica** la pagina (DOM)
- gestire **eventi** (click)
- mostrare/nascondere elementi e **aggiungere/rimuovere classi**
- leggere dati **JSON**
- fare chiamate **AJAX** con `fetch()` verso API pubbliche
- costruire un esercizio completo: **profilo random + quiz + punteggio**

---

## 1) JavaScript in breve

JavaScript è un linguaggio che gira nel browser e permette di rendere le pagine web interattive.

Esempio: stampare in console (strumento fondamentale per debuggare)
```js
console.log("Ciao JS!");
```

---

## 2) Variabili e tipi principali

```js
let nome = "Mario";  // variabile modificabile
const anno = 2026;   // costante

let numero = 10;     // number
let ok = true;       // boolean

let lista = [1, 2, 3]; // array
let persona = { nome: "Mario", eta: 30 }; // object
```

Note:
- `let` → riassegnabile
- `const` → non riassegnabile
- array e object sono molto usati con JSON e API

---

## 3) If, for, funzioni

```js
if (numero > 5) {
  console.log("Maggiore di 5");
} else {
  console.log("Minore o uguale a 5");
}

for (let i = 0; i < 3; i++) {
  console.log(i);
}

function somma(a, b) {
  return a + b;
}

```

---

## 4) DOM: leggere e modificare la pagina

Il DOM è la “rappresentazione in memoria” dell’HTML.

### Selezionare elementi
```js
const box = document.getElementById("boxInfo");
const btn = document.querySelector("#btnToggle");
```

### Cambiare testo o HTML
```js
box.textContent = "Testo semplice";
box.innerHTML = "<b>Testo in grassetto</b>"; // attenzione: usare con criterio
```

---

## 5) Eventi: click su un bottone

```js
btn.addEventListener("click", () => {
  console.log("Hai cliccato!");
});
```

---

## 6) Gestione grafica: mostra/nascondi + classi CSS

### 6.1 Classe CSS di base
Nel CSS definiamo una classe `.hidden`:
```css
.hidden { display: none; }
```

### 6.2 toggle() per mostrare/nascondere
```js
box.classList.toggle("hidden");
```

### 6.3 add() / remove() per aggiungere/rimuovere classi
```js
box.classList.add("hidden");     // nasconde
box.classList.remove("hidden");  // mostra
```

### 6.4 Esempio completo: “Mostra/Nascondi dettagli”
HTML:
```html
<button id="btnToggle">Mostra dettagli</button>
<div id="details" class="hidden">Contenuto dettagliato…</div>
```

JS:
```js
const btnToggle = document.getElementById("btnToggle");
const details = document.getElementById("details");

btnToggle.addEventListener("click", () => {
  const isHidden = details.classList.toggle("hidden");
  btnToggle.textContent = isHidden ? "Mostra dettagli" : "Nascondi dettagli";
});
```

---

## 7) JSON (simulato) e render in pagina

Un JSON molto comune è un **array di oggetti**:
```js
const dati = [
  { id: 1, nome: "Mario", ore: 8 },
  { id: 2, nome: "Luca", ore: 6.5 }
];
```

### Render semplice in tabella
```js
function renderTable(containerEl, rows) {
  if (!rows || rows.length === 0) {
    containerEl.innerHTML = "<i>Nessun dato</i>";
    return;
  }

  const headers = Object.keys(rows[0]);

  const thead = `<thead><tr>${
    headers.map(h => `<th>${h}</th>`).join("")
  }</tr></thead>`;

  const tbody = `<tbody>${
    rows.map(r => `<tr>${
      headers.map(h => `<td>${r[h]}</td>`).join("")
    }</tr>`).join("")
  }</tbody>`;

  containerEl.innerHTML = `<table>${thead}${tbody}</table>`;
}
```

---

## 8) AJAX moderno: fetch()

`fetch()` è il modo moderno per fare chiamate HTTP (AJAX).

```js
const resp = await fetch("https://example.com/api");
const data = await resp.json();
```

Gestione errori (buona pratica):
```js
try {
  const resp = await fetch(url);
  if (!resp.ok) throw new Error(`HTTP ${resp.status}`);
  const data = await resp.json();
} catch (e) {
  console.error(e);
}
```
