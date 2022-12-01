# Route 53
Route 53 è un DNS gestito altamente disponibile e scalabile e Authoritative (Authoritative = il cliente (tu) può aggiornare i record DNS)
- Route 53 è anche un registrar di domini
- può controllare la salute delle nostre risorse

## Cos'è il DNS
- Il DNS (Domain Name System) è sistema che traduce e risolve gli indirizzi IP per renderli "Human Friendly". 
- E' un concetto molto importante ed è considerata la "backbone"  dell'internet
Il DNS è una raccolta di regole e record che aiuta i client a capire come raggiungere un server tramite gli URL.
- utilizza una struttura gerarchica:  (esempio.com, .com, api.example.com, www.esempio.com) 

Alcune terminologie da sapere
- Domain Registrar: Amazon Route 53, GoDaddy, ...
- DNS Records: A, AAAA, CNAME, NS, ...
- Zone File: contiene i DNS records
- Name Server: risolve le query DNS (Authoritative or Non-Authoritative)
- Top Level Domain (TLD): .com, .us, .in, .gov, .org, ...
- Second Level Domain (SLD): amazon.com, google.com, ...

## Records
-  Ogni record contiene:
    - Domain/subdomain – e.g., example.com
    - Record Type – e.g., A or AAAA
    - Valore – e.g., 12.34.56.78
- Politica di instradamento (come Route 53 risponde alle query)
- TTL – periodo di tempo in cui il record è stato memorizzato nella cache DNS
    - High TTL (Meno traffico sulla Route 53, possibilità di avere record obsoleti)
    - Low TTL (tanto traffico, è possibile cambiare record velocemente, è difficile avere record obsoleti)
    - ad eccezione di ALIAS, il TTL è obbligatorio per tutti i Record DNS
- Route 53 i seguenti tipi di record DNS:
    - (da sapere) A / AAAA / CNAME / NS
    - (advanced) CAA / DS / MX / NAPTR / PTR / SOA / TXT / SPF / SRV

In AWS, i record più comuni sono (saranno in esame):
- A: URL per IPv4
- AAAA: URL per IPv6
- CNAME: da URL a URL (SOLO PER DOMINIO NON ROOT)
- ALIAS: URL della risorsa AWS
- NS: Controlla come viene instradato il traffico per un dominio

Preferisci alias a CNAME per le risorse AWS (per motivi di prestazioni)

## Hosted Zones 
- La Route 53 può utilizzare:
    - Public Hosted Zones contiene record che specificano come instradare traffico su Internet (nomi di pubblico dominio) application1.mypublicdomain.com
    - Private Hosted Zones contengono record che specificano come instradare traffico all'interno di uno o più VPC

## Route53 ha funzionalità avanzate come:
- Bilanciamento del carico (tramite DNS, chiamato anche bilanciamento del carico del client)
- Health check (anche se limitati...)
- Politica di routing: Simple, Weighted, Failover, Latency based, Geolocation, Multi-Value Answer, Geoproximity (si possono approndire ma non dovrebbere uscire all'esame)