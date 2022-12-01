# VPC
- All'interno di una regione è possibile creare una VPC (Virtual Private Cloud).
- Ogni VPC contiene sottoreti.
- Ogni sottorete deve essere mappata a una AZ.
- È comune avere una sottorete con IP pubblico e IP privato.
- È comune avere molte sottoreti per AZ.
- Le sottoreti pubbliche e private possono comunicare se si trovano nello stesso VPC

## Public Subnets 
Contengono solitamente: 
- Bilanciatori del carico
- Siti web statici
- File
- Livelli di autenticazione pubblica

## Private Subnets 
Contengono solitamente: 
- Web application servers
- Databases

## Sommario
- Questo argomento non è molto richiesto in materia d'esame
- Tutti i nuovi account vengono forniti con un VPC predefinito
- È possibile utilizzare una VPN per connettersi a un VPC
- I VPC flow logs ti consentono di monitorare il traffico all'interno, in entrata e in uscita dal tuo VPC (utile per sicurezza, prestazioni, audit)
- I VPC sono per account per regione
- Le subnets sono per VPC per AZ
- Alcune risorse AWS possono essere distribuite in VPC mentre altre no
- Puoi eseguire il peering di VPC (all'interno o tra account) per far sembrare che facciano parte della stessa rete