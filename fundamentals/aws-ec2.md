# EC2: Elastic Compute Cloud
E’ uno dei più popolari servizi che AWS offre ed è un infrastruttura a Servizi. Con Compute intendiamo la potenza di calcolo di cui abbiamo necessità, ovvero se abbiamo un carico di lavoro piccolo necessita di poca potenza di calcolo al contrario di un carico di lavoro elevato che ci obbliga ad avere molta più potenza di calcolo. Amazon EC2 ci permette quindi di acquisire potenza di calcolo mediante il lancio di servizi virtuali chiamate **“instance”**. Quando lanciamo un instance decidiamo noi la potenza che desideriamo a seconda del nostro business. 
Con questo servizio offerto da AWS acquisiamo la capacità di:
- **Noleggiare macchine** delle macchine virtuali
- **Salvare dati** su macchine virtuali (EBS)
- **Distribuire il carico di lavoro** sulle macchine (ELB)
- **Scalare servizi** usando uno scaling automatico (ASG)
La conoscenza di questo servizio è fondamentale per capire il funzionamento del cloud.

## EC2 User Data
- è uno **script che definisce il bootstrap** del nostro servizio, **bootstrapping** significa che viene lanciato quando la macchina parte e inoltre viene eseguito per ogni lancio di istanza.
- Questo script viene usato per **automatizzare alcuni processi iniziali** come installare aggiornamenti, software, scaricare particolari file da internet e qualsiasi altra cosa.
- Questo script **può essere lanciato solo con l’utente di Root**

## EC2 Instance Type
Ci permette di definire il tipo di “virtual hardware” che deve avere la nostra istanza EC2. La scelta del tipo è condizionata dai seguenti principi:
- **Virtual CPUs**
- **Memoria**
- **Storage (dimensione e tipo)**
- **Prestazioni di rete**

I tipi sono raggruppati in classi o famiglie che ci danno una configurazione di default a seconda di quella che scegliamo e che pensiamo sia giusta per il nostro business.
- La naming convention che utilizza AWS per definire una famiglia è la seguente: m5.2xlarge ➝
  - m: intendiamo la classe di istanza
  - 5: intendiamo la generazione che aws associa alla classe di istanza
  - 2xlarge: intendiamo la dimensione dell’istanza
- **Le dimensioni scalano linearmente** e il rapporto tra **CPU e VRAM e prezzo è costante**, all’aumentare di uno dei tre aumentano tutti gli altri.
- **Le famiglie di istanze** nascono per semplificare il processo di scelta d’istanza aiutandoci a selezionare il giusto rapporto di potenza che deve avere la nostra applicazione. Le famiglie sono suddivise sostanzialmente in **5 macro-aree**.
  - **General purpose** “M” ➝ m4, m5, t1,t2, a1 etc
    - Le istanze di uso generale assicurano un equilibrio tra le risorse di calcolo, memoria e rete e possono essere utilizzate per una vasta gamma di carichi di lavoro diversi. Tali istanze sono l’ideale per le applicazioni che utilizzano queste risorse in pari proporzioni come i server web e i repository di codice.
  - **Compute Optimized** “C” ➝ c4, c5, c6 etc
    - Ideali per le cosiddette “compute bounds operation”, ovvero per quelle applicazioni che necessitano un uso intensivo della CPU e richiedono quindi alte prestazioni per operazioni del tipo (Batch processing, Media transcoding, High performance web servers, High performance computing (HPC), Scientific modeling & machine learning o Dedicated gaming servers).
  - **Memory Optimized** “R” ➝ r4, r5, r6, z1, etc
    - Ideale per applicazioni che necessitano di tanta memoria volatile e di tanta velocità carichi di lavoro che elaborano grandi set di dati in memoria. Facciamo una scelta del genere quando vogliamo efficienza e performance in:  relational/non-relational databases, cache stores, in-memory db, applicazioni che processano dati real-time.  
  - **Storage Optimized** “I” ➝ i3, d2, d3, h1, etc
    - Le istanze di storage ottimizzato sono progettate per i carichi di lavoro che richiedono prestazioni elevate di lettura e scrittura sequenziali per insiemi di dati di grandi dimensioni sullo storage locale. Sono ottimizzate per fornire alle applicazioni decine di migliaia di operazioni I/O casuali a bassa latenza al secondo (IOPS)  e che richiedono tanta memoria persistente (SSD). I casi d’uso di questa scelta potrebbero essere i seguenti: Sistemi di elaborazione delle transazioni online ad alta frequenza (OLTP), Database relazionali e NoSQL, Cache per database in memoria (ad esempio, Redis), Applicazioni di data warehousing, File system distribuiti
  - **GPU Optimized** “G” ➝ g2, g3, etc
    - Ideali per le applicazioni che richiedono delle prestazioni per eseguire determinate funzioni in modo più efficiente rispetto a un software in esecuzione su CPU, ad esempio i calcoli a virgola mobile, i criteri di ricerca dati e l'elaborazione grafica


## Amazon Machine Images (AMIs)
- Con (AMI) **definiamo il software iniziale** che un'istanza deve avere quando verrà lanciato. 
- **Un'AMI è una personalizzazione di un'istanza EC2** e definisce ogni aspetto dell'istanza come: 
  - Il Sistema Operativo (SO) e la sua configurazione
  - l’aggiunta dei nostri software/applicazioni le nostre configurazioni
  - ciò ci consente di avere un avvio più rapido poiché la nostra istanza ha già tutto dentro e non ha la necessità di installare altro. 
- Sono costruite per specifiche regioni e possono essere copiate su regioni differenti.
- Tutte le AMI sono basate su sistemi operativi x86, Linux o Windows. Esistono **quattro fonti di AMI**: 
  - **Published by AWS**
    - AWS pubblica AMI con versioni di molti sistemi operativi diversi, entrambi Linux e Windows. Questi includono più distribuzioni di Linux e Windows 2008 e Windows 2012. Con l’avvio di questo tipo di AMI (che sono quelle di default al momento di una scelta) avremo un'immagine pulita con le impostazioni di default dell'OS che scegliamo. Più o meno come installare un SO da un ISO. 
  - **AWS Marketplace**
    - E’ uno store che aiuta permette di acquistare istanze pre-configurate da altri utenti o aziende (alcune  di esse iniziano un business su questo tipo di lavoro). 
  - **Generated from Existing Instances**
    - Un'AMI può essere creata da un'AMI esistente Istanza Amazon EC2. Questa è una fonte molto comune di AMI.
  - **Uploaded Virtual Servers**
    - utilizzando il servizio di importazione/esportazione di macchine virtuali AWS, i clienti possono creare immagini da vari formati di virtualizzazione, inclusi raw, VHD, VMDK e OVA. L'elenco corrente dei sistemi operativi supportati (Linux e Windows) è disponibile nella doc AWS. Spetta ai clienti rimanere conformi alla licenza del fornitore del sistema operativo.

## EC2 Purchasing Options
- **On-Demand Instances**
  - scegliamo questa opzione di acquisto quando il carico di lavoro non è eccessivo. Si paga per quello che si utilizza (Linux e Windows fatturano al minuto, mentre altri SO fatturano all’ora).
  - Ha il costo più alto rispetto ad altre opzioni, ma non ha bisogno di pagamenti anticipati e non ha nessun impegno a lungo termine.
  - La raccomandazione di questo tipo va alle applicazioni che girano per brevi periodi.
- **Reserved Instances**
  - questa opzione necessita minimo 1 anno di utilizzo, è adatta per carichi di lavoro a lunghi periodi, carichi di lavoro lunghi ma con istanza flessibile cioè può modificare il tipo  (Convertible Reserved Instance), carichi di lavoro lunghi ma schedulati riservabili solo per un 1 anno (Scheduled Reserved Instances) es.“Ogni giovedì dalle 3 alle 6 deve girare l’istanza X”. 
  - Questo tipo di istanze sono scontate del 75% rispetto al tipo “On-Demand”
  - Bisogna pagare in anticipo per l’utilizzo che se ne deve fare per avere il massimo sconto, si può pagare parzialmente (meno sconto) o non anticipatamente (non si riceve nessuno sconto).
  - L'istanza può essere riservata da 1 a 3 anni, consigliato per applicazioni stazionarie tipo il DB.
- **Spot Instances**
  - carichi di lavoro brevi, economici. Si può arrivare ad uno sconto del 90% rispetto a “On-Demand”. Difetto di questo tipo di istanza è che può essere perso in qualsiasi momento se il prezzo fosse inferiore allo SPOT. 
  - e’ il più efficiente in termini di costi, utili per carichi di lavoro resiliente al guasto come: Batch, Data Analysis, Image Processing. Non raccomandato per carichi di lavoro che necessitano di continuità.
- **Dedicated Hosts**
  - Con questo tipo di istanza prendiamo avremo a disposizione l’intero server fisico avendo così anche il controllo geografico dello stesso. L’allocazione parte da un minimo di 3 anni e il costo è molto elevato. Utile per le applicazioni che hanno esigenze normative o di compliance.
- **Dedicated Instances**
  - utilizzando questo tipo abbiamo il controllo sull’hardware su cui gira la nostra istanza, cioè sappiamo che l’istanza è dedicata a noi a differenza del Dedicated Host qui non prendiamo l’intero server fisico ma ci assicuriamo solo che l’istanza sarà dedicata solo a noi e nessun’altro ne può usufruire.
- **Best practices** purchasing option: 
  - Come scegliamo quindi il tipo di stanza da utilizzare? Prendiamo in considerazione le prenotazioni dell'Hotel sceglieremo: 
  - On-Demand se non vogliamo prenotare ma vogliamo pernottare quando vogliamo noi (nessuno sconto e prezzo che si adatta alla situazione)
  - Reserved, pianifichiamo in anticipo una vacanza quindi prenotiamo prima cercando anche di usufruire di qualche sconto
  - Spot l’hotel consente alle persone di aggiudicarsi delle stanze vuote in un determinato momento che però potrebbe cambiare in qualsiasi momento
  - Dedicated se vogliamo prenotare tutto l’albergo per noi.