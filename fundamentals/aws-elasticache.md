# ElastiCache

In modo analogo a RDS che consente di ottenere database relazionali gestiti, Elasticache consente di gestire Redis o Memcached (database in-memory)
- Le cache sono database in memoria con prestazioni davvero elevate e bassa latenza
- Aiutano a ridurre il carico dei database per i carichi di lavoro ad alta intensità di lettura
- Contribuisce a rendere un applicazione "stateless"
- AWS si occupa della manutenzione/applicazione, ottimizzazioni, installazione, configurazione, monitoraggio, ripristino da guasti e backup
- L'utilizzo di ElastiCache comporta pesanti modifiche al codice dell'applicazione

### DB Cache 
- Le query delle applicazioni ElastiCache, se non disponibili, ottengono da RDS e archiviano in ElastiCache.
- Aiuta ad alleviare il carico in RDS.
- La cache deve avere una strategia di invalidazione per assicurarsi che vengano utilizzati solo i dati più aggiornati.

### User Session Store
- L'utente accede a qualsiasi applicazione
- L'applicazione scrive i dati della sessione in ElastiCache
- L'utente accede a un'altra istanza della nostra applicazione
- L'istanza recupera i dati e l'utente è già connesso

### Redis vs Memcached
- REDIS
  - Multi AZ con failover automatico
  - Repliche di lettura per ridimensionare le letture e avere un'elevata disponibilità
  - Durabilità dei dati utilizzando la persistenza AOF
  - Funzionalità di backup e ripristino

- MEMCACHED
  - Multi-nodo per il partizionamento dei dati (sharding)
  - Non persistente
  - Nessun backup e ripristino
  - Architettura multithread

### Cache Security
- non supporta IAM authentication
- le policy IAM sono usate solo per AWS API-level security
- Redis AUTH:
  - è possibile settare una “password/token” quando viene creato un cluser Redis
  - è un livello di sicurezza in più (oltre ai Security group)
  - supporta l'encrypt SSL
- Memcached
  - supporta solol'autenticazione SASL-based (advanced)

###  ElastiCache Replication
- Cluster Mode Disabled:
  - Un nodo primario, fino a 5 repliche
  - Replica asincrona
  - Il nodo primario viene utilizzato per la lettura/scrittura
  - Gli altri nodi sono di sola lettura
  - Un frammento, tutti i nodi hanno tutti i dati
  - Prevenzione della perdita di dati in caso di guasto del nodo
  - Multi-AZ abilitato per impostazione predefinita per il failover
  - Utile per ridimensionare le prestazioni di lettura
- Cluster Mode Enabled:
  - I dati sono partizionati tra shard (utile ridimensionare le scritture)
  - Ogni shard ha un primario e fino a 5 replica nodi (stesso concetto di prima)
  - Funzionalità Multi-AZ
  - Fino a 500 nodi per cluster:
    - 500 shards con singolo master
    - 250 shards con 1 master e 1 replica
    - ... ecc.

### Implementazione della memorizzazione nella cache
- Lazy Loading / Cache-Aside / Lazy Population
- Pros
  - Vengono memorizzati nella cache solo i dati richiesti (la cache non viene riempita con dati inutilizzati)
  - Gli errori dei nodi non sono fatali (solo una maggiore latenza per riscaldare la cache)
- Contro
  - Penalità di mancata cache che si traduce in 3 viaggi di andata e ritorno, notevole ritardo per quella richiesta
  - Dati obsoleti: i dati possono essere aggiornati nel database e obsoleti nella cache

### Write Through
aggiungi o aggiorna la cache quando il database viene aggiornato
- Pro:
  - I dati nella cache non sono mai obsoleti, le letture sono rapide
  - Penalità di scrittura vs penalità di lettura (ogni scrittura richiede 2 chiamate)
- Contro:
  - Dati mancanti fino a quando non vengono aggiunti / aggiornati nel DB. La mitigazione consiste nell'implementare anche la strategia di caricamento lento
  - Cache churn: molti dati non verranno mai letti

### Rimozione della cache e time-to-live (TTL)
- L'eliminazione della cache può avvenire in tre modi:
  - Elimina l'elemento in modo esplicito nella cache
  - L'elemento viene rimosso perché la memoria è piena e non è stato utilizzato di recente (LRU)
  - Hai impostato la durata di un articolo (o TTL)
- I TTL sono utili per qualsiasi tipo di dati:
  - Classifiche
  - Commenti
  - Flussi di attività
- TTL può variare