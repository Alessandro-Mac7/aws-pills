# EFS: Elastic File Store
- è servizio gestito da AWS, **può essere montato su più istanze ec2 simultaneamente**. 
- Funziona con le istanze in EC2 nella **stessa AZ e anche in multi-AZ**
- Scalabile, affidabile e costoso. 
- Questo tipo di volume è adatto per: content management, web serving, WordPress, data sharing, ecc.
- Per controllare l’accesso vengono usati i **Security Group** 
- E’ compatibile con le macchine Linux AMI e la crittografia che applica è KMS. 
- **POSIX** è il file system standard usato EFS. 
- **scala automaticamente** non ci dobbiamo preoccupare di scegliere una tipologia in base a quello che vogliamo fare ed è pay per use.
- più nello specifico per quanto riguarda la performance:
  - EFS Scale -> **scala a fino all’ordine dei PETAByte autonomamente**
  - Performance Mode -> (settabile al momento della creazione) per ridurre la latenza al minimo infatti è detta latence sensitive.
  - Throughput Mode -> Bursting settabile.
  - Storage Tiers -> basso prezzi accessi poco frequenti tipo HDD.

## EFS vs EBS
- **EBS**:
  - può essere montato solo ad **un'istanza per volta** tranne (a meno che non si opti per IOPS che però è estremamente costoso) 
  - è **limitato per AZ**, se vogliamo abilitare il cross AZ dovremmo utilizzare il workaround "snapshot-ripristino nella zona che vogliamo".
  - con GP2 **aumentando gli IOPS** aumentiamo automaticamente anche la memoria.
  - le istanze ROOT quando **vengono terminate** di default eliminano il Volume.
- **EFS**:
  - può essere montato a **più'istanze simultaneamente**;
  - è **multi AZ**, ma molto costoso
  - non ci dobbiamo preoccupare di scegliere una determinata tipologia perchè si "adatta" in quanto **scala automaticamente**.
  - è adatto quando è necessario condividere file tra istanze (es. **WordPress**).
  - il file system funziona solo su istanze **Linux POSIX**
  - è un NFS cioè **Network File System**