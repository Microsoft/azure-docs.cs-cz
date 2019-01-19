---
title: Implementace brány Golden Oracle na virtuálním počítači Azure s Linuxem | Dokumentace Microsoftu
description: Rychlé zprovoznění brány Golden Oracle nahoru v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: cb5ad6f351cdeadb999b1a62c225f23937c5b92e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413817"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementace brány Golden Oracle na virtuálním počítači Azure s Linuxem 

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka podrobně popisuje, jak pomocí Azure CLI nasadit databázi Oracle 12c z image Galerie Azure Marketplace. 

Tento dokument popisuje krok za krokem k vytvoření, nainstalovat a nakonfigurovat bránu Golden Oracle na Virtuálním počítači Azure.

Než začnete, ujistěte se, že je rozhraní Azure CLI nainstalované. Další informace najdete v tématu [Průvodce instalací Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

K provedení instalace brány Golden Oracle, musíte vytvořit dva virtuální počítače Azure ve stejné skupině dostupnosti. Image Marketplace, který použijete k vytvoření virtuálních počítačů je **Oracle: Oracle – databáze-Ee:12.1.0.2:latest**.

Také musíte znát Unix editoru vi a mít základní znalosti o x11 (Windows X).

Následuje souhrn konfigurace prostředí:
> 
> |  | **Primární lokalita** | **Replikace webu** |
> | --- | --- | --- |
> | **Databáze Oracle** |Oracle 12c verze 2 – (12.1.0.2) |Oracle 12c verze 2 – (12.1.0.2)|
> | **Název počítače** |myVM1 |myVM2 |
> | **Operační systém** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Replikace schématu** |TEST|TEST |
> | **Brána Golden vlastníka/replikace** |C##GGADMIN |REPUSER |
> | **Proces Golden brány** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Přihlásit se k Azure 

Přihlaste se k předplatnému Azure pomocí [az login](/cli/azure/reference-index#az_login) příkazu. Potom postupujte podle na obrazovce pokynů.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, do které prostředky Azure se nasadí a od které bylo možné je spravovat. 

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Následující krok je volitelný, ale doporučené. Další informace najdete v tématu [skupiny dostupnosti Azure průvodce](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm#az_vm_create). 

Následující příklad vytvoří dva virtuální počítače s názvem `myVM1` a `myVM2`. Vytvoření klíčů SSH, pokud ještě neexistují ve výchozím umístění klíčů. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Vytvoření myVM1 (primární):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Po vytvoření virtuálního počítače Azure CLI zobrazí podobné informace jako v následujícím příkladu. (Povšimněte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.)

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

#### <a name="create-myvm2-replicate"></a>Vytvoření myVM2 (replikace):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Poznamenejte si `publicIpAddress` i po jeho vytvoření.

### <a name="open-the-tcp-port-for-connectivity"></a>Otevřete port TCP pro připojení

Dalším krokem je konfigurace externí koncové body, které vám umožní vzdálený přístup k databázi Oracle. Pokud chcete nakonfigurovat externí koncové body, spusťte následující příkazy.

#### <a name="open-the-port-for-myvm1"></a>Otevření portu pro myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Výsledky by měly vypadat podobně jako následující odpověď:

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

#### <a name="open-the-port-for-myvm2"></a>Otevření portu pro myVM2:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci SSH. IP adresu nahraďte pomocí adresy `publicIpAddress` vašeho virtuálního počítače.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Vytvořit databázi na myVM1 (primární)

Oracle software je již nainstalována na Marketplace image, dalším krokem je nainstalovat databázi. 

Spuštění softwaru jako superuživatele "oracle":

```bash
sudo su - oracle
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
Výstupy by měla vypadat podobně jako následující odpověď:

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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Nastavení proměnných ORACLE_SID a ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Volitelně můžete přidat ORACLE_HOME a ORACLE_SID do souboru .bashrc, aby tato nastavení se uloží pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Spuštění naslouchacího procesu Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Vytvořit databázi na myVM2 (replikace)

```bash
sudo su - oracle
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
Nastavení proměnných ORACLE_SID a ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Volitelně můžete přidané ORACLE_HOME a ORACLE_SID do souboru .bashrc, aby tato nastavení se uloží pro budoucí přihlášení.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Spuštění naslouchacího procesu Oracle
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurace brány Golden 
Konfigurace brány Golden, proveďte kroky v této části.

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
Povolení protokolování platnost a ujistěte se, že je k dispozici aspoň jeden soubor protokolu.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Stáhnout software Golden brány
Chcete-li stáhnout a příprava softwaru Oracle Golden brány, proveďte následující kroky:

1. Stáhněte si **fbo_ggs_Linux_x64_shiphome.zip** soubor [stránku pro stažení brány Golden Oracle](http://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Pod nadpisem stahování **12.x.x.x Oracle GoldenGate pro Oracle Linux x86 – x 64**, měla by existovat sadu souborů ZIP ke stažení.

2. Po stažení souborů ZIP na klientském počítači, zkopírujte soubory do virtuálního počítače pomocí protokolu Secure Copy (SCP):

  ```bash
  $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
  ```

3. Přesunout soubory .zip **/ opt** složky. Potom změníte vlastníka souborů následujícím způsobem:

  ```bash
  $ sudo su -
  # mv <folder>/*.zip /opt
  ```

4. Rozbalte soubory (instalace Linuxu rozbalte nástroj, pokud ještě není nainstalovaná):

  ```bash
  # yum install unzip
  # cd /opt
  # unzip fbo_ggs_Linux_x64_shiphome.zip
  ```

5. Změna oprávnění:

  ```bash
  # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
  ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Připravit klienta a virtuální počítač běžel x11 (pouze klienti Windows)
Tento krok je volitelný. Tento krok přeskočit, pokud používáte klienta Linux nebo již x11 instalační program.

1. Stáhněte si PuTTY a Xming do počítače s Windows:

  * [Stáhněte si PuTTY](http://www.putty.org/)
  * [Stáhněte si Xming](https://xming.en.softonic.com/)

2.  Po instalaci klienta PuTTY, ve složce PuTTY (například C:\Program Files\PuTTY), spusťte puttygen.exe (generátor klíče PuTTY).

3.  V PuTTY Key Generator:

  - Chcete-li vygenerovat klíč, vyberte **generovat** tlačítko.
  - Zkopírujte obsah klíče (**Ctrl + C**).
  - Vyberte **uložit privátní klíč** tlačítko.
  - Ignorovat upozornění, která se zobrazí a pak vyberte **OK**.

    ![Snímek obrazovky stránky PuTTY key generator](./media/oracle-golden-gate/puttykeygen.png)

4.  Ve virtuálním počítači spusťte tyto příkazy:

  ```bash
  # sudo su - oracle
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

5. Vytvořte soubor s názvem **authorized_keys**. Vložte obsah klíče v tomto souboru a pak soubor uložte.

  > [!NOTE]
  > Klíč musí obsahovat řetězce `ssh-rsa`. Obsah klíče musí být také jeden řádek textu.
  >  

6. Spusťte PuTTY. V **kategorie** vyberte **připojení** > **SSH** > **Auth**. V **soubor privátního klíče pro ověřování** pole, přejděte na klíč, který jste vygenerovali dříve.

  ![Snímek obrazovky stránky nastavit privátní klíč](./media/oracle-golden-gate/setprivatekey.png)

7. V **kategorie** vyberte **připojení** > **SSH** > **X11**. Vyberte **povolit X11 předávání** pole.

  ![Snímek obrazovky stránky povolit X11](./media/oracle-golden-gate/enablex11.png)

8. V **kategorie** podokně přejděte na **relace**. Zadejte informace o hostiteli a pak vyberte **otevřít**.

  ![Snímek obrazovky stránky relace](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instalace softwaru Golden brány

Instalace brány Golden Oracle, proveďte následující kroky:

1. Přihlaste se jako oracle. (Musí být schopni se přihlásit bez zobrazování výzev k zadání hesla.) Ujistěte se, že Xming běží před zahájením instalace.
 
  ```bash
  $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
  $ ./runInstaller
  ```
2. Vyberte "Oracle GoldenGate pro Oracle Database 12c". Potom vyberte **Další** pokračujte.

  ![Snímek obrazovky stránky instalace vyberte Instalační program](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Změňte umístění softwaru. Vyberte **spustit správce** pole a zadejte umístění databáze. Pokračujte výběrem tlačítka **Next** (Další).

  ![Snímek obrazovky stránky vyberte instalace](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Změňte adresář inventáře a pak vyberte **Další** pokračujte.

  ![Snímek obrazovky stránky vyberte instalace](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na **Souhrn** obrazovky, vyberte **nainstalovat** pokračujte.

  ![Snímek obrazovky stránky instalace vyberte Instalační program](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Můžete být vyzváni ke spuštění skriptu jako "root". Pokud ano, otevřete samostatnou relaci ssh k virtuálnímu počítači, sudo do kořenového adresáře a potom spusťte skript. Vyberte **OK** pokračovat.

  ![Snímek obrazovky stránky vyberte instalace](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Po dokončení instalace, vybrat **Zavřít** proces dokončete.

  ![Snímek obrazovky stránky vyberte instalace](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Nastavení služby Azure na myVM1 (primární)

1. Vytvořit nebo aktualizovat souboru tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Vytvoření brány Golden vlastníka a k uživatelským účtům.

  > [!NOTE]
  > Vlastník účtu musí mít předponu C ##.
  >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Vytvoření brány Golden testovací uživatelský účet:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> @demo_ora_insert
  SQL> EXIT;
  ```

4. Nakonfigurujte extrahovat soubor parametrů.

 Spuštění rozhraní příkazového řádku zlaté brány (ggsci):

  ```bash
  $ sudo su - oracle
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
  Successfully logged into database  pdb1
  GGSCI>  ADD SCHEMATRANDATA pdb1.test
  2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
  2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

  GGSCI> EDIT PARAMS EXTORA
  ```
5. Přidejte následující k EXTRAHOVAT parametr soubor (pomocí editoru vi příkazů). Stiskněte klávesu Esc, ": QW!" Uložte soubor. 

  ```bash
  EXTRACT EXTORA
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTRAIL ./dirdat/rt  
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT 
  LOGALLSUPCOLS
  UPDATERECORDFORMAT COMPACT
  TABLE pdb1.test.TCUSTMER;
  TABLE pdb1.test.TCUSTORD;
  ```
6. Registr extrahovat – integrované extrakce:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci

  GGSCI> dblogin userid C##GGADMIN, password ggadmin
  Successfully logged into database CDB$ROOT.

  GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

  2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

  GGSCI> exit
  ```
7. Nastavit kontrolní body extrakce a spustit v reálném čase extrakce:

  ```bash
  $ ./ggsci
  GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
  EXTRACT (Integrated) added.

  GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
  RMTTRAIL added.

  GGSCI>  START EXTRACT EXTORA

  Sending START request to MANAGER ...
  EXTRACT EXTORA starting

  GGSCI > info all

  Program     Status      Group       Lag at Chkpt  Time Since Chkpt

  MANAGER     RUNNING
  EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
  ```
V tomto kroku zjistíte, počáteční oznámení změny stavu, který se použije později v jiné části:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> SELECT current_scn from v$database;
  CURRENT_SCN
  -----------
      1857887
  SQL> EXIT;
  ```

  ```bash
  $ ./ggsci
  GGSCI> EDIT PARAMS INITEXT
  ```

  ```bash
  EXTRACT INITEXT
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTASK REPLICAT, GROUP INITREP
  TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
  ```

  ```bash
  GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
  ```

### <a name="set-up-service-on-myvm2-replicate"></a>Nastavení služby Azure na myVM2 (replikace)


1. Vytvořit nebo aktualizovat souboru tnsnames.ora:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Vytvoření replikace účtu:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> create user repuser identified by rep_pass container=current;
  SQL> grant dba to repuser;
  SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
  SQL> connect repuser/rep_pass@pdb1 
  SQL> EXIT;
  ```

3. Vytvoření brány Golden testovací uživatelský účet:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> EXIT;
  ```

4. Soubor parametrů REPLICAT replikovat změny: 

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS REPORA  
  ```
  Obsah souboru parametrů REPORA:

  ```bash
  REPLICAT REPORA
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT
  DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;
  ```

5. Nastavte kontrolní bod replicat:

  ```bash
  GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
  GGSCI> EDIT PARAMS INITREP

  ```

  ```bash
  REPLICAT INITREP
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;   
  ```

  ```bash
  GGSCI> ADD REPLICAT INITREP, SPECIALRUN
  ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>Nastavení replikace (myVM1 a myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. Nastavení replikace na myVM2 (replikace)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Aktualizace souboru následujícím kódem:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Restartujte službu správce:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Nastavení replikace na myVM1 (primární)

Spusťte počátečním načtení a zkontrolujte chyby:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Nastavení replikace na myVM2 (replikace)

Změna oznámení změny stavu číslo číslem, které jste předtím získali:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
Zahájení replikace a ji můžete otestovat pomocí vkládání nových záznamů do tabulek testu.


### <a name="view-job-status-and-troubleshooting"></a>Zobrazení stavu úlohy a řešení potíží

#### <a name="view-reports"></a>Zobrazení sestav
Chcete-li zobrazit sestavy na myVM1, spusťte následující příkazy:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Chcete-li zobrazit sestavy na myVM2, spusťte následující příkazy:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Zobrazit stav a historie
Chcete-li zobrazit stav a historie na myVM1, spusťte následující příkazy:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Chcete-li zobrazit stav a historie na myVM2, spusťte následující příkazy:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Tím dokončíte instalaci a konfiguraci brány Golden v Oracle linuxu.


## <a name="delete-the-virtual-machine"></a>Odstranění virtuálního počítače

Pokud už je nepotřebujete, slouží následující příkaz k odebrání skupiny prostředků, virtuálního počítače a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

[Kurz vytvoření virtuálního počítače s vysokou dostupností](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky nasazení virtuálního počítače pomocí rozhraní příkazového řádku](../../linux/cli-samples.md)
