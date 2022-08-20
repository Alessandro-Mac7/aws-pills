
# S3
- è un **servizio globale** di AWS
- Amazon S3 consente di memorizzare **oggetti (file)** in **"bucket" (directory)**.
- I bucket devono avere un nome univoco a livello globale
	- **Naming Convention**:
		- No maiuscole
		- Nessun trattino basso (‘_’)
		- lunghezza dei caratteri compresa tra 3 e 63
		- non un indirizzo IP
		- deve iniziare con una lettere minuscola o un numero
- **Objects**
  - sono dei file, hanno un **chiave (Key)**. La chiave è il suo **percorso completo**:
    - <my_bucket>/my_file.txt
    - <my_bucket>/my_folder/another_folder/my_file.txt
  - Non esiste il concetto di "directory" all'interno dei bucket (anche se l'interfaccia utente vi farà credere il contrario).
  - Solo chiavi con nomi molto lunghi che contengono degli slash ("/").
  - I valori degli oggetti sono il contenuto del corpo:
    - La **dimensione massima è di 5TB**
    - Se si caricano più di 5GB, si deve usare il **"multi-part upload"**.
  - **Metadati** (elenco di coppie chiave/valore di testo - metadati di sistema o dell'utente)
  - **Tag** (coppia chiave/valore Unicode - fino a 10) - utile per la sicurezza / ciclo di vita
  - **Version ID**

## S3 Versioning
- È abilitato a livello di bucket  
- La **sovrascrittura** della stessa chiave/file incrementa la **"versione"**: 1, 2, 3 
- È buona norma versionare i bucket
  - **Protezione contro le cancellazioni involontarie**(possibilità di ripristinare una versione)
  - Facile ritorno alle versioni precedenti
- Qualsiasi file che non sia una versione precedente all'abilitazione del versioning avrà la **versione "null"**.

## S3 Encryption for Objects
- Esistono 4 metodi di crittografia degli oggetti in S3:
  - **SSE-S3**: crittografia di oggetti S3
    - **Crittografia tramite chiavi gestite da AWS S3**
	- L'oggetto viene crittografato lato server
	- Tipo di crittografia AES-256
	- Deve essere aggiunto l’header: **"x-amz-server-side-encryption": "AES256"**
  - **SSE-KMS**: crittografia con chiavi gestite e amministrate da KMS
	- **Vantaggi del KMS**: controllo dell'utente + audit trail
	- L'oggetto è crittografato lato server
	- Mantenimento del controllo della politica di rotazione delle chiavi di crittografia
    - Deve essere impostata l'intestazione: **"x-amz-server-side-encryption": "aws:kms"**
  - **SSE-C**: crittografia lato server che utilizza chiavi di dati completamente gestite dal cliente al di fuori di AWS.
    - Amazon S3 non memorizza la chiave di crittografia fornita dall'utente.
	- È necessario utilizzare HTTPS
	- La chiave di crittografia deve essere fornita nelle intestazioni HTTP, per ogni richiesta HTTP effettuata
  - **Crittografia lato client**
    - Libreria client come il client di crittografia di Amazon S3
    - I client devono crittografare i dati prima di inviarli a S3.
    - I clienti devono decifrare i dati quando li recuperano da S3.
    - Il cliente gestisce completamente le chiavi e il ciclo di crittografia
- **Crittografia in transito (SSL)**
  - AWS S3 espone:
  	- endpoint HTTP: non crittografato
  	- endpoint HTTPS: crittografia in transito
  - Siete liberi di utilizzare l'endpoint che preferite, ma si raccomanda l'HTTPS.
  - **HTTPS è obbligatorio per SSE-C**
  - La crittografia in volo è anche chiamata SSL / TLS

## Sicurezza S3
- Basata sull'utente
  - **Criteri IAM:** quali chiamate API devono essere consentite per un utente specifico dalla console IAM.
- Basati sulle risorse
  - **Criteri di bucket**: regole a livello di bucket dalla console S3 
  - consentono di utilizzare più account.
  - Elenco di controllo dell'accesso agli oggetti **(ACL)**
    - grana più fine
  - Elenco di controllo dell'accesso ai bucket **(ACL)**
    - meno comune
- **Rete**
  - Supporta gli endpoint VPC (per le istanze in VPC senza Internet).
- **Logging and Audit**:
  - I log degli accessi S3 possono essere **archiviati in altri bucket S3**.
  - Le chiamate API possono essere registrate in **AWS CloudTrail**
- **User Security**
  - **MFA** (autenticazione multi-fattore) può essere richiesta nei bucket versionati per cancellare gli oggetti
  - **Signed URLs**: URL che sono validi solo per un periodo di tempo limitato (es. servizi videopremium per gli utenti che hanno effettuato l'accesso).

## S3 Bucket Policy
- JSON based policies 
  - **Risorse**: bucket e oggetti
  - **Azioni**: Set di API da consentire o negare
  - **Effect**: Consenti / Rifiuta
  - **Principal**: l'account o l'utente a cui applicare il criterio
- **Usa la policy sul bucket S3 per**:
  - **Concedere l'accesso pubblico al bucket**
  - **Forzare gli oggetti a essere crittografati al momento del caricamento** 
  - Concedere l'accesso a un altro account **(Cross Account)**
    
## S3 Website
- S3 può ospitare **siti web statici** e renderli **accessibili sul web**.
- L'URL del sito web sarà:
  - .s3-website..amzonaws.com o .s3-website..amazonaws.com
- Se si ottiene un errore 403 (forbidden), **assicurarsi che la policy del bucket consenta la lettura pubblica!**

## S3 Cors
- **Se si richiedono dati da un altro bucket S3, è necessario abilitare CORS.**
- Il **Cross Origin Resource Sharing** consente di limitare il numero di siti web che possono richiedere i vostri file in S3 (e di limitare i costi).
- Questa è una domanda d'esame molto diffusa
    
### AWS S3 - Consistency Model
- **Consistenza in lettura dopo la scrittura per i PUTS di nuovi oggetti**
  - Non appena un oggetto viene scritto, possiamo recuperarlo ex: (PUT 200 -> GET 200)
  - Questo è vero, tranne se abbiamo fatto una GET prima per vedere se l'oggetto esisteva
- **Eventuale coerenza per le cancellazioni e le modifiche di oggetti esistenti**
  - Se leggiamo un oggetto dopo averlo aggiornato, potremmo ottenere la versione precedente ex: (PUT 200 -> PUT 200 -> GET 200 (potrebbe essere la versione precedente))
  - Se si cancella un oggetto, si potrebbe essere ancora in grado di recuperarlo per un breve periodo di tempo es: (DELETE 200 -> GET 200)
    
### AWS S3 Altro
- S3 può **inviare notifiche** sulle modifiche a
  - **AWS SQS**: servizio di coda
  - **AWS SNS**: servizio di notifica
  - **AWS Lambda**: servizio serverless
- S3 ha una funzione di **replica interregionale** (gestita)

### AWS S3 - Prestazioni
- **Caricamento più veloce di oggetti di grandi dimensioni (>5GB), utilizzare il caricamento multipart**
- **Parallelizza le PUT** per un maggiore throughput
- **Massimizza la larghezza di banda** della rete
- **Riduzione del tempo di retry** nel caso in cui una parte fallisca
- **Usare CloudFront** per raggiungere gli oggetti S3 in tutto il mondo (migliora la lettura).
- **Accelerazione dei trasferimenti S3** (utilizza le posizioni dei bordi): basta cambiare l'endpoint su cui si scrive, non il codice.
- Se si utilizza la crittografia SSE-KMS, è possibile che i limiti AWS per l'utilizzo di KMS siano limitati (~100 - 1000 download / upload al secondo).