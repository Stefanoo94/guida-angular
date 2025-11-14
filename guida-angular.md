# Guida completa ad Angular

Versione: 1.0  
Autore: Stefanoo94

## Scopo
Questa guida copre i concetti e i comandi pratici per iniziare e lavorare professionalmente con Angular: creazione progetto, struttura, componenti, moduli, servizi, dependency injection, routing, form, gestione stato, testing, build e deploy. Contiene esempi di codice e i comandi principali.

---

## Prerequisiti
- Node.js LTS (consigliato) e npm o yarn
- Angular CLI (ng)
- Git
- Conoscenza base di TypeScript, HTML e CSS

Installare Angular CLI:
```bash
npm install -g @angular/cli
# oppure
yarn global add @angular/cli
```

---

## 1. Creare un nuovo progetto
```bash
ng new nome-app
# rispondi alle domande (routing: yes/ no, stylesheet: scss/css)
cd nome-app
ng serve --open
```
Con Vite attualmente non è lo standard: usare Angular CLI per un'app Angular completa.

---

## 2. Struttura consigliata
Struttura modulare e scalabile:
```
/src
  /app
    /core        # servizi singleton, interceptors, auth
    /shared      # componenti, pipe, directive riusabili
      /components
      /pipes
      /directives
    /features    # feature modules (es. /dashboard, /users)
    /environments
    app.module.ts
    app-routing.module.ts
  assets/
  index.html
angular.json
tsconfig.json
```

---

## 3. Componenti, template e style
Creare componente:
```bash
ng generate component shared/components/header
# shorthand
ng g c shared/components/header
```
Esempio base:
```ts
// src/app/shared/components/counter/counter.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <div>
      <p>Count: {{ count }}</p>
      <button (click)="increment()">Incrementa</button>
    </div>
  `
})
export class CounterComponent {
  count = 0;
  increment() { this.count++; }
}
```

---

## 4. Moduli e Lazy Loading
Modularizza con feature modules:
```ts
// app-routing.module.ts
const routes: Routes = [
  { path: '', loadChildren: () => import('./features/home/home.module').then(m=>m.HomeModule) },
  { path: 'admin', loadChildren: () => import('./features/admin/admin.module').then(m=>m.AdminModule) }
];
```
Lazy loading migliora startup e performance.

---

## 5. Servizi e Dependency Injection
Creare un servizio:
```bash
ng g s services/api
```
Esempio servizio con HttpClient:
```ts
// src/app/services/api.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ApiService {
  constructor(private http: HttpClient) {}
  getUsers(): Observable<User[]> { return this.http.get<User[]>('/api/users'); }
}
```
Iniettalo nel componente via costruttore.

---

## 6. Routing avanzato e guards
- Route guards: CanActivate, CanLoad
- Resolvers per pre-caricare dati
Esempio guard:
```bash
ng g guard auth
```

---

## 7. Forms (Template-driven e Reactive)
Reactive forms (consigliato per app complesse):
```bash
ng g c features/login
```
Esempio reactive form:
```ts
import { FormBuilder, Validators } from '@angular/forms';
loginForm = this.fb.group({
  email: ['', [Validators.required, Validators.email]],
  password: ['', Validators.required]
});
constructor(private fb: FormBuilder) {}
```

---

## 8. HttpClient e gestione errori
Usa HttpClient e interceptors:
```bash
ng g interceptor core/interceptors/auth
```
Esempio interceptor per token:
```ts
intercept(req, next) {
  const token = this.authService.getToken();
  const authReq = req.clone({ setHeaders: { Authorization: `Bearer ${token}` }});
  return next.handle(authReq);
}
```

---

## 9. Stato globale (NgRx / alternative)
- Per app enterprise usare NgRx (store, effects, entity)
  ```bash
  ng add @ngrx/store@latest
  ng add @ngrx/effects
  ```
- Alternative leggere: Akita, Zustand-like libs, Signals (ove supportato)
- Inizia con pattern facades e feature stores per separare UI da logica.

---

## 10. Pipes e Directives
- Pipes personalizzate per format (es. date, currency, custom)
- Directives per comportamenti riusabili (es. tooltip, lazy-load image)
- Creare una pipe:
```bash
ng g pipe shared/pipes/capitalize
```

---

(Truncated rest to keep payload reasonable)