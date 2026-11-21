# metisitalia-registro-urne

Registro pubblico delle urne di **METIS Italia** (www.metisitalia.it): timbri
durante il voto e alla chiusura.

Ogni file qui dentro è un commit con l'ora di GitHub, che METIS Italia non può
cambiare. Il sito scrive soltanto file **nuovi**: non può modificarli né
cancellarli, e la cronologia non si può riscrivere (cancellazioni e force push
bloccati sul ramo `main`).

## A cosa serve

Se dopo un timbro qualcuno toglie schede dall'urna, o cambia l'urna dopo la
chiusura, il confronto con questo registro lo mostra. La verifica la fa il tuo
browser, fuori dal nostro sito:

**https://marcoterzolo.github.io/metisitalia-nucleo-voto/verifica.html**

(dalla pagina dei risultati della votazione: «Verifica fuori dal nostro sito»).

## Cartelle

Una cartella per votazione. Il nome è un **codice** di 24 caratteri:

```
codice = primi 24 caratteri esadecimali di SHA-256("MI-AN05-REGISTRO|" + id della votazione)
```

L'id della votazione è quello che compare nel link dei risultati. Dal codice
non si risale al link: chi sfoglia il registro non trova i risultati. La pagina
di verifica ricalcola il codice dal link che l'utente ha già.

## Timbri intermedi (durante il voto)

File `<codice>/AAAAMMGG-HHMMSS.txt`, al massimo uno ogni 15 minuti e solo se i
numeri sono cambiati. Contengono **solo numeri**, mai codici delle schede né
l'ordine di arrivo (segreto del voto):

```
registro: METIS Italia - timbro INTERMEDIO dell'urna (solo numeri)
codice: <codice>
ora_server: <data e ora ISO 8601 del sito>
schede: <schede nell'urna>
gettoni_usati: <gettoni di voto usati>
votanti: <elettori che hanno votato, online e in sala>
```

## Timbro di chiusura

File `<codice>/chiusura.txt`, scritto dopo il minuto di scrutinio:

```
registro: METIS Italia - timbro di CHIUSURA dell'urna
codice: <codice>
ora_server: <data e ora ISO 8601 del sito>
chiusura: <data e ora di fine votazione>
schede: <schede finali>
gettoni_usati: <gettoni usati>
votanti: <votanti>
firma_versione: <1 o 2>
impronta_urna: <impronta MI-URNA-v1 delle schede>
impronta_file_urna: <SHA-256 del file dell'urna scaricabile (formato MI-URNA-DATI-1)>
sigillo_lista: <SHA-256 della lista dei candidati>
impronta_chiave: <impronta della chiave pubblica della firma cieca>
```

Con meno di 5 schede l'urna non viene pubblicata (segreto del voto) e
`impronta_urna` lo dice.

## Cosa scopre e cosa no

- **Scopre** schede cancellate dopo un timbro (alla chiusura sono meno di
  quelle già timbrate) e ogni modifica dell'urna, della lista o della chiave
  dopo la chiusura (le impronte non coincidono più).
- Le modifiche delle **singole schede** le scoprono le firme cieche
  (nucleo del voto, https://github.com/marcoterzolo/metisitalia-nucleo-voto).
- **Non scopre** schede aggiunte da chi possiede anche la chiave segreta della
  firma.

Formati, calcoli e codice del voto: repository
[metisitalia-nucleo-voto](https://github.com/marcoterzolo/metisitalia-nucleo-voto).
