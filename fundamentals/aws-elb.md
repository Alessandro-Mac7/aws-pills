# ELB: Elastic Load Balancer

## Scalabilità e Alta disponibilità (High Availability)

- **Scalabilità** significa gestire carichi di lavoro di qualunque tipo adattandosi. Un'applicazione può scalare in due modi cioè in:
	- **verticale**: incrementando la dimensione o la potenza di calcolo verticalmente es. scalare verticalmente un'istanza t1.micro vuol dire portarla ad t1.xlarge. Questo tipo di scalabilità è diffusa nei sistemi non distribuiti come i database. Lo scaling verticale da quel che abbiamo capito pone dei limiti non indifferenti a livello hardware.
	- **orizzantale**: significa **incrementare il numero di istanze** per la nostra applicazione. Naturalmente scalare un applicazione orizzontalmente implica avere un sistema distribuito.

- **High availability** è un concetto legato principalmente alla scalabilità orizzantale, ovvero, un applicazione è detta **altamente disponibile** quando **è presente in almeno 2 AZ diverse**, l'obiettivo è quello di essere pronti ed integri in caso di disastri o perdite di interi data center. Può essere passiva (RDS Multi AZ) o attiva (ridimensionamento orizzontale).

- **EC2 esempi:** 
	- **scalibilità verticale**: aumento dimensioni istanze (up/down) da 0.5 GB a 12 TB.
	- **scalibilità orizzantale**: incremento numero di istanze attive (out/in) -> Auto scaling, Load Balancer
	- **high Availability**: lancia almeno due istanze in zone diverse -> Auto scaling group Multi-AZ, Load Balancer Multi AZ

## Load Balancer
- Un load balancer è un server che “trasmette” il traffico in entrata verso server multipli
- L’utilizzo di un load balancer è importante perché:
	- **distribuisce il carico di lavoro** su istanze downstream
	- espone **un singolo punto di accesso** (DNS) alla nostra applicazione
	- gestisce gli errori delle istanze
	- esegue i **“sanity check”** regolarmente sulle istanze attive per verificare che il loro stato sia buono
	- fornisce **connessioni SSL** (HTTPS) per le nostre applicazioni
	- rafforza lo **"stickiness"** (viscosità) con i cookie
	- soddisfa l’**high availability** grazie al Multi-AZ
	- separa il traffico pubblico da quello privato

 - **Health check o sanity check**: è un aspetto importante per il load balancing, tramite questa chiamata il LB **acquisisce lo stato di un'istanza** controllando se è in grado di rispondere al traffico che le viene inoltrato. 
	- Solitamente questo controllo viene eseguito su uno specifico path esposto (solitamente /health). 
	- Se la risposta è 200 allora **è sano** altrimente viene marchiato come **“unhealthy”**

**ELB** è quindi un Load Balancer **gestito da AWS** il che vuol dire che Amazon garantisce che il sistema sia sempre funzionante e attivo, aggiornato, e manutenuto regolarmente e lascia all’utente solo le configurazioni applicative verso la nostra istanza ma è già tutto **pronto all’uso**. 

Questo servizio è un grosso vantaggio per chi vorrebbe includere un Load Balancer nella propria applicazione, perchè sarebbe costoso crearne uno proprio e costa meno configurarne uno già fatto.

è inoltre integrato con gli altri servizi che fornisce AWS come **(EC2, Auto Scaling Group, ECS, ACM, Cloud Watch, Route 53, Global Accelerator,  WAF)**.

## ELB Types
- I tipi previsti da AWS sono 4:
	- **Classic Load Balancer**: la cosiddetta *“vecchia generazione”*, CLB (2009) supporta:  HTTP, HTTPS, TCP, SSL
	- **Application Load Balancer**: *“new generation”*, ALB (2016) supporta: HTTP, HTTPS, WebSocket
	- **Network Load Balancer**: “new generation”, NLB (2017) supporta: TCP, TLS, UDP
	- **Gateway Load Balancer**: GLB (2020) supporta il layer 3 cioè IP.
- AWS consiglia l’utilizzo dei **ALB** anziché optare per il **CLB** per le funzionalità che vengono offerte.

## CLB Classic Load Balancer
- supporta:  HTTP, HTTPS, TCP, SSL
- esegue gli health check su TCP o HTTP è sconsigliato l’utilizzo (legacy).
- Ha un hostname fisso: XXX.region.elb.amazonaws.com

## ALB Application Load Balancer
- bilancia il carico su macchine multiple (HTTP) definite come **target group**
- può bilanciaew su più applicazioni presenti sulla stessa macchina (docker container).
- supporta HTTP/2 e il WebSocket
- supporta il redirect da HTTP a HTTPS

- si possono avere diverse **Routing Tables** applicate a diversi **“target groups”**:
	- è possibile definire rotte basate sul path del URL es. example.com/user
	- rotte basate sull’hostname nell’URL es. one.example.com e other.example.com
	- rotte basate sulle query string presenti nell’URL ex. example.com/user?name=pippo

- questo tipo di load balancer è ideale per applicazioni con un **architettura a microservizi** o applicazioni basatu sui container
- ha una funzione di mappatura sulle porte per reindirizzare su porte dinamica per es. su ECS.
- per replicare lo stesso funzionamento utilizzando un CLB, ne avremmo bisogno di N per applicazione (per far capire che il vantaggio di ALB è alto)
- hostname fisso XXX.region.elb.amazonaws.com
- l’application server non vede mai l’IP del client direttamente, il vero IP infatti è inserito nell’header nel parametro **X-Forwarded-For**, la porta nel parametro **X-Forwarder-Port**, il protocollo in **X-Forwarder-Proto**

### ALB Target Groups
- Per **EC2**, le istanze sono gestite da Autoscaling Group (HTTP)
- Per **ECS** (task) sono gestite da se stesso.
- Per le **Lambda Function**, HTTP viene traslato in un evento JSON
- Gli indirizzi **IP diventano privati**.
- **ALB può dirottare il traffico su target multipli**, il sanity check in questo caso viene fatto sull’**intero Target Group**.

## NLB Network Load Balancer
- Questo load balancer di **layer 4** permette di: dirottare il traffico **TCP e UDP** verso le nostre istanze
- piò gestire **milioni di richieste** al secondo
- ha una **latenza ridotta**, più o meno 100 ms contro i 400 ms di ALB
- NLB ha un **indirizzo IP Statico per AZ** e supporta l’assegnazione di un indirizzo Elastic IP (utile per un eventuale whitelist IP)
- NLB è usato quando si vuole **performance e bassa latenza** per TCP o UDP (traffico) e non è incluso nel pacchetto free.
- Il processo di creazione è uguale a ALB, **stesso concetto di target group**  ecc. esempio di possibili scelte target

## GLB Gateway Load Balancer
- Il più nuovo operante a **Layer 3** (Network Layer) ossia gli IP
- **Transparent Network Gateway**: singolo ingresso singola uscita per tutto il traffico
- **Load Balancer**: distribuisce il traffico verso le virtual appliance (macchine virtuali)
- Utilizza il protocollo **GENEVE** sulla porta 6081
- I target group che possono essere scelti sono: **EC2 e IP Address**

## Sticky Session  
- lo **"stickiness"** (viscosità) è un modo per reindirizzare un client verso la stessa instanza anche se essa è gestita da un Load Balancer.
- funziona sia con **CLB che ALB**
- Per far funzionare questo sistema viene generato un **cookie** che ha una **durata** (settabile), un **nome** e un **valore**. L’utilizzo di questo cookie ci permette di **non perdere la sessione** e l’abilitazione dello stesso può portare ad un’**alterazione del bilancio** di carico da parte del Load Balancer.
- si possono avere due tipi di cookie:
	- **Application Based Cookie**: che può essere a sua volta 
		- **“custom”**: generato dal target, che include attributi custom inseriti da chi lo genera, il nome deve essere specificato individualmente per ogni target group, non possono essere utilizzati i seguenti nomi: **AWSALB, AWSALBAPP, AWSALBTG,**
		- **“application cookie”**: generato dal load balancer con il nome **AWSALBAPP**
	- **Duration Cookie**: generato dal Load Balancer con il **AWSALB** per ALB e **AWSELB** per CLB.

## Cross Zone Load Balancing  
- E’ un modo di distribuire il carico, ovvero ogni istanza registrata nel load balancer viene distribuita in modo uniforme in tutte le istanze registrate nella AZ.
- con l'attivazione di questa funzionalità ogni istanza ELB **viene distribuita uniformamente** tra tutte le istaze registrate nella AZ, senza la richiesta sarebbe distribuita al nodo provocando uno scompenso.
- In **ALB** è **sempre attivo di default** e non può essere disabilitato 
- in **NLB** è **disponibile** all’attivazione ma si paga 
- in **CLB** è d**isattivato di default** e si può attivare gratuitamente.

## SSL Certificates  
- I Load Balancer usano i certificati X.509 (SSL/TLS), possono essere facilmente gestiti mediante il servizio offerto da AWS ACM. Alternativamente possiamo caricare il nostro certticato.
- può essere utilizzato il reindirizzamento su HTTPS solo se:
	- specifichiamo un **certificato di default**.
	- aggiungiamo una **lista di certificati** (nel caso avremmo bisogno di supportare domini multipli)
	- possiamo usare **SNI (Server Name Indicator)** per associare ad un determinato hostname il certificato corretto da usare.
- **CLB**: supporta solo un certificato SSL per volta, bisogna usare più CLB per server multipli per avere più SSL;
- **ALB/NLB**: supportano più certificati SSL grazie a **SNI** che garantisce il corretto funzionamento.

## SNI (Server Name Indicator)  
- risolve il problema di **caricare certificati multipli** in un Web Server.
- è un protocollo nuovo e richiede al client di indicare il nome del hostname del server di destinazione nell’handshake iniziale, il server quindi troverà il certificato corretto e lo restituirà di conseguenza. 
- come detto sopra **funziona solo con il Load Balancer di nuova generazione** quindi **ALB e NLB**, **CLB non è supportato**.

## Connection Draining 
- definito **Connection Draining per CLB** e **Deregistration Delay per ALB e NLB**.
- è il tempo che viene impegato per completare le richieste in transito mentre l’istanza si sta de-registrando o non è più sana.
- viene interrotto l’invio di nuove richieste all’istanza che si sta de-registrando.  
- questo avviene in un tempo che varia da 1 a 3600 sec (default 300 sec). 
- può essere disabilitata mettendo a 0 il valore. 
- settare un basso valore se le richieste sono di brevi durate.