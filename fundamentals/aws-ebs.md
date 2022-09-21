# EBS: Elastic Block Store

- Un'istanza EC2 quando viene terminata **perde il suo volume**. Oltre ad essere terminata “spontaneamente” questa può anche subire delle interruzioni improvvise causando magare la perdita del nostro lavoro. Per far fronte a questi tipi di problemi nasce **EBS (Elastic Block Store)**
- è un **unità di rete (non fisica)**, utilizza la rete per comunicare con le istanze, il che significa che potrebbe esserci un pò di latenza nel salvataggio/recupero dei dati. Può essere **scollegata da un istanza e attaccata ad un altro in maniera veloce e a “caldo”**  (più o meno come una penna USB).
- Può essere montato **solo su un'istanza alla volta**.
- **è bloccata per Availability Zones** es un EBS che si trova in eu-west-1 non può essere attaccata ad un istanza che si trova in eu-west-2. 
	- *Per poter abilitare questa sorta di cross-zone il modo più comune è: fare uno screenshot dell’EBS che vogliamo utilizzare in una AZ diversa da quella su cui stiamo operando e in seguito creare un EBS nella zona che vogliamo utilizzarla a partire dallo screenshot fatto.*
- è capace di **scalare verticalmente in GB e IOPS** (operazioni di IO per Secondo). E' possibile aumentare queste capacità in più in qualsiasi momento (pagando).

## EBS delete on termination
 - Di default la console di **AWS setta a true** questo valore. **Bisogna sempre controllare il comportamento di EBS al termine di un'istanza EC2**
	 - **Per default, il volume ROOT EBS viene eliminato**
	 - **Per default, qualsiasi altro volume attaccato non viene eliminato.**
 - Tutto questo può essere controllato sia da Console che da CLI.
 - E’ buona prassi **preservare il volume ROOT** quando l'istanza viene terminata.
 
## EBS Snapshot
 - E’ possibile eseguire il backup dei volumi EBS in qualsiasi momento utilizzando gli **SNAPSHOT**.
 - **Occupano solo lo spazio occupato del volume EBS** (es. volume da 100 GB con 5 GB occupati, lo SNAPSHOT occuperà solo 5 GB)
 - **Non è necessario scollegare il volume** per poterlo effettuare (ma è consigliato)
 - Gli snapshot vengono utilizzati principalmente per:
	 - **Migrare Volumi da una zona ad un altra**
	 - **Ridimensionare un volume**
	 - **Cambiare il tipo di un volume**
	 - **Crittografare un volume**

## Local EC2 Instance Store
 - A differenza di EBS che sono unità di rete soggette a latenza, se si necessità di più **performance** hardware la scelta ricade sull’instance store di EC2.
 - **è la migliore per le operazione I/O**
 - **non mantiene i dati al termine dell’istanza**
 - **ottima per salvare dati cache**, temporanei, buffering **e tutte quelle cose che non richiedono persistenza**
 - rischio di perdita dei dati
 - backup e repliche sono a una nostra responsabilità

## EBS Volume Type
 - **GP2/GP3 (SSD)**
	 - equilibrato e di “General Purpose”, adatto per situazioni che non richiedono né troppa potenza né poca potenza ma che cercano un equilibrio.
 - **IO1/IO2 (SSD)**
	 - alte performance e carichi di lavoro più ardui che richiedono bassa latenza e alto throughput
 - **ST1 (HDD)**
	 - low cost, progettati per lavori ad alta produttività con accesso frequente, la velocità non è un requisito importante
 - **SC1 (HHD)**
	 - è il più economico, progettato per carichi di lavoro molto bassi che richiedono accessi poco frequenti.

#### Use Case Type

 - **General Purpose (GP2,GP3):**
	 - **GP3**: parte da 3000 IOPS e throughput di 25 mb/s arriva fino ad un massimo di 16000 IOPS e throughput di 1000 mb/s indipendentemente (scalano singolarmente)
	 - **GP2**: parte 3000 IOPS, in questo caso all’aumentare dello IOPS aumenta anche il throughput quindi i due valori aumentano in concomitanza.
 - **Alte performance (Provisioned IOPS-> IO2, IO3):**
	 - **IO1/IO2**: entrambi arrivano ad un massimo di 64000 IOPS sono per elevate prestazioni, inoltre gli IOPS possono essere incrementati in maniera indipendente dalla memoria, IO2 ha più IOPS massimi. Questo tipo di memoria è l’unico supportato per il Multi-Attach (può essere collegato a più memorie contemporaneamente).
 - **HDD Low cost (ST1)**
	 - per la sua lentezza sicuramente non può essere un volume di avvio. E’ adatto a lavori costanti, Big Data, elaborazione di log o Data Warehouse
 - **Cold HDD (SC1)**
	 - per dati non frequentemente acceduti, per scenari dove il minor costo è la cosa più importante. Non può chiaramente essere un volume di avvio.

## EBS Encryption

 - Possiamo decidere di creare un volume criptato, così facendo avremo **tutti i dati al suo interno criptati**, tutti i dati interscambiati tra un istanza e un altra saranno criptati, tutti gli snapshot saranno criptati, **tutti i volumi creati a partire da un volume criptato sarà criptato**.
 - La criptazione e la decriptazione vengono fatte in maniera totalmente trasparente, **queste operazioni hanno un impatto minimo sulla latenza**.
 - *Possiamo decidere di trasformare un EBS non criptato in criptato, basta fare uno snapshot del volume non criptato e creare un volume criptato a partire dallo snapshot fatto.*
