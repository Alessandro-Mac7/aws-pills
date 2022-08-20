# Security Groups
- Sono fondamentali per la sicurezza della rete in AWS, tramite la **Security Group controlliamo il traffico della rete** gestendo **permessi e divieti** verso le nostre istanze EC2.
- I Security Group contengono solo **“allow rules”**, possono puntare a specifici indirizzi IP o altri Security Group.
- Possiamo vedere i Security Group come una sorta di firewall per le nostre istanze EC2, nello specifico possono:
  - **Regolare l’accesso alle porte**
  - **Autorizzare range di IP** (IPv4, IPv6)
  - **Controllare il traffico inbound** (dagli altri verso l’istanza) e il traffico outbound (dall’istanza verso gli altri)
- Alcune delle porte da conoscere: 
  - **22**: SSH/SFTP (log in linux Shell, Upload File)
  - **21**: FTP (Upload file)
  - **80**: HTTP (accesso non sicuro website)
  - **443**: HTTPS (accesso sicuro website)
  - **3389**: RDP (Remote Desktop Protocol, log istanza Windows)

## Good to Know
- **SG = Security Group**
- Possono essere attaccate ad **una o più istanze**
- Opera su **Regions/VPC** in combinazione
- Vive **"fuori"** da EC2, se il traffico è bloccato l’istanza non lo vedrà
- è buona pratica mantenere separati i SG per gli accessi in SSH
- Se l’applicazione va in **Timeout** molto probabilmente è un problema di regole in SG
- Se l’applicazione dasse errori del tipo **“connection refused”**, il problema potrebbe essere legato all’applicazione o non è stata lanciata proprio l’istanza
- Tutto **il traffico inbound è bloccato di default**, mentre quello outbound è autorizzato di default.
- **EC2 SSH Troubleshooting**
  - Per la connessione possono essere usati **Putty, SSH o Ec2 Instance Connect**
  - Per Instance Connect non c’è la necessità di installazioni esterne e download di certificati come vengono richieste da SSH o Putty, ma è possibile accedere direttamente in quanto ad ogni accesso viene generato un token temporaneo.
