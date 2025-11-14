# Guida HTML avanzata per esperti

Versione: 1.0  
Autore: Stefanoo94

## Scopo
Questa guida è pensata per sviluppatori esperti che vogliono padroneggiare HTML moderno in contesti professionali: semantica, accessibilità avanzata (a11y), performance, sicurezza, SEO avanzato, integrazione con web platform APIs, markup strutturato per dati, progressive enhancement, pattern per componenti, internazionalizzazione, testing e deployment ottimale.

---

## Indice rapido
- Principi generali e filosofia
- Semantica e struttura del documento
- Head: meta, preload, font loading, powertips SEO
- Accessibilità avanzata e gestione del focus
- Forms avanzati e UX delle interazioni
- Immagini e media responsive
- Performance: critical rendering path e ottimizzazioni
- SEO tecnico e dati strutturati (JSON‑LD, Microdata)
- Progressive Enhancement, offline e PWA
- Security: CSP, SRI, input sanitization, headers
- Web Platform APIs rilevanti
- Web Components, templates e pattern di componentizzazione
- Internazionalizzazione e localizzazione
- Testing, auditing e CI pipelines
- Best practices di deploy e headers HTTP
- Checklist rapida per produzione

---

## Principi generali e filosofia
- HTML è markup semantico: struttura e significato prima di styling.
- Progressive enhancement: fornire funzionalità di base a tutti, miglioramenti a chi supporta feature moderne.
- Separazione di responsabilità: HTML (struttura), CSS (presentazione), JS (comportamento).
- Accessibility-first: pensare l’accessibilità dal primo markup, non come rifattorizzazione finale.
- Preferire soluzioni native (form validation, dialog element, details/summary) quando possibile.

---

## Semantica e struttura del documento
- Usa i landmark HTML5: <header>, <nav>, <main>, <aside>, <footer>, <article>, <section>.
- Esempio base:
```html
<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Documento</title>
</head>
<body>
  <header role="banner">...</header>
  <nav role="navigation">...</nav>
  <main role="main">...</main>
  <footer role="contentinfo">...</footer>
</body>
</html>
```
- Non usare elementi semantici come decorazione (es. non usare <section> solo per styling).
- Titoli gerarchici coerenti: un solo <h1> per pagina (se usi multiple sections come documenti indipendenti, considera <article> con proprio h1).
- Micro-formatting: evita <div> quando esistono elementi semantici (use <figure>, <figure><figcaption>, <figure><img>).

---

## Head: meta, preload, font loading, powertips SEO
- Charset e viewport:
```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
```
- Preload critico:
  - CSS critico inlined e preload per il CSS principale:
    ```html
    <link rel="preload" href="/assets/main.css" as="style">
    <link rel="stylesheet" href="/assets/main.css">
    ```
  - Preload font:
    ```html
    <link rel="preload" href="/fonts/Inter.woff2" as="font" type="font/woff2" crossorigin>
    ```
- Prefetch & preconnect:
  - Preconnect a CDN/Font provider:
    ```html
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    ```
- Meta per SEO e social:
  - title e description unici, Open Graph e Twitter Card:
    ```html
    <meta name="description" content="Riassunto...">
    <meta property="og:title" content="Titolo">
    <meta property="og:description" content="...">
    <meta name="twitter:card" content="summary_large_image">
    ```
- Link rel="alternate" + hreflang per multi-lingua.
- Canonical per evitare duplicate content:
  ```html
  <link rel="canonical" href="https://example.com/page" />
  ```

---

## Accessibilità avanzata (a11y)
- Landmark & ARIA: usa markup semantico e ARIA sovrattutto quando non esistono elementi nativi adeguati.
- Ruoli e attributi utili:
  - role="alert", role="status", aria-live="polite" per aggiornamenti dinamici.
  - aria-hidden="true" per markup decorativo non letto dai lettori.
- Gestione del focus:
  - Quando apri un dialog/modal, muovi focus a primo elemento tabbabile, salva il focus precedente, ritorna focus al close.
  - Usa focus trap per modali.
- Dialog pattern:
  - Preferisci <dialog> (se supportato) o implementa aria-modal="true", role="dialog", aria-labelledby, aria-describedby.
- Keyboard support:
  - Tutte le interazioni devono essere accessibili via tastiera.
  - Documenta e supporta roving tabindex per listbox/menù complessi.
- Live regions per aggiornamenti dinamici:
  ```html
  <div aria-live="polite" id="notifications"></div>
  ```
- Testing a11y:
  - Strumenti: axe, pa11y, Lighthouse, NVDA/VoiceOver.
  - Test manuali: navigazione a tastiera, lettura con screen reader, contrast checks.
- Accessibility for forms:
  - Labels espliciti <label for="id"> o aria-label; error messages con aria-describedby e aria-invalid.

---

## Forms avanzati e UX
- Usa tipi di input HTML5 (email, tel, url, number, date, datetime-local) per sfruttare keyboard mobile e validazione nativa.
- Inputmode per suggerire tastiera mobile:
  ```html
  <input inputmode="numeric" pattern="[0-9]*" />
  ```
- Constraint validation API e messaggi localizzati:
  ```js
  if (!input.checkValidity()) {
    input.reportValidity();
  }
  ```
- Progressive enhancement: fallback server-side sempre attivo.
- File uploads: usa attributes accept, capture se serve mobile camera, e chunked uploads per file grandi.
- Accessible error handling:
  - Messaggi d'errore con role="alert" o aria-live e focus sul primo campo con errore.
- UX hints: placeholder non sostituisce label; usa help text con aria-describedby.

---

## Immagini e media responsive
- Use responsive images (srcset, sizes):
```html
<img src="img-640.jpg"
     srcset="img-320.jpg 320w, img-640.jpg 640w, img-1280.jpg 1280w"
     sizes="(max-width:600px) 100vw, 50vw"
     alt="Descrizione concisa">
```
- <picture> per art direction:
```html
<picture>
  <source media="(min-width:1024px)" srcset="hero-large.jpg">
  <img src="hero-small.jpg" alt="...">
</picture>
```
- Lazy loading nativo:
  - Use loading="lazy" su <img> e <iframe> per ritardare il caricamento.
- WebP/AVIF: fornire fallback generici con <picture>.
- Video: sorgenti multiple e attrib autoplay/muted/playsinline se necessario; fornire sottotitoli e descrizioni.
- Accessibility: sempre alt significativo; role="presentation" per immagini decorative e aria-hidden se necessario.

---

## Performance: critical rendering path e ottimizzazioni
- Minimizzare il numero e la dimensione dei resource blocking (CSS, fonts).
- CSS critico: inline per above-the-fold, carica il resto in modo non-blocking.
- Caricamento JS non-blocking: defer per script che manipolano DOM dopo parsing; async per script indipendenti.
- HTTP/2 & HTTP/3: sfruttare multiplexing e server push (con prudenza).
- Avoid layout thrashing: preferire transform/opacity per animazioni.
- Use resource hints: preload, preconnect, dns-prefetch.
- Fonts: strategia swap/fallback per evitare FOIT; font-display: swap in @font-face.
- Lighthouse metrics: ottimizza LCP, FID/INP, CLS — riduci JavaScript in main-thread.

---

## SEO tecnico & dati strutturati
- Meta tags, canonical, hreflang, structured data.
- JSON-LD (consigliato) per rich results:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Titolo articolo",
  "author": { "@type": "Person", "name": "Autore" },
  "datePublished": "2025-11-14",
  "image": "https://example.com/cover.jpg"
}
</script>
```
- BreadcrumbList, Product, FAQPage markup per SERP enhanced.
- Evitare duplicatione del contenuto tra varianti (www vs non-www, http vs https).
- Ensure semantic header structure and meaningful text for search engines.
- Use robots meta & X-Robots-Tag header for fine-grained crawling control.

---

## Progressive Enhancement, offline e PWA
- Service Worker: cache strategies (network-first for APIs, cache-first for static assets); Workbox semplifica patterns.
- App manifest per installabile:
```json
{
  "name": "App",
  "short_name": "App",
  "start_url": "/",
  "display": "standalone",
  "icons": [...]
}
```
- Offline fallbacks: offline page fallback for navigation requests, graceful degradation for features not available offline.
- Feature detection: use modernizr-like checks or conditional code, do not rely su user-agent sniffing.
- App Shell model: HTML base minimal + lazy-hydration per route per component per need.

---

## Security: CSP, SRI, sanitization, headers
- Content Security Policy (CSP) per ridurre XSS. Esempio restrittivo:
```
Content-Security-Policy:
  default-src 'self';
  script-src 'self' https://trusted.cdn.com 'sha256-...';
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  img-src 'self' data: https:;
  object-src 'none';
  base-uri 'self';
  form-action 'self';
```
- Subresource Integrity per CDN-hosted assets:
```html
<script src="https://cdn.example.com/lib.js"
        integrity="sha384-abc..."
        crossorigin="anonymous"></script>
```
- X-Frame-Options / CSP frame-ancestors per clickjacking.
- Referrer-Policy, Strict-Transport-Security (HSTS), Feature-Policy / Permissions-Policy.
- Server-side sanitization: non fidarsi mai dell’input client; sanifica/valida su server.
- Escape output: HTML escape in templates per prevenire XSS.
- CORS: configure server with least privileges (Access-Control-Allow-Origin only what’s needed).
- Cookies: set HttpOnly, Secure, SameSite attributes.

---

## Web Platform APIs rilevanti
- History API: pushState/replaceState per routing client-side con URL semantici.
- Fetch API + Streams: streaming responses per migliorar perceived performance.
- IntersectionObserver: lazy-loading, infinite scroll, analytics.
- ResizeObserver, MutationObserver per layout/reactivity senza polling costoso.
- WebSockets / Server-Sent Events: realtime.
- WebRTC for P2P realtime.
- Payment Request, Web Share, Clipboard API, Credential Management API (for UX ottimizzate).
- Web Animations API per animazioni ad alte prestazioni.
- OffscreenCanvas per operazioni grafiche off-main-thread.

---

## Web Components, templates e pattern
- Use <template> per markup riusabile non renderizzato fino a cloneNode.
- Shadow DOM: encapsulamento stile; attenzione a accessibilità e theming (CSS custom properties attraversano shadow boundary).
- Custom Elements:
```js
class MyCard extends HTMLElement {
  connectedCallback() { this.attachShadow({mode:'open'}); this.shadowRoot.innerHTML = `<style>...</style><slot></slot>`; }
}
customElements.define('my-card', MyCard);
```
- Hydration & SSR: se usi server-side rendering, progetta componenti per progressive hydration (islands architecture).
- Libraries: lit, SkateJS, Stencil per developer experience e tooling.

---

## Internazionalizzazione (i18n) e localizzazione (l10n)
- Use lang attribute on <html lang="it">.
- Direction support: dir="rtl" for RTL languages; use logical CSS properties.
- Date/number formatting: Intl.DateTimeFormat, Intl.NumberFormat.
- Pluralization: use server-side or libraries (Intl.PluralRules).
- SEO multi-locale: hreflang tags e sitemaps per lingue.
- Consider locale-specific content negotiation via Accept-Language or structured URL path (example.com/it/...).

---

## Testing, auditing e CI
- Automated checks:
  - a11y: axe-core, pa11y
  - Linting: HTMLHint, stylelint, eslint (JS)
  - Visual regression: Percy, BackstopJS, Chromatic
  - Performance: Lighthouse CI
- CI pipeline:
  - Run linters, unit tests, a11y scans, Lighthouse scores gating (soft/fail thresholds).
  - Produce artifacts (report PDF/HTML) on failure for review.
- Manual QA: screen reader walkthrough, keyboard-only navigation, color contrast checks.

---

## Deploy & HTTP headers (prod checklist)
- Redirects: enforce https via HSTS, canonical redirects to a single origin.
- Cache policy:
  - Immutable fingerprinted assets: Cache-Control: public, max-age=31536000, immutable
  - HTML: short cache + stale-while-revalidate
- Security headers: HSTS, X-Frame-Options, Referrer-Policy, Permissions-Policy, Content-Security-Policy
- Use CDN for static assets, enable compression (brotli/gzip), set correct Vary headers (Accept-Encoding).

---

## Best practices e anti-patterns
- Evita markup non-semantic solo per layout.
- Non usare inline event handlers (onclick) in produzione — prefer unobtrusive JS.
- Evita grandi bundle JS per semplici pagine content-driven.
- Non fidarti di librerie esterne senza controllare attività di manutenzione e vulnerabilità.
- Semplifica: preferisci API native quando possibile per performance e compatibilità.

---

## Esempio completo minimo (HTML + accessibility + performance hints)
```html
<!doctype html>
<html lang="it">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>Demo avanzato</title>
  <link rel="preload" href="/css/critical.css" as="style">
  <link rel="stylesheet" href="/css/critical.css">
  <link rel="preload" href="/fonts/Inter.woff2" as="font" type="font/woff2" crossorigin>
  <meta name="description" content="Demo avanzato di HTML, performance e accessibilità">
</head>
<body>
  <header>
    <a href="#main" class="skip-link">Vai al contenuto</a>
    <nav aria-label="Principale">...</nav>
  </header>

  <main id="main" role="main">
    <article aria-labelledby="post-title">
      <h1 id="post-title">Titolo articolo</h1>
      <p>...</p>
      <figure>
        <picture>
          <source type="image/avif" srcset="hero.avif">
          <source type="image/webp" srcset="hero.webp">
          <img loading="lazy" src="hero.jpg" alt="Descrizione">
        </picture>
        <figcaption>Didsc.</figcaption>
      </figure>
    </article>
  </main>

  <script type="module">
    // lazy hydrations / feature-detect
    if ('IntersectionObserver' in window) {
      // register lazy behaviors
    }
  </script>
</body>
</html>
```

---

## Checklist finale (quick)
- [ ] lang e charset corretti
- [ ] head: meta description, canonical, og tags
- [ ] critical CSS e preload per risorse critiche
- [ ] fonts con preload e font-display appropriato
- [ ] accessible landmarks, labels, aria attributes when needed
- [ ] keyboard navigation and focus management
- [ ] responsive images (srcset/picture) and lazy loading
- [ ] CSP & SRI configured for external resources
- [ ] SEO structured data (JSON-LD) where relevant
- [ ] PWA: manifest + service worker + offline fallback
- [ ] CI checks: lint, a11y, performance audits

---

Risorse raccomandate
- MDN Web Docs — HTML, Accessibility, Web APIs  
- Web.dev — Performance e Core Web Vitals  
- A11y Project, axe-core  
- Google Lighthouse & Lighthouse CI  
- OWASP — XSS & Content Security Policy guides

---

Vuoi che:
- generi il file guida-html.md e lo aggiunga al repo per te, oppure
- prepari anche la versione PDF (pandoc) e lo committi?  
Se preferisci che esegua le operazioni fammi sapere: genero il file nel repo o ti do i comandi per la conversione/push.