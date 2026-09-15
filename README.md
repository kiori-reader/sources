# Kiori Sources

Repository di sorgenti firmate per Manga Reader.

L'indice pubblicato è firmato con Ed25519. L'app verifica la firma prima di
leggere qualsiasi voce del catalogo, quindi né GitHub né questo workflow
possono alterarne il contenuto senza invalidarla.

## Aggiungere il repository nell'app

Scheda **Repository** → importa `docs/kiori.mangarepo`, oppure apri il link
di importazione. In entrambi i casi l'app mostra identità e impronta e chiede
una conferma esplicita: il file **non autorizza nulla da solo**.

**Impronta della chiave (SHA-256)**

```
4eed2e23 1cc70c13 d57fd476 13ce5242 49e26b4d 7540e27f cd9c9fcc 43f81749
```

Confronta questo valore con quello mostrato dall'app prima di autorizzare.
È l'unica cosa che autentica davvero questo repository: l'impronta contenuta
nel file è ricalcolata dalla stessa chiave del file, quindi rileva la
corruzione, non la contraffazione.

| | |
|---|---|
| Identità | `kiori.sources` |
| Indice | `https://kiori-reader.github.io/sources/index.json` |
| Domini consentiti | `kiori-reader.github.io` |
| ID chiave | `kiori-repository` |

## Sorgenti pubblicate

| Sorgente | Versione | Lingue | Tipo |
|---|---|---|---|
| MangaDex | 0.4.0 | en | dichiarativa, schema v4 |

MangaDex espone un'API pubblica e documentata. Le sorgenti qui pubblicate si
limitano a provider i cui termini permettono a un client di terze parti di
leggerli; l'app non include alcun catalogo, e questo repository non è l'unico
possibile — chiunque può pubblicarne uno e firmarlo con la propria chiave.

## Aggiungere una sorgente

Servono **due chiavi distinte**: quella del repository firma l'indice, quella
dell'editore firma i pacchetti. L'indice fa da garante per la chiave pubblica
dell'editore, ma non può falsificare la firma di un pacchetto — è questa
separazione a fare in modo che chi ospita il catalogo non possa, da solo,
alterare il contenuto di una sorgente.

Nessuna chiave privata **si trova in questo repository** e non deve mai
entrarci: `.gitignore` rifiuta `*.key`. Tienila fuori dal controllo di
versione e conservala al sicuro — perderla significa non poter più aggiornare
il repository, e le app dovranno autorizzare una chiave nuova.

```sh
# 1. crea il pacchetto della sorgente
source-validator package-create manifest.json source.json publisher.key \
    build/nuova-sorgente.mangasource --icon icon.png --filters filters.json

# 2. aggiungi la voce all'indice e rifirma
source-validator repository-update docs/index.json \
    build/nuova-sorgente.mangasource PUBLISHER_PUBLIC_KEY_BASE64 \
    https://kiori-reader.github.io/sources/packages/nuova-sorgente.mangasource \
    kiori.sources kiori-repository /percorso/della/chiave-privata.key \
    docs/index.json

# 3. verifica prima di pubblicare
source-validator repository-verify docs/index.json kiori.sources \
    kiori-repository GAwXlSitaJ19hn/lQWDvjFC5o1vXQ4/Ltw6daq8ia4A=
```

Il pacchetto va copiato in `docs/packages/` e il push su `main` lo pubblica
tramite GitHub Pages (sorgente: branch `main`, cartella `/docs`).

## Struttura

```
docs/index.json        indice firmato (servito da Pages)
docs/kiori.mangarepo   descrittore di importazione
docs/packages/         pacchetti .mangasource
index.source.json        indice non firmato, sorgente per la rigenerazione
```
