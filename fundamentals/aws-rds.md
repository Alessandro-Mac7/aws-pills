
# AWS RDS
- è un **database gestito** di AWS
- Consente di creare in cloud database **Postgres, Oracle, MySQL, MariaDB, Server SQL Microsoft** E **Aurora (database proprietario di AWS)**.

I vantaggi di RDS rispetto a distribuirlo manualmente su un'instanza EC" sono molteplici: 
- è un **servizio gestito** di AWS
- patching automatico del SO e processo di configurazione di un'infrastruttura completamente affidato ad AWS.
- Backup continui e ripristino a **timestamp specifici** (ripristino point in time)
- Dashboard di monitoraggio
- **Repliche di lettura** per alte prestazioni in lettura 
- Configurazione **Multi AZ** per DR (Disaster Recovery)
- Finestre di manutenzione per gli aggiornamenti
- Capacità di **scaling** (verticale e orizzontale)

**Non è possibile accedere tramite SSH alle istanze del DB**

## Backup
- Sono abilitati di default su RDS
  - Vengono fatti **giornalmente** dei backup completi (durante le finestre di manutenzione)
  - I logs delle transazioni vengono sottoposti a backup da RDS **ogni 5 minuti**
  - possibilità di ripristinare in qualsiasi momento (dal backup più vecchio a 5 minuti fa)
  - Conservazione di 7 giorni (può essere aumentata a 35 giorni)
- **DB Snapshots**
  - eseguita dall'utente manualmente
  - non ha una durata specifica, può durare finche l'utente  non la cancella

## Repliche di lettura RDS per la scalabilità in lettura
- Fino a 5 repliche di lettura
- All'interno di AZ, Cross AZ o Cross region
- La replica è asincrona, quindi le letture sono **"eventually consistent"** (non è garantita la consistenza)

### Repliche di lettura RDS: casi d'uso
- Si dispone di un database di produzione che sta assumendo un carico normale
- Si desidera eseguire un'applicazione di reportistica per eseguire alcune analisi
- Si crea una replica di lettura per eseguire lì il nuovo carico di lavoro
- L'applicazione di produzione non è interessata
- Le repliche di lettura vengono utilizzate solo per il tipo di istruzioni SELECT (=read) (non INSERT, UPDATE, DELETE)

### Repliche di lettura RDS: costo di rete
- In AWS si ha un costo di rete quando i dati **passano da una AZ a un'altra**
- Per ridurre i costi, è consigliabile avere le repliche di lettura nella stessa zona di disponibilità

## RDS Multi AZ (Disaster Recovery)
- Replica di **SINCRONIZZAZIONE**
- Un nome DNS: failover automatico dell'app in standby
- Aumentare la disponibilità
- Failover in caso di perdita di AZ, perdita di rete, istanza o errore di archiviazione
- Nessun intervento manuale nelle app
- Non utilizzato per il ridimensionamento (solo disaster recovery)

## RDS Encryption
- Funzionalità di crittografia con crittografia AWS KMS - AES-256
- Certificati SSL per crittografare i dati in RDS
- Per applicare SSL:
  - PostgreSQL: rds.force_ssl=1 nella console AWS RDS (gruppi di parametri)
  - MySQL: GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;
- PER connettersi utilizzando SSL:
  - Fornire il certificato SSL Trust (può essere scaricato da AWS)
  - Fornire opzioni SSL durante la connessione al database

 ### Operazioni di crittografia RDS
 - Crittografia dei backup RDS:
  - Gli snapshot dei database RDS non crittografati non sono crittografati
  - Gli snapshot dei database RDS crittografati sono crittografati
  - Può essere copiato uno snapshot non crittografato in uno crittografato
- Per crittografare un database RDS non crittografato:
  - Creare uno snapshot del database non crittografato
  - Copia lo snapshot e abilita la crittografia per lo snapshot
  - Ripristina il database dallo snapshot crittografato
  - Migrare le applicazioni nel nuovo database ed eliminare il vecchio database

 ### Sicurezza RDS
- I database RDS vengono generalmente distribuiti all'interno di una sottorete privata, non in una pubblica
- RDS Security funziona sfruttando i gruppi di sicurezza (lo stesso concetto delle istanze EC2): controlla chi può comunicare con RDS
- Le policy IAM aiutano a controllare chi può gestire RDS
- Il nome utente e la password tradizionali possono essere utilizzati per accedere al database
- Possono essere utilizzati anche gli utenti IAM (solo per MySQL/Aurora)

## Aurora
- Aurora è una tecnologia proprietaria di AWS (non open source)
- Postgres e MySQL sono entrambi supportati da Aurora DB (ciò significa che Aurora funziona come se fosse un database Postgres o MySQL)
- Aurora è "ottimizzata per il cloud AWS" e dichiara miglioramenti delle prestazioni 5 volte superiori rispetto a MySQL su RDS, oltre 3 volte le prestazioni di Postgres su RDS
- Lo storage Aurora cresce automaticamente con incrementi di 10 GB, fino a 64 TB
- Aurora può avere 15 repliche mentre MySQL ne ha 5 e il processo di replica è più veloce (ritardo di replica inferiore a 10 ms)
- Il failover in Aurora è istantaneo. È nativo di HA.
- Aurora costa più di RDS (20% in più), ma è più efficiente
- Aurora supporta le repliche cross region

### Aurora Cluster
Ha due endpoint principali:
- writer endpoint: punta al master
- reader endpoint: punta al load balancing

### Aurora Security
è molto simile ad RDS in generale perche funziona sugli stessi motori, quindi:
- Crittografia dei dati tramite KMS
- Backup automatici, snapshots e le repliche sono crittografati
- per le chiamate in transito usa SSL (stessa storia di MySql e Postgres)
- è possibile autenticarsi tramite il token IAM
- la responsabilità della protezione è affidata a noi ed è gestibile tramite i Security Group
- non si può assolutamente usare SSH

### Aurora Serverless
- Istanziazione automatizzata del database e ridimensionamento automatico in base all'utilizzo effettivo
- Ottimo per carichi di lavoro poco frequenti, intermittenti o imprevedibili
- Non è necessaria alcuna pianificazione della capacità
- Paga al secondo, può essere più conveniente

### Aurora Global
- Repliche di lettura Aurora in più regioni:
  - Utile per il ripristino di emergenza
  - Semplice da posizionare
- Aurora Global Database (consigliato):
  - 1 regione primaria (lettura/scrittura)
  - Fino a 5 aree secondarie (di sola lettura), ritardo di replica inferiore a 1 secondo
  - Fino a 16 repliche di lettura per regione secondaria
  - Aiuta a ridurre la latenza
  - La promozione di un'altra regione (per il ripristino di emergenza) ha un RTO di < 1 minuto