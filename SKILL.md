---
name: santanna-pisa
description: Monitor job postings, admissions, courses, and research opportunities from Scuola Superiore Sant'Anna di Pisa
version: 1.0.0
author: Baroni Fabio
license: MIT
metadata:
  hermes:
    tags: [university, italy, jobs, education, monitoring, santanna, pisa]
    related_skills: [blogwatcher, duckduckgo-search]
prerequisites:
  commands: []
---

# Sant'Anna Pisa - Monitoraggio Bandi e Corsi

Monitora le pagine del sito della Scuola Superiore Sant'Anna di Pisa per bandi, concorsi, selezioni, corsi e opportunita' formative/lavorative.

**Sito:** https://www.santannapisa.it/it
**Nessuna API key o dipendenza richiesta** — usa `browser_navigate` e `browser_snapshot`

## Pagine chiave

### LAVORO / SELEZIONI
| Pagina | URL | Cosa contiene |
|--------|-----|---------------|
| Hub concorsi | `/it/ateneo/concorsi-selezioni-e-gare` | Hub centrale con tutte le categorie (lavoro + formazione) |
| Selezioni attive | `/it/cerca-selezioni?status=current` | Bandi lavorativi attivi |
| Selezioni in scadenza | `/it/cerca-selezioni?status=expiring` | Bandi lavorativi prossimi alla scadenza |
| Archivio selezioni | `/it/cerca-selezioni?status=expired` | Bandi scaduti |

### CORSI / FORMAZIONE
| Pagina | URL | Cosa contiene |
|--------|-----|---------------|
| Entrare al Sant'Anna | `/it/entrare-al-sant-anna` | **HUB principale** — scadenze concorsi ammissione |
| Concorso I livello | `/it/concorso-di-primo-livello-e-ciclo-unico` | Bando, date prove, scadenze triennale |
| Concorso II livello | `/it/concorso-di-secondo-livello` | Bando, date prove, scadenze magistrale |
| Bando PhD | `/it/formazione/bando-phd` | **Bando PhD corrente** con scadenze e posizioni |
| PhD overview | `/it/formazione/phd` | Lista tutti i PhD, link a bando e FAQ |
| Master I livello | `/it/master-i-livello` | Lista Master I livello |
| Master II livello | `/it/master-ii-livello` | Lista Master II livello |
| Seasonal School | `/it/formazione/seasonal-school` | Corsi estivi con date |
| Come accedere Seasonal School | `/it/formazione/come-accedere-seasonal-school` | Info iscrizioni Seasonal School |

## Categorie di selezioni (filtri URL)

Aggiungi `?categories[0]=NOME_CATEGORIA` a `/it/cerca-selezioni`:

| Categoria | Filter URL | Descrizione |
|-----------|-----------|-------------|
| Personale docente | `personale_docente_e_ricercatore` | Professori e ricercatori |
| Ricercatori TD | `selezioni_ricercatori_a_temp_determinato` | RTD-A, RTD-B |
| Contratti di ricerca | `contratto_di_ricerca` | Assegni/contratti di ricerca |
| Post doc | `incarichi_post_doc` | Incarichi post-dottorato |
| Incarichi ricerca | `incarichi_di_ricerca_selezione` | Selezione diretta |
| Manifestazione interesse | `incarichi_di_ricerca_interesse` | Chiamata aperta |
| Assegni di ricerca | `assegni_di_ricerca` | Archivio assegni |
| Didattica | `incarichi_di_insegnamento` | Insegnamenti |
| Tecnico-amministrativo | `selezioni_personale_tecnico_amm` | Personale TA |
| Tecnologo | `selezioni_personale_tecnologo` | Ruoli tecnologici |
| Incarichi esterni | `incarichi_esterni` | Collaborazioni esterne |

## Come monitorare (workflow completo)

Il monitoraggio si divide in due sezioni: **SELEZIONI** (lavoro) e **CORSI** (formazione).

### SEZIONE A: SELEZIONI (LAVORO)

Dallo snapshot di `/it/cerca-selezioni`, ogni bando ha:
```
- article:
    - text: Data pubblicazione bando DD.MM.YYYY
    - text: ID Bando: NNN/YYYY
    - heading "Titolo" [ref=eXX] [level=3]:
      - link "Titolo" [ref=eXX]: - /url: "/it/assegni-di-ricerca-e-selezioni-incarichi-esterni/..."
    - text: Termine presentazione domanda DD.MM.YYYY
```

### SEZIONE B: CORSI (FORMAZIONE) — SCADENZE

#### Bando Concorso Ammissione I e II livello (da /it/entrare-al-sant-anna)
```
- heading "Bando del Concorso di Ammissione YYYY/YYYY"
- "Termine iscrizione domanda Concorso I livello:" → DD mese YYYY, ore HH:MM
- "Termine iscrizione domanda Concorso II livello:" → DD mese YYYY, ore HH:MM
- PDF: https://www.santannapisa.it/sites/default/files/YYYY-MM/Bando%20unico%20Concorso%20di%20Ammissione%20I%20e%20II%20livello%20YYYY-YY.pdf
```

#### Bando PhD (da /it/formazione/bando-phd)
```
- heading "Bando PhD YYYY/YYYY"
- "NN posizioni"
- Scadenza generale: "DD mese YYYY, ore HH:MM"
- Scadenza PhD Economics: "DD mese YYYY, ore HH:MM" (anticipata)
- PDF: https://www.santannapisa.it/sites/default/files/.../Bando%20concorso%20PhD%20...pdf
- Iscrizione: https://sssup.esse3.cineca.it/Home.do
```

#### Seasonal School (da /it/formazione/seasonal-school)

La pagina ha ~36 corsi in card con layout a 3 colonne. Serve scrollare per catturarli tutti.

```
- heading "Programma YYYY-YYYY"
- Per ogni corso (card):
    - heading level 3: Nome corso
    - link: /it/seasonalschool/nome-corso
    - paragraph: date (es. "March 23rd - 27th, 2026")
- Archivio programmi precedenti: 24-25, 23-24, 22-23, 21-22
```

Per le scadenze di iscrizione: navigare `/it/formazione/come-accedere-seasonal-school`
- Deadline per corsi invernali: tipicamente ~2 mesi prima
- Deadline per corsi primaverili: ~1-2 mesi prima
- Deadline per corsi estivi: ~1 mese prima

#### Master (da /it/master-i-livello e /it/master-ii-livello)
Ogni Master ha la sua pagina con:
- Titolo del Master
- Scadenza domanda
- Link al bando

## Monitoraggio completo (cronjob workflow)

Per un check completo, naviga in ordine:
1. `/it/cerca-selezioni?status=expiring` — selezioni lavoro in scadenza
2. `/it/cerca-selezioni?status=current` — selezioni lavoro attive
3. `/it/entrare-al-sant-anna` — scadenze concorso ammissione I e II livello
4. `/it/formazione/bando-phd` — stato bando PhD
5. `/it/formazione/seasonal-school` — corsi Seasonal School (scrollare 3-4x per catturare tutto)
6. `/it/formazione/come-accedere-seasonal-school` — scadenze Seasonal School
7. `/it/master-i-livello` — Master I livello
8. `/it/master-ii-livello` — Master II livello

## Estrazione URL completi
I link relativi vanno prefixati con `https://www.santannapisa.it`

## Note e Pitfalls
- Sito Drupal-based, contenuti server-side, nessun feed RSS
- Cookie banner presente — ignorare
- Bando PhD cambia URL ogni anno ma `/it/formazione/bando-phd` e' sempre corrente
- Bando concorso ammissione I/II livello ha URL PDF che cambia annualmente
- Confrontare sempre con la versione precedente per rilevare novita'
- Le pagine individuali dei corsi Summer School futuri (aprile-luglio) potrebbero non essere ancora pubblicate — verificare periodicamente