---
title: "Implementace Oracle Data Guard na virtuálním počítači Azure Linux | Microsoft Docs"
description: "Rychle získáte Oracle Data Guard nahoru a spouštění v prostředí Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: rclaus
ms.openlocfilehash: 822e18db4d01210d741addad4c820f51adc8f26a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Implementace Oracle Data Guard na virtuálním počítači Azure Linux 

Rozhraní příkazového řádku Azure slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento článek popisuje, jak používat rozhraní příkazového řádku Azure k nasazení databázi Oracle Database 12c z image Azure Marketplace. Tento článek ukazuje pak můžete krok za krokem, jak nainstalovat a nakonfigurovat ochranu dat na virtuální počítač Azure (VM).

Než začnete, ujistěte se, že je nainstalované rozhraní příkazového řádku Azure. Další informace najdete v tématu [Průvodce instalací Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí
### <a name="assumptions"></a>Předpoklady

Pokud chcete nainstalovat Oracle Data Guard, potřebujete vytvořit dva virtuální počítače Azure ve stejné skupině dostupnosti:

- Primární virtuální počítač (myVM1) má spuštěnou instanci Oracle.
- V úsporném režimu virtuálních počítačů (Můjvp2) je nainstalován pouze software Oracle.

Marketplace obrázku, který použijete k vytvoření virtuálních počítačů je Oracle: Oracle – databáze-Ee:12.1.0.2:latest.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k předplatnému Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login) příkazů a postupujte podle na obrazovce pokynů.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků s použitím [vytvořit skupinu az](/cli/azure/group#az_group_create) příkaz. Skupinu prostředků Azure je logický kontejner, ve které jsou nasazené a spravované prostředky. 

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v `westus` umístění:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Vytvoření skupiny dostupnosti je nepovinný, ale doporučujeme ho. Další informace najdete v tématu [sady Azure dostupnosti pokyny](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) příkaz. 

Následující příklad vytvoří dva virtuální počítače s názvem `myVM1` a `myVM2`. Pokud už neexistují v umístění klíče výchozí klíče SSH, vytvoří se také. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

Vytvořte myVM1 (primární):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Po vytvoření virtuálního počítače, rozhraní příkazového řádku Azure se zobrazují informace podobně jako v následujícím příkladu. Poznamenejte si hodnotu `publicIpAddress`. Použijte tuto adresu přístup k virtuálnímu počítači.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

Vytvoření Můjvp2 (pohotovostní):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Poznamenejte si hodnotu `publicIpAddress` po vytvoření Můjvp2.

### <a name="open-the-tcp-port-for-connectivity"></a>Otevřete port TCP pro připojení k síti

Tento krok nakonfiguruje externí koncové body, které umožňují vzdálený přístup k databázi Oracle.

Otevřete port pro myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Výsledek by měl vypadat podobně jako následující odpověď:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Otevřete port pro Můjvp2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte na IP adresu `publicIpAddress` hodnotu pro virtuální počítač.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Vytvořit databázi na myVM1 (primární)

Oracle software je již nainstalována na bitovou kopii Marketplace, takže dalším krokem je pro instalaci databáze. 

Přepnout na superuživatel Oracle:

```bash
$ sudo su - oracle
```

Vytvoření databáze:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Výstupy by měl vypadat podobně jako následující odpověď:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

Nastavení proměnných ORACLE_SID a ORACLE_HOME:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Volitelně můžete přidat ORACLE_HOME a ORACLE_SID soubor /home/oracle/.bashrc, tak, aby tato nastavení se uloží pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Nakonfigurujte ochranu dat

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Povolit režim protokolu archiv na myVM1 (primární)

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
Povolit protokolování platnost a ujistěte se, zda je přítomen alespoň jeden soubor protokolu:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Vytvořte znovu pohotovostní protokoly:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Zapnout Flashback (díky čemuž obnovení bylo mnohem snazší) a nastavte pohotovostní\_souboru\_správu, aby automaticky. Ukončete SQL * Plus potom.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Nastavení služby v myVM1 (primární)

Upravovat nebo vytvářet tnsnames.ora souboru, který je ve složce ORACLE_HOME\network\admin $.

Přidejte následující položky:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Upravovat nebo vytvářet listener.ora souboru, který je ve složce ORACLE_HOME\network\admin $.

Přidejte následující položky:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Povolte zprostředkovatele ochrana dat:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```
Spuštění nástroje pro sledování:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Nastavení služby v Můjvp2 (pohotovostní)

SSH k Můjvp2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Přihlaste se jako Oracle:

```bash
$ sudo su - oracle
```

Upravovat nebo vytvářet tnsnames.ora souboru, který je ve složce ORACLE_HOME\network\admin $.

Přidejte následující položky:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Upravovat nebo vytvářet listener.ora souboru, který je ve složce ORACLE_HOME\network\admin $.

Přidejte následující položky:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Spuštění nástroje pro sledování:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Obnovit databázi do Můjvp2 (pohotovostní)

Vytvoří /tmp/initcdb1_stby.ora soubor parametru s následující obsah:
```bash
*.db_name='cdb1'
```

Vytváření složek:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Vytvořte soubor heslo:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
Spuštění databáze na Můjvp2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Obnovte databázi pomocí nástroje RMAN:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Spusťte následující příkazy v RMAN:
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Po dokončení příkazu, měli byste vidět zpráv podobný následujícímu. Ukončete RMAN.
```bash
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17

RMAN> EXIT;
```

Volitelně můžete přidat ORACLE_HOME a ORACLE_SID soubor /home/oracle/.bashrc, tak, aby tato nastavení se uloží pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Povolte zprostředkovatele ochrana dat:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Konfigurace zprostředkovatele ochrana dat na myVM1 (primární)

Spusťte správce ochrana dat a přihlaste se pomocí SYS a heslo. (Nepoužívejte ověřování operačního systému.) Postupujte takto:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Zkontrolujte konfiguraci:
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Po dokončení instalace Oracle Data Guard. V další části se dozvíte, jak k testování připojení a přepínačů.

### <a name="connect-the-database-from-the-client-machine"></a>Připojení databáze z klientského počítače

Aktualizujte nebo vytvoření souboru tnsnames.ora v klientském počítači. Tento soubor je obvykle v $ORACLE_HOME\network\admin.

Nahraďte IP adresy s vaší `publicIpAddress` hodnoty myVM1 a Můjvp2:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Spusťte SQL * Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-the-data-guard-configuration"></a>Otestujte konfiguraci Data Guard

### <a name="switch-over-the-database-on-myvm1-primary"></a>Přepínač pro databázi na myVM1 (primární)

Přepnutí z primární do úsporného režimu (cdb1 k cdb1_stby):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Nyní můžete připojit k databázi pohotovostní.

Spusťte SQL * Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Přepínač pro databázi na Můjvp2 (pohotovostní)

Na přepnutí, spusťte na Můjvp2 následující:
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Znovu musí teď nebudete moct připojit k primární databázi.

Spusťte SQL * Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Dokončení instalace a konfigurace Data Guard na Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Odstranění virtuálního počítače

Když virtuální počítač již nepotřebujete, můžete odebrat skupinu prostředků, virtuální počítač a všechny související prostředky následující příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

[Kurz: Vytvoření vysoce dostupné virtuální počítače](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky rozhraní příkazového řádku Azure nasazení virtuálních počítačů](../../linux/cli-samples.md)
