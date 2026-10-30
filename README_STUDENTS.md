# Abstimmungsdetektiv: Ähnlich ist nicht dieselbe Initiative

Baut einen kleinen Knowledge Graph über zehn Schweizer Volksinitiativen und verbindet ihn mit einer vorbereiteten RAG-Baseline. Ihr wählt Quellen über Themen und Abstimmungsdaten aus, rankt passende Textchunks und lasst OpenAI eine quellengebundene Antwort formulieren.

Die Challenge ist unbenotet. Ihr könnt Codex oder Claude Code verwenden, eigene Fragen ausprobieren und interessante Erfolge oder Fehler mit anderen teilen.

## Voraussetzungen

- Docker Desktop unter Windows/macOS oder Docker Engine mit Compose unter Linux.
- Den vollständigen Inhalt dieses Repositories lokal auf eurem Computer.
- Internetzugang für den ersten Docker-Build und die OpenAI-Aufrufe.
- Einen im Kurs bereitgestellten OpenAI-API-Key mit Zugang zu den konfigurierten Modellen.

Python muss nicht lokal installiert sein. Alle Bibliotheken werden im Container aus `requirements.txt` installiert; die Notebook-Imports sind bereits vorgegeben.

## Starten

1. Startet Docker und öffnet ein Terminal im Stamm dieses Repositories, dort wo `compose.yaml` liegt.
2. Kopiert `.env.example` nach `.env`. PowerShell: `Copy-Item .env.example .env`; macOS/Linux: `cp .env.example .env`.
3. Öffnet `.env` im Editor und tragt den Kurs-Key bei `OPENAI_API_KEY=` ein. Die Datei bleibt lokal.
4. Startet die Umgebung:

```text
docker compose up --build
```

Beim ersten Start werden das Python-Image und die Pakete geladen. Wartet auf den Jupyter-Link im Terminal und öffnet ihn im Browser. Falls die Adresse `0.0.0.0` enthält, verwendet `localhost:8890` mit demselben Token.

Öffnet `Abstimmungsdetektiv.ipynb`, wählt **Python 3** und arbeitet die Zellen von oben nach unten durch. Bei den TODOs implementiert ihr die fehlenden Schritte. Das Notebook ist ein Starter, keine fertig gelöste Anwendung.

## Euer Arbeitsweg

1. **Baseline erkunden:** die vorbereiteten Chunks laden, Treffer ansehen und eine erste Antwort prüfen.
2. **Graph bauen:** Themen, gemeinsame Abstimmungsdaten, Initiativen, Quellenauswahlen und Chunks verbinden. Beispiele und passende Bibliotheksfunktionen sind angegeben.
3. **Manuell auswählen:** Thema und Zeitraum setzen und den Graphpfaden zu zulässigen Chunks folgen.
4. **Frage automatisch planen:** den von OpenAI erzeugten Plan vor der Ausführung prüfen. Bei Unklarheit zuerst die Rückfrage beantworten.
5. **Kombinieren und vergleichen:** innerhalb der Graphauswahl nach Ähnlichkeit suchen und die Quellen verschiedener Initiativen berücksichtigen.
6. **Sichtbar machen:** Graphübersicht und hervorgehobene Evidenzpfade ansehen. Danach eigene Fragen ausprobieren oder mit anderen austauschen.

Themen und Daten werden exakt über den Graphen ausgewählt. Nur Textchunks und Fragen werden eingebettet. Die Originaltexte der ausgewählten Treffer gehen an das Antwortmodell. Gemeinsame Themen machen zwei Initiativen nicht zu derselben Vorlage.

## API und lokale Dateien

Das Notebook nutzt OpenAI für Embeddings, Fragepläne und Antworten. Die Modelle stehen in `.env.example` und können bei Bedarf in `.env` angepasst werden. Es gibt einen einzigen Ablauf und keine Live-/Offline-Moduswahl.

Beim ersten Indexaufbau werden die vorbereiteten Chunks eingebettet. Der Cache wird unter `outputs/` gespeichert und wiederverwendet, solange Textbestand und Embedding-Modell gleich bleiben. Fragen werden separat eingebettet. Erneutes Ausführen von Plan- oder Antwortzellen sendet weitere API-Anfragen und kann Kosten verursachen. Beim Containerstart selbst gibt es keine API-Aufrufe.

Änderungen am Notebook und der Cache bleiben lokal erhalten, weil der Repositoryordner in den Container eingebunden ist. `.env`, Checkpoints und Laufzeitausgaben werden durch `.gitignore` aus Git herausgehalten.

## Quellen prüfen

`data/catalog.json` beschreibt die zehn Initiativen, Themen, Daten und Quellen. `data/chunks.jsonl` enthält die vorbereiteten Textstücke. Die vier vollständigen Originalbüchlein liegen in `data/pdfs/`; ihr könnt sie in Jupyter öffnen.

Die Originale enthalten weitere Vorlagetypen, die nicht Teil des Initiative-Index sind. Manche Initiativen teilen ein Büchlein. Beachtet Initiative, Zeitstand, PDF-Seite und Quellenrolle: Komiteeargumente sind zugeschriebene Positionen. Überlappende Chunks aus derselben Seite sind keine unabhängigen Belege. Eine gültige Quellen-ID allein beweist noch nicht, dass jede Aussage von der zitierten Stelle gestützt wird.

## Beenden und erneut starten

Beendet den laufenden Prozess im Terminal mit `Ctrl+C`, danach:

```text
docker compose down
```

Die lokalen Dateien bleiben erhalten. Für den nächsten Start genügt `docker compose up`; nach einer Änderung an `requirements.txt` erneut `docker compose up --build` verwenden.

## Häufige Probleme

- **Docker-Verbindung fehlt:** Docker Desktop vollständig starten und danach erneut versuchen.
- **Port 8890 ist belegt:** eine frühere Instanz dieser Übung stoppen.
- **Jupyter fragt nach einem Token:** den vollständigen Link aus dem laufenden Terminal verwenden.
- **API-Key fehlt oder wurde geändert:** `.env` prüfen und den Container neu starten, damit Compose den neuen Wert übernimmt.
- **API-Zugriff/Quota fehlt:** im Kurs melden; die Fehlerausgabe nicht durch Posten des Keys ergänzen.
- **`NotImplementedError` an einer TODO-Zelle:** den geforderten Graph- oder Retrievalschritt implementieren, danach die Zelle erneut ausführen.
