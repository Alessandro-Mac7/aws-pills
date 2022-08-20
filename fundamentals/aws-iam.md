# IAM: Identity and Access Management

 - IAM sta per **Identity Access Management** fa parte dei servizi globali di AWS, cioè quei servizi che non necessitano di selezionare una regione specifica in cui operare.
 - E’ uno dei servizi fondamentali in quanto ci permette di gestire le autenticazioni e le autorizzazioni per gli utenti o per l’applicazione.
 - IAM mette a disposizione: Users, Groups e Roles.

## IAM Users
Esistono due tipi di utenti:
 - **Root User**, è l’account che creiamo di default, cioè dopo aver fatto la registrazione su AWS entreremo come utente root che dispone di qualsiasi potere quindi è importante non condividere con nessuno questo account. **Dalle best practices apprendiamo che l'account root non deve mai essere utilizzato.**
 - **Users**, sono gli utenti che possiamo creare a partire dal **Root User**, essi rappresentano i diversi utenti che faranno parte della nostra organizzazione, che possono essere raggruppati per funzionalità, ecc. Per poter accedere come utente è necessario inserire un **AccountID** o **Account Alias** che è configurabile dall’utente root e poi in seguito username e password che ci vengono fornite.

## IAM Groups
 - Per poter gestire al meglio le autorizzazioni per gli utenti IAM, nascono i **gruppi**.
 - Quando un utente decide di usare un servizio all’interno della console o fare una chiamata ad un API, AWS cerca le appartenenze al gruppo di quell'utente ed esamina i criteri di autorizzazione se il servizio è consentito allora procede altrimenti no.
 - **I gruppi possono contenere solo utenti e non altri gruppi**. Un utente può o non può appartenere ad un gruppo, e può essere in più gruppi contemporaneamente. Quindi possiamo dire che la relazione tra IAM users e IAM Groups è di tipo **many-to-many.**
 - Gli utenti associati a un gruppo **ereditano le autorizzazioni dalle policy allegate al loro gruppo**, più eventuali permessi delle policy che sono associate direttamente a quell'utente IAM.

## IAM Roles
 - I ruoli sono un altro concetto importante che riguarda IAM. Ci sono situazioni in cui non vogliamo creare e gestire nuove credenziali per i nostri utenti o le nostre applicazioni es: grande azienda con migliaia di dipendenti, è improponibile creare per ogni dipendente che usa AWS delle credenziali.
 - I ruoli nascono in modo da isolare il concetto di **autorizzazioni a un servizio AWS**. Infatti **un ruolo può essere assegnato solo e soltanto ad un servizio AWS non ad un utente**.
 - Quando un servizio assume un ruolo, AWS fornisce delle nuove credenziali di sicurezza che sono validi per una sessione limitata di tempo tramite AWS Security Token Service (AWS STS). Queste credenziali sono composte da un access key ID, secret access key e un session token con una data di scadenza.
 - I ruoli possono essere assegnati a qualsiasi servizio, ma i più comuni e noti sono: **EC2, Lambda, CloudFormation**

## IAM Policies
 - I permessi possono essere associati al singolo **utente** ad un **gruppo** o un **ruolo**.
 - Per gestire le autorizzazioni/permessi per ogni utente, gruppo o ruolo, l’assegnamento può avvenire definendo una "**policy**" tramite visualmente tramite la console di AWS o tramite un documento JSON.
 - E’ buona pratica applicare il principio “**least privilege principle**” ossia, non dare più permessi di quelli che l’utente stesso ha bisogno.
 - Una policy è definita in formato JSON ed è strutturata:
	 - **Version**: rappresenta la versione della policy
	 - **ID**: è un identificativo per la policy (opzionale)
	 - **Statement**: contiene uno o più “statement” e a sua volta contiene:
		 - **Sid**: che è un identificativo per lo statement corrente (opzionale)
		 - **Effect**: specifica se lo statement è “Allow (consentito)” o “Deny (negato)”
		 - **Principal**: definisce l’attore a cui applicare la policy che può essere un account/user/role
		 - **Action**: rappresenta la lista di azione che questa policy deve negare o consentire.
		 - **Resource**: rappresenta la lista delle risorse a cui applicare le azioni
		 - **Condition**: è una condizione che si deve verificare affinché la policy abbia effetto (opzionale)
 - AWS consiglia di utilizzare le policy disponibili e create da AWS quando possibile. Però, quando hai bisogno di maggiore controllo, è possibile definire criteri personalizzati esse vengono chiamate **“Custom Policy"**
 - Per “**testare**” una policy create possiamo utilizzare il **Policy Simulator** o la **CLI** mediante il comando **--dry-run** che ci risponderà con un errore del tipo “**UnauthorizedOperation**”
 - Le policy **vengono ereditate** nei gruppi

## IAM Security
 - **Password Policy**
	 - Oltre ad assegnare permessi vari agli utenti, tramite il servizio IAM è possibile definire anche una Policy per la creazione delle password. AWS ci da la possibilità di impostare alcuni constraints del tipo:
		 - Richiedere caratteri specifici ovvero includere lettere maiuscole, minuscole, numeri e caratteri speciali
		 - Impostare una scadenza per la password
		 - Prevenire il riuso di una password
 - **MFA**
	 - Sempre in tema di sicurezza è possibile impostare anche l’autenticazione a più fattori, questo è strettamente raccomandato per l’**account root** che è il più importante da proteggere.
	 - L’autenticazione a due fattori si può attivare o con un **Virtual MFA device** (scaricando un app come google Authenticator o simili), con una **security Key** o con un **device fisico**.

## IAM Audit
Per quanto riguarda l’Audit e il controllo degli utenti AWS mette a disposizione due strumenti:
- **IAM Credentials Report**  (account-level)
	- un report che elenca tutti gli utenti del tuo account e lo stato dei loro vari credenziali
 - **IAM Access Advisor** (user-level)
	 - mostra le autorizzazioni dei servizi concesse a un utente e quando quelle ultimo accesso ai servizi questo ci permette di controllare le nostre policy ed eventualmente vederle

## IAM Best Practices and Shared Responsibility
- **Non utilizzare l'account root** tranne che per la configurazione dell'account AWS
- **utente fisico = un utente AWS**
- Assegna gli utenti ai gruppi e assegna i permessi ai gruppi
- Creare una policy per le password 
- Utilizzare e imporre l'uso dell'autenticazione a più fattori (MFA)
- Creare e utilizzare ruoli per concedere autorizzazioni ai servizi AWS
- Utilizzare chiavi di accesso per l'accesso programmatico (CLI/SDK)
- Controlla le autorizzazioni del tuo account con IAM Credentials Report
- Non condividere mai utenti IAM e chiavi di accesso
- AWS **ha la responsabilità per l’infrastruttura**, le configurazioni e analisi delle vulnerabilità e le validazioni di compliance;
- Noi abbiamo la responsabilità di creare, gestire e monitorare utenti, gruppi, ruoli e policy, abilitare la MFA sui nostri account, cambiare spesso le nostre chiavi e usare il tool IAM per applicare i giusti permessi, analizzarli e gestirli
