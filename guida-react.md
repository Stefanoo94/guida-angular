# Guida rapida a React

Versione: 1.0  
Autore: Stefanoo94

## Scopo
Questa guida fornisce i passaggi pratici per iniziare con React, organizzarne un progetto, usare hook principali, routing, gestione dello stato, testing, build e deploy. Contiene esempi pratici e comandi da eseguire.

---

## Prerequisiti
- Node.js LTS (consigliato) e npm o yarn
- Git
- Familiarità base con JavaScript (ES6+), HTML e CSS

---

## 1. Creare un nuovo progetto (consigliato: Vite)
Vite è veloce e moderno:
```bash
# con npm
npm create vite@latest nome-app -- --template react

# oppure con yarn
yarn create vite nome-app --template react
```
Alternativa (Create React App):
```bash
npx create-react-app nome-app
```

Entra nella cartella e installa dipendenze:
```bash
cd nome-app
npm install
npm run dev
```

---

## 2. Struttura consigliata
Una struttura semplice e scalabile:
```
/src
  /assets
  /components
    Header.jsx
    Footer.jsx
  /pages
    Home.jsx
    About.jsx
    Dashboard.jsx
  /hooks
    useFetch.js
  /contexts
    AuthContext.jsx
  /services
    api.js
  App.jsx
  index.jsx
/public
package.json
```

(Truncated rest to keep payload reasonable)