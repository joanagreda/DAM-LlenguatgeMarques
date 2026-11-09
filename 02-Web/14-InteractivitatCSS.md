# Interactivitat amb CSS

És possible canviar l’aparença d’una pàgina (per exemple, el color d’una capsa) **sense utilitzar JavaScript, fent servir només HTML i CSS**.

La idea és molt senzilla:

1. Un `<input>` de tipus *"checkbox"* o *"radio"* **guarda un estat** (marcat o no marcat).
2. El CSS **detecta aquest estat** amb `:checked`.
3. Amb `:has()` s'aplica un estil a **qualsevol altre element** segons aquell estat.

## El primer exemple

Un checkbox que, quan està marcat, posa el fons de la pàgina de color verd:

```html
<input type="checkbox" id="verd">
<label for="verd">Fons verd</label>
```

```css
body {
  background-color: white;
}

body:has(input:checked) {
  background-color: lightgreen;
}
```

Com es llegeix `body:has(input:checked)`?

- `input:checked` → un input que està marcat
- `body:has(...)` → el `<body>` **que conté** allò que hi ha dins del parèntesi

És a dir: *"el body, si conté un input marcat"*. Quan desmarquem el checkbox, la regla deixa de complir-se i el fons torna a ser blanc.

Exemple-00: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-00/index.html"

## El selector :has()

`A:has(B)` selecciona l'element **A** quan **conté** un element **B**. Es fa servir de dues maneres:

```css
/* 1. Estat global: un element de qualsevol lloc de la pàgina */
body:has(#verd:checked) .box { ... }

/* 2. Estat local: el contenidor que té l'input a dins */
label:has(:checked) { ... }
```

- Amb `body:has(...)` podem canviar **qualsevol element** de la pàgina, estigui on estigui l'input.
- Amb `contenidor:has(...)` canviem **només** l'element que conté l'input (i els seus fills). Així, **una sola regla** serveix per a molts botons.

## El label

Un `<label>` associat a un input fa que, **en clicar el text, es marqui l'input**. Hi ha dues maneres d'associar-los:

```html
<!-- Amb l'atribut "for" que apunta a l'id de l'input -->
<input type="checkbox" id="verd">
<label for="verd">Fons verd</label>

<!-- Posant l'input DINS del label (no cal "for") -->
<label><input type="checkbox"> Fons verd</label>
```

La segona manera és molt pràctica amb `:has()`, perquè el label **conté** l'input i podem fer `label:has(:checked)`.

Com que el label ja fa de botó, sovint s'amaga l'input amb `display: none;` i només es veu el label.

## Checkbox o radio

- Els *"checkbox"* són independents: se'n poden marcar diversos alhora.
- Els *"radio"* s’exclouen entre ells: **només un del grup** pot estar marcat. El grup es defineix amb el mateix atribut **name**.

<br/>
<center>
<video src="./assets/cssadv-change.mov" width="300" controls></video>
</center>

```html
<label><input type="radio" name="color" id="verd"> Color verd</label>
<label><input type="radio" name="color" id="vermell" checked> Color vermell</label>

<div class="box"></div>
```

```css
/* El botó que conté el radio marcat (una regla per a tots els botons) */
label:has(:checked) {
  background: blue;
  color: white;
}

/* La capsa és fora dels labels: cal mirar-ho des del body */
body:has(#verd:checked) .box {
  background-color: green;
}
body:has(#vermell:checked) .box {
  background-color: red;
}
```

Exemple-01: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-01/index.html"

# Botó alternador *(toggle)*

Un toggle és un checkbox amagat dins d'un label, amb un `<span>` que fa d'interruptor:

```html
<label class="toggle">
  <input type="checkbox">
  <span class="indicator"></span>
  <span>Wi-Fi</span>
</label>
```

Per canviar l'estil quan està activat, es fa servir `.toggle:has(:checked)`:

```css
.toggle input {
  display: none;
}
.toggle:has(:checked) .indicator {
  /* Estil quan està activat (ON) */
}
.toggle:has(:checked) .indicator::after {
  /* El cercle es desplaça a la dreta */
}
```

Com que cada `.toggle` només mira el seu propi checkbox, la mateixa regla serveix per a tots els toggles de la pàgina.

<br/>
<center>
<video src="./assets/cssadv-toggles.mov" width="300" controls></video>
</center>

Exemple-02: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-02/index.html"

# Capgirar *(flip)* targes

És el mateix patró que el toggle: cada targeta és un `<label>` amb un checkbox amagat a dins.

```css
.cardwrap:has(.flip:checked) .card {
  transform: rotateY(180deg);
}
```

<br/>
<center>
<video src="./assets/cssadv-flipcards.mov" width="300" controls></video>
</center>

Exemple-03: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-03/index.html"

# Carrusel CSS

<center>
<img src="./assets/cssadv-carrusel.png" style="width: 90%; max-width: 500px">
</center>

Exemple d'un carrusel fet únicament amb CSS, sense JavaScript.

Exemple-04: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-04/index.html"

## Com funciona

### 1. Cada punt de navegació conté un radio

```html
<div class="dots">
  <label class="dot"><input type="radio" name="slide" id="slide1" checked></label>
  <label class="dot"><input type="radio" name="slide" id="slide2"></label>
  ...
</div>
```

- Tots els radio tenen el mateix `name="slide"`: només un pot estar marcat.
- Els radio estan amagats amb `display: none`; només es veuen els punts.

### 2. Les fletxes també són labels

```html
<label for="slide2" class="navArrow next arrowNext1">›</label>
```

- Les fletxes són fora dels punts, per això fan servir `for` per marcar el radio corresponent.

### 3. El CSS reacciona al radio marcat

```css
/* Punt actiu: una sola regla per a tots els punts */
.dot:has(:checked) {
  background: white;
}

/* Desplaçament de les diapositives segons el radio marcat */
.carousel:has(#slide1:checked) .slides { transform: translateX(0%); }
.carousel:has(#slide2:checked) .slides { transform: translateX(-100%); }
```

- `.carousel:has(#slide2:checked)` vol dir: *"el carrusel, si conté el radio #slide2 marcat"*.
- Aleshores es desplaça el contenidor `.slides` i, amb una `transition`, les diapositives es mouen.

### Flux complet
```text
Clic al punt o a la fletxa (label)
  → Es marca el radio corresponent
    → .carousel:has(#slideX:checked) es compleix
      → S'aplica el transform adequat
        → Les diapositives es mouen amb transition
```

# Pestanyes CSS

<center>
<img src="./assets/cssadv-tabs.png" style="width: 90%; max-width: 500px">
</center>

Exemple de pestanyes fet únicament amb CSS, sense JavaScript.

Exemple-05: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-05/index.html"

## Com funcionen

### 1. Cada pestanya és un label que conté un radio
```html
<div class="tabButtons">
  <label class="tabButton"><input type="radio" name="tab" id="tab1" checked>🏠 Inici</label>
  <label class="tabButton"><input type="radio" name="tab" id="tab2">📊 Serveis</label>
  ...
</div>
```
- Totes comparteixen el mateix `name="tab"`: **només una pot estar marcada** alhora.
- Els radio estan amagats amb `display: none`, però continuen funcionant.

### 2. L'estil de la pestanya activa
```css
.tabButton:has(:checked) {
  color: #667eea;
  background: white;
  border-bottom-color: #667eea;
}
```
- *"La pestanya que conté el radio marcat"*. Una sola regla per a totes les pestanyes.

### 3. Es mostra el contingut de la pestanya activa
```css
.tab-content { display: none; }

.tabs:has(#tab1:checked) .content-1,
.tabs:has(#tab2:checked) .content-2 {
  display: block;
}
```
- El contingut és fora dels labels, per això cal mirar-ho des del contenidor `.tabs`.
- **Només es mostra el contingut** associat a la pestanya activa; la resta queda amagat.

### Flux complet
```text
Clic sobre una pestanya (label)
   → Es marca el radio que hi ha a dins
     → .tabButton:has(:checked) pinta la pestanya activa
       → .tabs:has(#tabX:checked) mostra el contingut associat
```

# Iniciar i aturar animacions CSS

<center>
<img src="./assets/cssadv-gatcoet.png" style="width: 90%; max-width: 500px">
</center>
<br/>

La propietat `animation-play-state` permet aturar (`paused`) o engegar (`running`) una animació. Amb un toggle podem controlar-la:

```css
.orbit {
  animation: rotate 12s linear infinite;
  animation-play-state: paused;
}

body:has(#animacio:checked) .orbit {
  animation-play-state: running;
}
```

Exemple-06: Obrir amb "Show preview" la pàgina "02-Web/14-InteractivitatCSS/exemple-06/index.html"
