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
