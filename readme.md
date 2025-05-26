# Team Performance Metrics 

Eine schlanke, rein client-seitige Web-App zur **Analyse des Teamerfolgs**:

* **Cycle Time Auswertung** - berechnet Ø-, Median- und Max-Cycle-Time Daten (inklusive Scatter-Plot je Item-Typ)
* **Throughput-Report** – zeigt, wie viele Issues pro ISO-Kalenderwoche erledigt wurden (Ø / Median inklusive).  
* **Monte-Carlo-Simulation** – schätzt auf Basis des historischen Weekly Throughputs wahlweise  
  * _Wie viele Items schaffen wir bis Datum X?_  
  * _Bis wann sind **N** Items fertig?_

> **Hinweis:** Die Berechnungen erfolgen jeweils teambasiert. 

---

## Verwendete Technologien

| Zweck                     | Technologie                                               |
|---------------------------|-----------------------------------------------------------|
| UI-Framework              | [Vue 3 (CDN Prod Build)](https://vuejs.org)               |
| Styling                   | [Tailwind CSS](https://tailwindcss.com)                   |
| Charts                    | [Chart.js 4](https://www.chartjs.org) + date-fns Adapter  |
| Icons                     | [Heroicons](https://heroicons.com)                        |
| Zeit & KW-Berechnung      | native JS (ISO-Woche-Algorithmen)                         |
| Jira-Anbindung            | Jira REST API v3 (Browser `fetch`, Session-Cookie)        |
| Code & Grafiken           | [ChatGPT](https://chatagpt.com)                           |

---

## `config.json` – Aufbau

```jsonc
{
  "jiraBaseUrl": "https://your-domain.atlassian.net",
  "weekLabel": "KW {WW}/{YYYY}",
  "teams": [
    { "name": "Team Alpha", "filterId": 12345 },
    { "name": "Team Beta",  "filterId": 67890 }
  ]
}
````

| Feld          | Beschreibung                                                           |
| ------------- | ---------------------------------------------------------------------- |
| `jiraBaseUrl` | Basis-URL der Jira-Cloud-Instanz (ohne Slash am Ende)                  |
| `weekLabel  ` | Platzhalter‐String für die KW-Anzeige ({WW} = Woche, {YYYY} = Jahr)    |
| `teams`       | Liste auswählbarer Teams                                               |
| `name`        | Anzeigename im Dropdown                                                |
| `filterId`    | **Numerische** ID eines gespeicherten Jira-Filters                     |

> **Hinweis:** Der Browser muss bei Aufruf bereits ein gültiges Jira-Cookie haben.

---

## CORS

Weil die Seite lokal oder auf einer anderen Domain läuft, blockiert der Browser standard­mäßig Cross-Origin-Requests. CORS oder Cross-Origin Resource Sharing ist in modernen Browsern standardmäßig blockiert. Um CORS temporär für die Anfragen an die JIRA-API zu aktiveren, kann ein entsprechendes [Browser Plugin](https://chromewebstore.google.com/detail/allow-cors-access-control/lhobafahddgcelffkeicbaginigeejlf?hl=de) installiert werden. 

Damit CORS vollständig für die Anfragen an die JIRA-API konfiguriert ist, müssen im CORS-Plugin folgende Einstellungen getroffen werden:

* Access-Control-Allow-Credentials: `true`
* Access-Control-Allow-Origin: `ORIGIN`
* Allow CORS whitelisted domain(s): `*.atlassian.com`

---

## Lokale Installation per einfachem Python-Webserver

1. Repository klonen oder Release-ZIP entpacken
2. In das Verzeichnis wechseln, das `index.html` enthält
3. `config.json.example` als `config.json` speichern und die Jira-URL und IDs für die Team-Filter anpassen.   
4. Webserver starten ([Python](https://winpython.github.io) ≥ 3 ist praktisch überall vorinstalliert):

```bash
# Verzeichnisinhalt über Port 8000 verfügbar machen
python -m http.server 8000 d C:\path\to\tpm\www
```

5. Browser öffnen: [`http://localhost:8000/index.html`](http://localhost:8000/index.html)
6. Jira-Tab offen lassen bzw. einloggen – dann Seite neu laden und Team auswählen.

Fertig! Die HTML-Datei ruft nun über den Jira-Cookie die REST-API ab und zeigt die entsprechenden Daten zum ausgewählten Team an.