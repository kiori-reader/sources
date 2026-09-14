# Odli3 Sources

Repository di sorgenti firmate per Manga Reader.

L'indice pubblicato è firmato con Ed25519. L'app verifica la firma prima di
leggere qualsiasi voce del catalogo, quindi né GitHub né questo workflow
possono alterarne il contenuto senza invalidarla.

## Aggiungere il repository nell'app

Scheda **Repository** → importa `docs/odli3.mangarepo`, oppure apri il link
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
| Identità | `odli3.sources` |
| Indice | `https://odli3.github.io/MangaReaderSources/index.json` |
| Domini consentiti | `odli3.github.io` |
| ID chiave | `odli3-repository` |

## Aggiungere una sorgente

La chiave privata **non si trova in questo repository** e non deve mai
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
    https://odli3.github.io/MangaReaderSources/packages/nuova-sorgente.mangasource \
    odli3.sources odli3-repository /percorso/della/chiave-privata.key \
    docs/index.json

# 3. verifica prima di pubblicare
source-validator repository-verify docs/index.json odli3.sources \
    odli3-repository GAwXlSitaJ19hn/lQWDvjFC5o1vXQ4/Ltw6daq8ia4A=
```

Il pacchetto va copiato in `docs/packages/` e il push su `main` lo pubblica
tramite GitHub Pages (sorgente: branch `main`, cartella `/docs`).

## Struttura

```
docs/index.json        indice firmato (servito da Pages)
docs/odli3.mangarepo   descrittore di importazione
docs/packages/         pacchetti .mangasource
index.source.json        indice non firmato, sorgente per la rigenerazione
```
