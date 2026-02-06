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


---

# 9) Esercizio finale – “Profile + Quiz” (API pubbliche)

## Obiettivo
Creare una pagina con:
1) **Scegli profilo** → carica un profilo random (nome/cognome + foto) da **RandomUser**  
2) Punteggio iniziale **0**  
3) **Prossima domanda** → carica una domanda da **Open Trivia DB**  
4) Mostra risposte come bottoni → click su una risposta  
5) Se corretta, aumenta “indovinate”  
6) Punteggio in formato: **3 indovinate su 5 domande**  
7) UI: pannelli mostrati/nascosti, classi corretto/sbagliato, stato loading.

API usate:
- RandomUser: `https://randomuser.me/api/`
- Open Trivia DB: `https://opentdb.com/api.php?amount=1&type=multiple`

---

## 9.1 `index.html`

> Metti **app.js** nella stessa cartella.

```html
<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Profile + Quiz</title>
  <style>
    body { font-family: system-ui, Arial; padding: 16px; }
    .row { display: flex; gap: 12px; flex-wrap: wrap; align-items: center; }
    .card { border: 1px solid #ddd; border-radius: 12px; padding: 12px; margin-top: 12px; }
    .hidden { display: none; }
    .muted { opacity: 0.7; }
    .answers { display: grid; gap: 8px; margin-top: 10px; }
    button { padding: 10px 12px; border-radius: 10px; border: 1px solid #ccc; cursor: pointer; }
    button:disabled { opacity: 0.5; cursor: not-allowed; }
    .correct { border-color: #2f9e44; }
    .wrong { border-color: #e03131; }
    img { border-radius: 12px; }
    .badge { padding: 2px 8px; border-radius: 999px; border: 1px solid #ccc; }
  </style>
</head>
<body>

  <h1>Profile + Quiz</h1>

  <div class="row">
    <button id="btnProfile">Scegli profilo</button>
    <button id="btnQuestion" disabled>Prossima domanda</button>
    <span id="loading" class="badge hidden">Loading...</span>
  </div>

  <div id="profileCard" class="card hidden">
    <h2>Profilo</h2>
    <div id="profile"></div>
  </div>

  <div id="quizCard" class="card hidden">
    <h2>Quiz</h2>
    <div id="question" class="muted">Premi “Prossima domanda”</div>
    <div id="answers" class="answers"></div>
  </div>

  <div class="card">
    <h2>Punteggio</h2>
    <p id="score">0 indovinate su 0 domande</p>
    <button id="btnReset" class="hidden">Reset quiz</button>
  </div>

  <script src="app.js"></script>
</body>
</html>
```


---

## 9.2 `app.js`

```js
// Stato del quiz
let correctCount = 0;
let totalCount = 0;
const MAX_QUESTIONS = 5;

// Elementi DOM
const btnProfile = document.getElementById("btnProfile");
const btnQuestion = document.getElementById("btnQuestion");
const btnReset = document.getElementById("btnReset");

const loadingEl = document.getElementById("loading");

const profileCard = document.getElementById("profileCard");
const profileDiv = document.getElementById("profile");

const quizCard = document.getElementById("quizCard");
const questionDiv = document.getElementById("question");
const answersDiv = document.getElementById("answers");

const scoreP = document.getElementById("score");

// Utility: mostra/nasconde un elemento
function show(el) { el.classList.remove("hidden"); }
function hide(el) { el.classList.add("hidden"); }

// Utility: abilita/disabilita pulsanti
function setDisabled(el, disabled) { el.disabled = disabled; }

// Utility: aggiorna punteggio (testo)
function updateScore() {
  scoreP.textContent = `${correctCount} indovinate su ${totalCount} domande`;
}

// Utility: decodifica entità HTML (OpenTrivia manda testo con &quot; ecc.)
function decodeHtml(html) {
  const txt = document.createElement("textarea");
  txt.innerHTML = html;
  return txt.value;
}

// Utility: shuffle risposte
function shuffle(arr) {
  return arr
    .map(x => ({ x, r: Math.random() }))
    .sort((a, b) => a.r - b.r)
    .map(o => o.x);
}

// Loading UI
function setLoading(isLoading) {
  if (isLoading) show(loadingEl); else hide(loadingEl);
  setDisabled(btnProfile, isLoading);
  setDisabled(btnQuestion, isLoading);
}

// 1) PROFILO RANDOM
btnProfile.addEventListener("click", async () => {
  setLoading(true);
  try {
    const r = await fetch("https://randomuser.me/api/");
    if (!r.ok) throw new Error(`RandomUser HTTP ${r.status}`);

    const d = await r.json();
    const u = d.results[0];

    // Mostra card (gestione grafica: show/hide)
    show(profileCard);
    show(quizCard); // sblocchiamo anche la parte quiz

    // Render profilo
    const fullName = `${u.name.first} ${u.name.last}`;
    profileDiv.innerHTML = `
      <div class="row">
        <img src="${u.picture.medium}" alt="Foto profilo" width="96" height="96" />
        <div>
          <div><b>${fullName}</b></div>
          <div class="muted">${u.location.country}</div>
        </div>
      </div>
    `;

    // Abilita “Prossima domanda”
    setDisabled(btnQuestion, false);
  } catch (e) {
    alert("Errore nel caricamento del profilo: " + e.message);
  } finally {
    setLoading(false);
  }
});

// 2) PROSSIMA DOMANDA
btnQuestion.addEventListener("click", async () => {
  if (totalCount >= MAX_QUESTIONS) return;

  setLoading(true);
  try {
    const r = await fetch("https://opentdb.com/api.php?amount=1&type=multiple");
    if (!r.ok) throw new Error(`OpenTrivia HTTP ${r.status}`);

    const d = await r.json();
    const q = d.results[0];

    totalCount++;
    updateScore();

    // Render domanda (decodifica HTML)
    questionDiv.textContent = decodeHtml(q.question);

    // Prepara risposte + shuffle
    const correct = decodeHtml(q.correct_answer);
    const incorrect = q.incorrect_answers.map(decodeHtml);
    const allAnswers = shuffle([...incorrect, correct]);

    // Pulisci risposte
    answersDiv.innerHTML = "";

    // Crea bottoni risposta
    allAnswers.forEach(ans => {
      const b = document.createElement("button");
      b.textContent = ans;

      b.addEventListener("click", () => {
        // Dopo una scelta: blocca tutte le risposte
        const btns = answersDiv.querySelectorAll("button");
        btns.forEach(x => (x.disabled = true));

        // Evidenzia corretto/sbagliato con classi CSS
        if (ans === correct) {
          correctCount++;
          b.classList.add("correct");
        } else {
          b.classList.add("wrong");
          // evidenzia anche il corretto
          btns.forEach(x => {
            if (x.textContent === correct) x.classList.add("correct");
          });
        }

        updateScore();

        // Fine quiz: mostra reset
        if (totalCount >= MAX_QUESTIONS) {
          questionDiv.textContent += "  ✅ Fine quiz!";
          show(btnReset);
          setDisabled(btnQuestion, true);
        }
      });

      answersDiv.appendChild(b);
    });
  } catch (e) {
    alert("Errore nel caricamento della domanda: " + e.message);
  } finally {
    setLoading(false);
  }
});

// 3) RESET
btnReset.addEventListener("click", () => {
  correctCount = 0;
  totalCount = 0;
  updateScore();

  questionDiv.textContent = "Premi “Prossima domanda”";
  answersDiv.innerHTML = "";

  hide(btnReset);
  setDisabled(btnQuestion, false);
});
```


