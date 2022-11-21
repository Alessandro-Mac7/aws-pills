# ASG: Auto scaling Group 
Il carico di un'applicazione può aumentare o diminuire specialmente nel mondo cloud che possono essere create e distrutte N istanze in maniera veloce. Proprio per questo nasce ASG con l'obiettivo di:
- **far scalare orizzontalmente un applicazione** in modo da rendere l’applicazione pronta a carichi maggiori (aggiunta di istanze) -> **SCALE OUT**
- **aumentare la scalabilità** (rimuovere le istanze) per corrispondere a un carico ridotto -> **SCALE IN**
- Assicurarsi di avere un **minimo e un massimo di istanze** in esecuzione
- **Registrare automaticamente** nuove istanze del load balancer
- - Le **scaling policy** possono essere basate su **CPU, Network**, ecc, su **metriche custom** o basate su una **pianificazione**
- è possibile creare un ASG tramite **configurazione iniziale o partendo da un template**
- **non è possibile modificare delle configurazioni "a caldo"**, ma è necessaria fornire una nuova configurazione di avvio o un template
- I ruoli **IAM** associati ad un ASG **verranno assegnate alle rispettive istanze**
- ASG è gratuito di base, viene pagato il lancio delle risorse in più.
- Avere istanze sotto ASG vuol dire che se una termina per qualche motivo **è compito di ASG ricrearla** in modo da avere sempre una quantità minima desiderata di istanze.

## Auto scaling Group Alarms
- una delle policy che può essere usata per far si che un target group scali orizzontalmente si basa sugli **“Allarmi”**.
- un **“Allarme”** può **monitorare delle metriche** esaminando per esempio l’utilizzo della CPU/RAM.
- Le metriche vengono **calcolate per tutte le istanze dentro un ASG**. 
- basandoci sugli allarmi possiamo creare **policy di incremento/decremento istanze**
- su EC2 è possibile già scegliere degli **Allarmi forniti di default** come:
	- utilizzo medio della CPU
	- numero di richieste per istanze
	- flusso medio di ingresso di rete, flusso medio di uscita di rete
- possiamo scalare le nostre istanze anche creando le nostre **metriche personalizzate**. Es. abbiamo la necessità di scalare la nostra applicazione in base al numero di persone connesse. 
- Possiamo creare una metrica personalizzata:
	- inviando **metriche custom** direttamente **dalla nostra applicazione EC2 e CloudWatch** tramite l’utilizzo dell’API **PutMetric**.
	- creando **allarmi personalizzati su cloudwatch** che reagiscono a dei valori bassi/alti
	- utilizzando **cloudwatch alarms come policy** di scaling di ASG
- è possibile settare dinamicamente uno scaling policies come:
	- **Target tracking scaling**: semplice da settare, es. vogliamo mantenere l’utilizzo della CPU intorno al 40%
	- **Simple/Step scaling**: es. quando CloudWatch attiva un allarme (CPU > 70%) vengono aggiunte 2 unità quando è tornato alla normalità (CPU < 30%) viene rimossa 1 unità
	- **Azione schedulata**: anticipo di uno scaling in base a qualcosa che so che deve accadere es. so che giorno X verrà attivato il black friday e quindi la mia affluenza sicuramente aumenterà quindi incremento le mie unità.
	- **Predictive Scaling**: sviluppato e introdotto di recente cerca di prevedere il carico di lavoro e reagire ad esse in maniera automatica ed efficiente.
- Le metriche “standard” che dobbiamo necessariamente conoscere sono:
	- **CPU Utilization**: utilizzo medio CPU
	- **RequestCountPerTarget**: numero di richieste per istanza
	- **Network In / Network Out medio**

## Scaling Cooldown
- Dopo uno scaling di istanza si entra in periodo chiamato **“Cooldown”** (default: 300ms) che è il periodo in cui l’istanza non può essere né aggiunta e né rimossa.
- durante il periodo di cooldown **ASG non può avviare ne rimuovere istanze**
- per velocizzare il processo il consiglio è di utilizzare un **AMI personalizzata che possa ottimizzare i tempi di avvio/spegnimento**