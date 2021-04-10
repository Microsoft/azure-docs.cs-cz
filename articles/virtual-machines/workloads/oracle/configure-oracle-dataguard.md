---
title: Implementace Oracle data Guard na virtuálním počítači Azure Linux | Microsoft Docs
description: Do prostředí Azure se rychle dostanete a spusťte ochranu dat Oracle.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: 1b04ef24ff01787c6904db0e288c23d4434e7dcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673829"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Implementace Oracle data Guard na virtuálním počítači Azure Linux 

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tento článek popisuje, jak pomocí rozhraní příkazového řádku Azure nasadit databázi Oracle Database 12c z image Azure Marketplace. V tomto článku se pak zobrazí, krok za krokem, jak nainstalovat a nakonfigurovat ochranu dat na virtuálním počítači Azure (VM).

Než začnete, ujistěte se, že je rozhraní Azure CLI nainstalované. Další informace najdete v příručce pro [instalaci Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí
### <a name="assumptions"></a>Předpoklady

Pro instalaci Oracle data Guard je potřeba vytvořit dva virtuální počítače Azure ve stejné skupině dostupnosti:

- Primární virtuální počítač (myVM1) má spuštěnou instanci Oracle.
- Pohotovostní virtuální počítač (myVM2) má jenom nainstalovaný software Oracle.

Image Marketplace, kterou použijete k vytvoření virtuálních počítačů, je Oracle: Oracle-Database-EE: 12.1.0.2: nejnovější.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se ke svému předplatnému Azure pomocí příkazu [AZ Login](/cli/azure/reference-index) a postupujte podle pokynů na obrazovce.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Vytvoření skupiny dostupnosti je volitelné, ale doporučujeme. Další informace najdete v tématu [pokyny k Azure Availability Sets](/previous-versions/azure/virtual-machines/windows/infrastructure-example).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří dva virtuální počítače s názvem `myVM1` a `myVM2` . Také vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

Vytvořit myVM1 (primární):
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

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Všimněte si hodnoty `publicIpAddress` . Tuto adresu použijete pro přístup k virtuálnímu počítači.

```output
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

Vytvořit myVM2 (pohotovostní):

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

Všimněte si hodnoty `publicIpAddress` po vytvoření myVM2.

### <a name="open-the-tcp-port-for-connectivity"></a>Otevřete port TCP pro připojení.

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

```output
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

Otevřete port pro myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. Nahraďte IP adresu `publicIpAddress` hodnotou svého virtuálního počítače.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Vytvoření databáze na myVM1 (primární)

Na imagi Marketplace už je nainstalovaný software Oracle, takže dalším krokem je instalace databáze. 

Přepněte na uživatele Oracle:

```bash
$ sudo su - oracle
```

Vytvořte databázi:

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

Výstupy by měly vypadat podobně jako na následující reakci:

```output
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

Nastavte proměnné ORACLE_SID a ORACLE_HOME:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Volitelně můžete do souboru/Home/Oracle/.bashrc přidat ORACLE_HOME a ORACLE_SID, aby se tato nastavení uložila pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Konfigurace ochrany dat

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Povolit režim protokolu archivace na myVM1 (primární)

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

Povolte protokolování vynucení a ujistěte se, že je k dispozici alespoň jeden soubor protokolu:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Vytvořte pohotovostní protokoly opětovného přihlášení a nastavte stejnou velikost a množství jako v případě protokolů opětovného provedení primární databáze:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 200M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 200M;
```

Zapněte Flashback (což usnadňuje obnovení) a nastavte pohotovostní \_ \_ správu souborů na auto. Ukončete SQL * plus a potom.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Nastavení služby v myVM1 (primární)

Upravte nebo vytvořte soubor souboru Tnsnames. Ora, který je ve složce $ORACLE _HOME \network\admin.

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

Upravte nebo vytvořte soubor naslouchacího procesu. Ora, který je ve složce $ORACLE _HOME \network\admin.

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

Povolit zprostředkovatele ochrany dat:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

Spusťte naslouchací proces:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Nastavení služby v myVM2 (pohotovostní)

SSH pro myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Přihlaste se jako Oracle:

```bash
$ sudo su - oracle
```

Upravte nebo vytvořte soubor souboru Tnsnames. Ora, který je ve složce $ORACLE _HOME \network\admin.

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

Upravte nebo vytvořte soubor naslouchacího procesu. Ora, který je ve složce $ORACLE _HOME \network\admin.

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

Spusťte naslouchací proces:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Obnovení databáze do myVM2 (pohotovostní)

Vytvořte soubor parametrů/TMP/initcdb1_stby. Ora s následujícím obsahem:

```bash
*.db_name='cdb1'
```

Vytvořit složky:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Vytvořte soubor hesla:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Spusťte databázi na myVM2:

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

V RMAN spusťte následující příkazy:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Po dokončení příkazu byste měli vidět zprávy podobné následujícímu. Ukončete RMAN.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

Volitelně můžete do souboru/Home/Oracle/.bashrc přidat ORACLE_HOME a ORACLE_SID, aby se tato nastavení uložila pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Povolit zprostředkovatele ochrany dat:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Konfigurace zprostředkovatele ochrany dat v myVM1 (primární)

Spusťte Správce ochrany dat a přihlaste se pomocí SYS a hesla. (Nepoužívejte ověřování operačního systému.) Proveďte následující:

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

Dokončili jste instalaci Oracle data Guard. V další části se dozvíte, jak otestovat připojení a přepnout přes.

### <a name="connect-the-database-from-the-client-machine"></a>Připojení databáze z klientského počítače

Aktualizujte nebo vytvořte soubor souboru Tnsnames. Ora na klientském počítači. Tento soubor je obvykle ve $ORACLE _HOME \network\admin.

Nahraďte IP adresy `publicIpAddress` hodnotami pro myVM1 a myVM2:

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

Spusťte SQL * plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Test konfigurace ochrany dat

### <a name="switch-over-the-database-on-myvm1-primary"></a>Přepínání databáze na myVM1 (primární)

Přepnutí z primárního na pohotovostní (cdb1 na cdb1_stby):

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

Nyní se můžete připojit k pohotovostní databázi.

Spusťte SQL * plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Přepínání databáze na myVM2 (pohotovostní)

Chcete-li přepnout, spusťte na myVM2 následující:

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

Nyní byste měli být schopni se připojit k primární databázi.

Spusťte SQL * plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Dokončili jste instalaci a konfiguraci ochrany dat v Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Odstraňte virtuální počítač

Pokud už virtuální počítač nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít následující příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)

[Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
