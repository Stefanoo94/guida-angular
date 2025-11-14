# Guida rapida a Vue.js

Versione: 1.0  
Autore: Stefanoo94

## Scopo
Questa guida fornisce i passaggi pratici per iniziare con Vue.js (consigliato Vue 3), creare progetti, usare Composition API e Options API, routing, gestione stato con Pinia, testing, build e deploy. Contiene esempi pratici e comandi utili.

---

## Prerequisiti
- Node.js LTS (consigliato) e npm o yarn
- Git
- Conoscenza base di JavaScript (ES6+), HTML e CSS
- (Opzionale) TypeScript se vuoi usare Vue con tipi

---

## 1. Creare un nuovo progetto (consigliato: Vite)
Vite è il modo raccomandato per nuovi progetti Vue 3:

```bash
# con npm
npm create vite@latest nome-app -- --template vue

# oppure con yar
yarn create vite nome-app --template vue

# entra nella cartella e installa
cd nome-app
npm install
npm run dev
```

(Truncated rest to keep payload reasonable)