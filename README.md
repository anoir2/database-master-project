# Esame Database

## Testo d'esame
### Prefazione
Si desidera automatizzare il sistema di gestione delle carte fedeltà.
Le specifiche del sistema, acquisite attraverso intervista, sono quelle nel seguito riportate.
Analizzare tali specifiche, filtrare le ambiguità presenti e poi raggrupparle in modo omogeneo. 
Individuare i collegamenti esistenti tra i vari gruppi di specifiche così ottenuti.
Realizzare la progettazione del modello concettuale e rappresentare le specifiche (dopo la fase di riorganizzazione) con uno schema del modello Entità-Relazione. Effettuare la progettazione logica della base di dati del sistema informativo e una sua implementazione sia nel modello relazionale che in un modello non relazionale basato sulla gestione dei documenti.
### Specifiche
Si consideri una base di dati che contiene informazioni sugli acquisti dei clienti abituali di unsupermercato.
Dei clienti interessano il codice fiscale, che li identifica, il nome, il reddito, il sesso, l’anno di nascita e l’indirizzo di residenza completo di CAP, città, provincia e regione.
Di ogni spesa di un cliente interessano il numero dello scontrino, che la identifica, la data, il totale della spesa, la modalità del pagamento (carta, bancomat, contanti) e, per ogni prodotto, la quantità, il prezzo pagato e l’eventuale sconto praticato (prodotto in promozione).
Di ogni prodotto interessano il codice, che lo identifica, la descrizione, la categoria, il costo unitario e il prezzo di vendita. I prodotti possono essere interessati da promozioni, con riduzione temporanea del prezzo, a partire da una certa data e per un numero prefissato di giorni.
### Estrazioni richieste
Scrive le interrogazioni SQL che permettono di determinare:
1. Per uno specifico cliente, determinare le quantità di ciascun prodotto acquistato in un determinato periodo;
2. Per un determinato prodotto, determinare il numero dei clienti distinti che lo hanno acquistato in un determinato periodo;
3. Individuare tutti i clienti che hanno acquistato un prodotto in promozione, indicandone il CAP di residenza;
4. Per uno specifico CAP, individuare i clienti che hanno fatto acquisti in un determinato periodo;
5. Per uno specifico CAP, individuare i clienti che non hanno fatto acquisti in un determinato periodo;
6. Per ciascun prodotto in promozione determinarne la quantità venduta in un determinato periodo;
7. I clienti che non hanno mai pagato con bancomat o carta di credito;
8. Il cliente che ha speso di più in un determinato periodo;
9. Per uno specifico CAP la spesa media per scontrino in un determinato periodo;
10. Per ciascuna categoria di prodotti il totale del prezzo pagato per i prodotti venduti in un determinato periodo.

Scrivere le interrogazioni precedenti nel linguaggio d’interrogazione per il DBMS MongoDB.

## Analisi

### Assunzioni
Dal testo dato, si sono fatte le seguenti assunzioni:
* Un prodotto appartiene ad una sola categoria
* Un cliente può registrarsi al supermercato prima di effettuare la spesa
* Una o più promozioni di un unico prodotto non possono sovrapporsi temporalmente
* I clienti risiedono in Italia
* Per semplicità, le chiavi primarie vengono popolate con tipo intero auto increment
* La citazione dell'eventuale sconto per ogni prodotto all'interno della spesa è rimossa poiché ridondante con un altro attributo
### Glossario dei termini

Qui di seguito è presente il glossario dei termini utilizzati nelle specifiche.

| Termine    | Descrizione                                                                                                                                                                            | Sinonimi | Collegamenti      |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ----------------- |
| Cliente    | Una persona che acquista i prodotti dal supermercato                                                                                                                                   | N/A      | Spesa             |
| Spesa      | Un insieme di prodotti comprati da un cliente                                                                                                                                          | N/A      | Cliente, Prodotto |
| Pagamento  | Il pagamento di una spesa. Può essere effettuato con diverse modalità                                                                                                                  | N/A      | Spesa             |
| Prodotto   | Tipo di prodotto presente all'interno del supermercato. Il supermercato ha uno o più tipi di prodotti                                                                                  | N/A      | Spesa             |
| Categoria  | Una categoria è un insieme che descrive le qualità di uno o più prodotti                                                                                                               | NA       | Prodotto          |
| Promozione | Uno o più prodotti possono essere interessati da promozioni che potrebbero portare ad avere un prezzo ridotto per un lasso temporale scandito da una data di inizio e una data di fine | N/A      | Prodotto          |

### Frasi omogenee
**Frasi relative ai clienti**: dei clienti interessano il Codice Fiscale, che li identifica, il reddito, il sesso, l’anno di nascita e l’indirizzo di residenza compreso di CAP, città, provincia e regione.

**Frasi relative alla spesa**: di ogni spesa interessano il numero dello scontrino che la identifica, la data, il totale della spesa, la modalità di pagamento (carta, bancomat, contanti).

**Frasi relative al prodotto**: di ogni prodotto interessano il codice, che lo identifica, la descrizione, la categoria, il costo unitario e il prezzo di vendita.

**Frasi relative alle promozioni di un prodotto**: I prodotti possono essere interessati da promozioni, con riduzione temporanea del prezzo, a partire da una certa data e per un numero prefissato di giorni


### Modello ER
#### Prima versione
![](https://i.imgur.com/AfdiSxW.png)


In prima istanza, si sono individuate le seguenti entità:
* **Cliente**: identifica tutti i clienti che acquistano i prodotti all'interno del supermercato. Un cliente ha una serie di dati identificativi. L'entità viene identificata univocamente con il `Codice Fiscale (CF)`.
L'entità presenta la specializzazione *Uomo* o *Donna* per identificare il sesso del cliente. 
  Quest'ultime potrebbero convergere in un unico attributo che può assumere solamente i valori (*M*, *F*).
  
  Un **Cliente** può *acquistare* una o più **Spese**.
* **Spesa**: rappresenta l'insieme dei prodotti acquistati da un determinato cliente in una certa data. Ogni spesa possiede uno scontrino con un identificativo univoco, la data di acquisto ed è associata univocamente ad un unico pagamento. L'entità viene identificata univocamente con il `Numero scontrino (NScontrino)`. 
  
  Una **Spesa** *include* uno o più **Prodotti** e ogni Prodotto all'interno della spesa ha una sua quantità e il prezzo totale per quel prodotto fatturato all'interno della spesa. 	
  
  Una **Spesa** è *fatturata* da un unico **Pagamento**.
  
  Una **Spesa** è *acquistata* da un **Cliente**.
* **Pagamento**: identifica il pagamento di una spesa. Un pagamento ha un identificativo univoco e il totale. L'entità presenta le specializzazioni che identificato il tipo di pagamento. Quest'ultime potrebbero convergere in un unico attributo che può assumere solamente i valori (*carta*, *bancomat*, *contanti*). L'entità viene identificata univocamente con `ID`. 

  Un **Pagamento** *fattura* una **Spesa**.
* **Prodotto**: identifica tutti i prodotti disponibili all'interno del supermercato. Un prodotto è identificato da un codice univoco e ha un costo unitario, il prezzo di vendita e una descrizione. L'entità viene identificata univocamente con `Codice`. 
Poiché il prodotto può essere in sconto in alcuni periodi, l'entità presenta presenta due specializzazioni:
    * *Costo normale*: rappresenta un prodotto in condizioni normali di vendita, senza alcuna promozione applicata.
    * *In promozione*: rappresenta un prodotto con una promozione in corso. Un prodotto di promozione ha come attributo lo sconto applicato al prezzo di vendita, una data di inizio e una data di fine della promozione.
    
    Le specializzazioni potrebbero essere ristrutturate introducendo una nuova entità **Promozione** che includerà lo sconto e la data di inizio e fine di una promozione.

  Un **Prodotto** è *contenuto* da una **Categoria**. 

  Un **Prodotto** può essere *incluso* ad una o più **Spese**.
* **Categoria**: questa entità identifica univocamente le categorie di prodotti presenti all'interno del supermercato. Una categoria presenta un identificativo univoco, un nome e una descrizione. L'entità viene identificata univocamente con `ID`. 

  Una **Categoria** *contiene* uno o più **Prodotti**.

#### Ristrutturato e normalizzato
![](https://i.imgur.com/Y41OL4M.png)

Dopo aver ristrutturato e normalizzato lo schema ER, avremo le seguenti entità:
* **Regione**: identifica tutte le regioni delle varie provincie presenti nella base dati. L'entità viene identificata univocamente da `ID`
Una **Regione** ha *situate* una o più **Provincie**.
* **Provincia**: identifica tutte le provincie delle varie città presenti nella base dati. L'entità viene identificata univocamente da `Codice`
  
  Una **Provincia** ha *locate* una o più **Città**.
  
  Una **Provincia** è *situata* in una **Regione**.
* **Città**: identifica tutte le città in cui possono abitare i clienti presenti nella base dati. L'entità viene identificata univocamente da `ID`.
  
  Una **Città** è *locata* in una **Provincia**.
  
  Una **Città** può essere *abitata* da uno o più **Clienti**.
* **Cliente**: identifica tutti i clienti che acquistano i prodotti all'interno del supermercato. Un cliente ha una serie di dati identificativi. L'entità viene identificata univocamente con il `Codice Fiscale (CF)`.
L'entità presenta la specializzazione *Uomo* o *Donna* per identificare il sesso del cliente. L'attributo **sesso** può assumere solamente i valori (*M*, *F*).
  
  Un **Cliente** può *acquistare* una o più **Spese**.
  
  Un **Cliente** *abita* in una **Città**.
* **Spesa**: rappresenta l'insieme dei prodotti acquistati da un determinato cliente in una certa data. Ogni spesa possiede uno scontrino con un identificativo univoco, la data di acquisto ed è associata univocamente ad un unico pagamento. L'entità viene identificata univocamente con il `Numero scontrino (NScontrino)`. 
  
  Una **Spesa** *include* uno o più **Prodotti** e ogni Prodotto all'interno della spesa ha una sua quantità e il prezzo totale per quel prodotto fatturato all'interno della spesa. 
  
  Una **Spesa** è *fatturata* da un unico **Pagamento**.
  
  Una **Spesa** è *acquistata* da un **Cliente**.
* **Pagamento**: identifica il pagamento di una spesa. Un pagamento ha un identificativo univoco e il totale. L'attributo **tipo** che può assumere solamente i valori (*carta*, *bancomat*, *contanti*). L'entità viene identificata univocamente con `ID`. 
  
  Un **Pagamento** *fattura* una **Spesa**.
* **Prodotto**: identifica tutti i prodotti disponibili all'interno del supermercato. Un prodotto è identificato da un codice univoco e ha un costo unitario, il prezzo di vendita e una descrizione. L'entità viene identificata univocamente con `Codice`. 
  
  Un **Prodotto** è *contenuto* da una **Categoria**. 
  
  Un **Prodotto** può essere *incluso* ad una o più **Spese**.
  
  Un **Prodotto** può *appartenere* ad una o più **Promozioni**.
* **Promozione**: l'entità identifica le promozioni di uno specifico prodotto. 
Idealmente, tra promozione e prodotto dovrebbe essere presente una relazione molti a molti dove una promozione è *appartiene* ad uno o più prodotti e un prodotto può *appartenere* ad uno o più promozioni. Tuttavia questa gestione delle promozioni manca di praticità reale nella fruizione della base di dati.
Ho considerato il caso, come esempio illustrativo, dei prodotti in scadenza. Per poter mettere in promozione un determinato prodotto, dovrei creare una specifica promozione prima e, poi, associarla al prodotto. Così facendo, avremo alla lunga la tabella promozioni piena di record creati ad-hoc per la singola promozione e l'eventuale associazione nella tabella di join. Questo renderebbe la fruibilità della base di dati molto rigida.
In ottica di un sistema reale (pur rispettando i requisiti richiesti) senza complicare troppo le logiche, 
le promozioni sono identificate univocamente dal prodotto, la data di inizio e di fine promozione. L'entità viene identificata univocamente da una chiave composta da `Prodotto` e `DataInizio`. Questo renderebbe la fruibilità della base di dati più flessibile poiché l'aggiunta di una promozione ad un prodotto non richiederebbe prima la creazione di una promozione ad-hoc accettando come trade-off un po' di duplicazione.
  
  Ad una **Promozione** *appartiene* un **Prodotto**.  
* **Categoria**: questa entità identifica univocamente le categorie di prodotti presenti all'interno del supermercato. Una categoria presenta un identificativo univoco, un nome e una descrizione. L'entità viene identificata univocamente con `ID`. 
  
  Una **Categoria** *contiene* uno o più **Prodotti**.
### Tavola dei volumi


| Concetto   | Tipo      | Volume |
| ---------- | --------- | ------ |
| Regione    | Entità    | 20     |
| Provincia  | Entità    | 107    |
| Città      | Entità    | 8000   |
| Cliente    | Entità    | 1000   |
| Spesa      | Entità    | 70000  |
| Pagamento  | Entità    | 70000  |
| Include    | Relazione | 350000 |
| Prodotto   | Entità    | 2000   |
| Promozione | Entità    | 5000   |
| Categoria  | Entità    | 20     |

### Modello Logico
**Cliente**(<ins>CF</ins>, Nome, Anno_Nascita, Sesso, Reddito, CAP, Via, Civico, *Citta*)

**Spesa**(<ins>N_Scontrino</ins>, Data, *Cliente*, *Pagamento*)

**Pagamento**(<ins>ID</ins>, Totale, Tipo)

**Spesa_Include_Prodotto**(<ins>*Spesa*</ins>, <ins>*Prodotto*</ins>, Prezzo, Quantita)

**Prodotto**(<ins>Codice</ins>, Descrizione, Costo_Unitario, Prezzo, *Categoria*)

**Promozione**(<ins>*Prodotto*</ins>, <ins>Data_Inizio</ins>, Data_Fine, Sconto)

**Categoria**(<ins>ID</ins>, Nome, Descrizione)

**Città**(<ins>ID</ins>, Nome, *Provincia*)

**Provincia**(<ins>Codice</ins>, Nome, *Regione*)

**Regione**(<ins>ID</ins>, Nome)

### Modello Fisico
#### SQL

##### Categoria
```
CREATE TABLE `categoria` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `nome` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  `descrizione` varchar(1000) COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

##### Pagamento
```
CREATE TABLE `pagamento` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `totale` double NOT NULL,
  `tipo` enum('carta','bancomat','contanti') COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Regione

```
CREATE TABLE `regione` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `nome` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Prodotto

```
CREATE TABLE `prodotto` (
  `codice` int(11) NOT NULL AUTO_INCREMENT,
  `descrizione` varchar(1000) COLLATE utf8_unicode_ci NOT NULL,
  `costo_unitario` double NOT NULL,
  `prezzo` double NOT NULL,
  `categoria` int(11) NOT NULL,
  PRIMARY KEY (`codice`),
  KEY `categoria_FK` (`categoria`),
  CONSTRAINT `categoria_FK` FOREIGN KEY (`categoria`) REFERENCES `categoria` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Promozione

```
CREATE TABLE `promozione` (
  `data_inizio` date NOT NULL,
  `data_fine` date NOT NULL,
  `sconto` int(11) NOT NULL,
  `prodotto` int(11) NOT NULL,
  PRIMARY KEY (`prodotto`,`data_inizio`),
  CONSTRAINT `prodotto_FK` FOREIGN KEY (`prodotto`) REFERENCES `prodotto` (`codice`),
  CONSTRAINT chk_sconto CHECK (sconto > 0 AND sconto < 100),
  CONSTRAINT chk_data CHECK (data_fine > data_inizio)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Provincia

```
CREATE TABLE `provincia` (
  `codice` char(2) COLLATE utf8_unicode_ci NOT NULL,
  `nome` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  `regione` int(11) NOT NULL,
  PRIMARY KEY (`codice`),
  KEY `regione_FK` (`regione`),
  CONSTRAINT `regione_FK` FOREIGN KEY (`regione`) REFERENCES `regione` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Citta
```
CREATE TABLE `citta` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `nome` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  `provincia` char(2) COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`id`),
  KEY `provincia_FK` (`provincia`),
  CONSTRAINT `provincia_FK` FOREIGN KEY (`provincia`) REFERENCES `provincia` (`codice`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Cliente

```
CREATE TABLE `cliente` (
  `cf` char(16) COLLATE utf8_unicode_ci NOT NULL,
  `nome` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  `anno_nascita` datetime NOT NULL,
  `reddito` int(11) NOT NULL,
  `CAP` int(11) NOT NULL,
  `via` varchar(128) COLLATE utf8_unicode_ci NOT NULL,
  `civico` int(11) NOT NULL,
  `citta` int(11) NOT NULL,
  `sesso` enum('M','F') COLLATE utf8_unicode_ci DEFAULT NULL,
  PRIMARY KEY (`cf`),
  KEY `citta_FK` (`citta`),
  CONSTRAINT `citta_FK` FOREIGN KEY (`citta`) REFERENCES `citta` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Spesa

```
CREATE TABLE `spesa` (
  `n_scontrino` int(11) NOT NULL AUTO_INCREMENT,
  `data` datetime NOT NULL,
  `pagamento` int(11) NOT NULL,
  `cliente` char(16) COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`n_scontrino`),
  KEY `cliente_FK` (`cliente`),
  KEY `pagamento_FK` (`pagamento`),
  CONSTRAINT `cliente_FK` FOREIGN KEY (`cliente`) REFERENCES `cliente` (`cf`),
  CONSTRAINT `pagamento_FK` FOREIGN KEY (`pagamento`) REFERENCES `pagamento` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
##### Spesa_include_Prodotto

```
CREATE TABLE `spesa_include_prodotto` (
  `spesa` int(11) NOT NULL,
  `prodotto` int(11) NOT NULL,
  `prezzo` double NOT NULL,
  `quantita` int(11) NOT NULL,
  PRIMARY KEY (`spesa`,`prodotto`),
  KEY `prodotto_FK_` (`prodotto`),
  CONSTRAINT `prodotto_FK_` FOREIGN KEY (`prodotto`) REFERENCES `prodotto` (`codice`),
  CONSTRAINT `spesa_FK` FOREIGN KEY (`spesa`) REFERENCES `spesa` (`n_scontrino`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```

#### NoSQL
Di seguito sono presenti le strutture utilizzate per il Database NoSQL. La struttura delle collection differisce da quella delle tabelle SQL per sfruttare al meglio le proprietà di NoSQL.
##### Cliente
###### Esempio
```
{
    "nome": "Mario Rossi",
    "anno_nascita": "1975-05-04 00:00:00",
    "sesso": "M",
    "reddito": 200421,
    "CAP": 20085,
    "via": "Via gasperi",
    "civico": 119,
    "citta": "Catania",
    "provincia": "CT",
    "regione": "Sicilia"
}
```
###### Schema
```
{
  "fields": [
    {
      "name": "_id",
      "path": "_id",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "_id",
        }
      ],
    },
    {
      "name": "anno_nascita",
      "path": "anno_nascita",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "anno_nascita",
        }
      ],
    },
    {
      "name": "CAP",
      "path": "CAP",
      "types": [
        {
          "name": "Int32",
          "bsonType": "Int32",
          "path": "CAP",
        }
      ],
    },
    {
      "name": "citta",
      "path": "citta",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "citta",
          "values": [
            "Palermo"
          ],
        }
      ],
    },
    {
      "name": "civico",
      "path": "civico",
      "types": [
        {
          "name": "Int32",
          "bsonType": "Int32",
          "path": "civico",
        }
      ],
    },
    {
      "name": "nome",
      "path": "nome",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "nome"

        }
      ],
    },
    {
      "name": "provincia",
      "path": "provincia",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "provincia",
        }
      ],
    },
    {
      "name": "reddito",
      "path": "reddito",
      "types": [
        {
          "name": "Int32",
          "bsonType": "Int32",
          "path": "reddito",
        }
      ],
    },
    {
      "name": "regione",
      "path": "regione",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "regione",
        }
      ],
    },
    {
      "name": "sesso",
      "path": "sesso",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "sesso",
      ],
    },
    {
      "name": "via",
      "path": "via",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "via",
        }
      ],
    }
  ],
}
```

##### Spesa
###### Esempio
```
{
    "data": {
        "$date": "2020-02-05T00:00:00.000Z"
    },
    "cliente": "VTLB4CB4GEI92KW5",
    "totale": 3.2949999570846558,
    "tipo_pagamento": "contanti",
    "composizione_spesa": [{
        "prodotto": 1,
        "prezzo": 0.6299999952316284,
        "quantita": 1
    }, {
        "prodotto": 5,
        "prezzo": 0.5400000214576721,
        "quantita": 1
    }, {
        "prodotto": 6,
        "prezzo": 1.4249999523162842,
        "quantita": 1
    }, {
        "prodotto": 7,
        "prezzo": 0.699999988079071,
        "quantita": 1
    }]
}
```
###### Schema
```
{
  "fields": [
    {
      "name": "_id",
      "path": "_id",
      "types": [
        {
          "name": "Int32",
          "bsonType": "Int32",
          "path": "_id",
        }
      ],
    },
    {
      "name": "cliente",
      "path": "cliente",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "cliente",
        }
      ],
    },
    {
      "name": "composizione_spesa",
      "path": "composizione_spesa",
      "types": [
        {
          "name": "Array",
          "bsonType": "Array",
          "path": "composizione_spesa",
          "types": [
            {
              "name": "Document",
              "bsonType": "Document",
              "path": "composizione_spesa",
              "fields": [
                {
                  "name": "prezzo",
                  "path": "composizione_spesa.prezzo",
                  "types": [
                    {
                      "name": "Double",
                      "bsonType": "Double",
                      "path": "composizione_spesa.prezzo",
                    }
                  ],
                },
                {
                  "name": "prodotto",
                  "path": "composizione_spesa.prodotto",
                  "types": [
                    {
                      "name": "Int32",
                      "bsonType": "Int32",
                      "path": "composizione_spesa.prodotto",
                    }
                  ],
                },
                {
                  "name": "quantita",
                  "path": "composizione_spesa.quantita",
                  "types": [
                    {
                      "name": "Int32",
                      "bsonType": "Int32",
                      "path": "composizione_spesa.quantita",
                    }
                  ],
                }
              ],
            }
          ],
        }
      ],
    },
    {
      "name": "data",
      "path": "data",
      "types": [
        {
          "name": "Date",
          "bsonType": "Date",
          "path": "data",
        }
      ],
    },
    {
      "name": "tipo_pagamento",
      "path": "tipo_pagamento",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "tipo_pagamento",
        }
      ]
    },
    {
      "name": "totale",
      "path": "totale",
      "types": [
        {
          "name": "Double",
          "bsonType": "Double",
          "path": "totale",
        }
      ]
    }
  ],
}
```

##### Prodotto
###### Esempio
```
{
    "descrizione": "Biscotto Doria",
    "costo_unitario": 0.4,
    "prezzo": 0.8,
    "categoria": "biscotti",
    "promozione": [{
        "data_inizio": {
            "$date": "2020-03-01T00:00:00.000Z"
        },
        "data_fine": {
            "$date": "2020-05-05T23:59:59.999Z"
        },
        "sconto": 10
    }]
}
```
###### Schema
```
{
  "fields": [
    {
      "name": "_id",
      "path": "_id",
      "types": [
        {
          "name": "Int32",
          "bsonType": "Int32",
          "path": "_id",
        }
      ],
    },
    {
      "name": "categoria",
      "path": "categoria",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "categoria",
        }
      ],
    },
    {
      "name": "costo_unitario",
      "path": "costo_unitario",
      "types": [
        {
          "name": "Double",
          "bsonType": "Double",
          "path": "costo_unitario",
        },
      ],
      "type": ["Double"],
    },
    {
      "name": "descrizione",
      "path": "descrizione",
      "types": [
        {
          "name": "String",
          "bsonType": "String",
          "path": "descrizione",
        }
      ],
    },
    {
      "name": "prezzo",
      "path": "prezzo",
      "types": [
        {
          "name": "Double",
          "bsonType": "Double",
          "path": "prezzo",
        },
      ],
      "type": ["Double"],
    },
    {
      "name": "promozione",
      "path": "promozione",
      "types": [
        {
          "name": "Array",
          "bsonType": "Array",
          "path": "promozione",
          "types": [
            {
              "name": "Document",
              "bsonType": "Document",
              "path": "promozione",
              "fields": [
                {
                  "name": "data_fine",
                  "path": "promozione.data_fine",
                  "types": [
                    {
                      "name": "Date",
                      "bsonType": "Date",
                      "path": "promozione.data_fine",
                    }
                  ],
                },
                {
                  "name": "data_inizio",
                  "path": "promozione.data_inizio",
                  "types": [
                    {
                      "name": "Date",
                      "bsonType": "Date",
                      "path": "promozione.data_inizio"
                    }
                  ],
                },
                {
                  "name": "sconto",
                  "path": "promozione.sconto",
                  "types": [
                    {
                      "name": "Int32",
                      "bsonType": "Int32",
                      "path": "promozione.sconto"
                    }
                  ],
                }
              ]
            }
          ],
        }
      ],
    }
  ]
}
```
### Operazioni
#### SQL

Le operazioni sono consultabili ed eseguibili nel seguente Jupyter notebook: https://colab.research.google.com/github/anoir2/database-master-project/blob/main/db_sql.ipynb
#### NoSQL

Le operazioni sono consultabili ed eseguibili nel seguente Jupyter notebook: https://colab.research.google.com/github/anoir2/database-master-project/blob/main/db_no_sql.ipynb

