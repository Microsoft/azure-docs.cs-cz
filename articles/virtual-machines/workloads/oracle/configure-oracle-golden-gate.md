---
title: Implementace oracle golden gate na virtuálním počítači Azure Linux | Dokumenty společnosti Microsoft
description: Ve svém prostředí Azure rychle zprovozněte bránu Oracle Golden Gate.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: ae6bfb0ab0208d0f778476c9f0959b0c0f1d6471
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683720"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Implementace oracle golden gate na virtuálním počítači Azure Linux 

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. Tato příručka podrobně popisuje, jak pomocí příkazového příkazového příkazu Azure nasadit databázi Oracle 12c z image galerie Azure Marketplace. 

Tento dokument vám ukáže krok za krokem, jak vytvořit, nainstalovat a nakonfigurovat Oracle Golden Gate na virtuálním počítači Azure. V tomto kurzu jsou dva virtuální počítače nastaveny v sadě dostupnosti v jedné oblasti. Stejný kurz lze použít k nastavení OracleGolden Gate pro virtuální počítače v různých zónách dostupnosti v jedné oblasti Azure nebo pro nastavení virtuálních počítačích ve dvou různých oblastech.

Než začnete, ujistěte se, že je rozhraní Azure CLI nainstalované. Další informace najdete v tématu [Průvodce instalací Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

Chcete-li provést instalaci Oracle Golden Gate, musíte vytvořit dva virtuální počítače Azure ve stejné sadě dostupnosti. Image Marketplace, kterou používáte k vytvoření virtuálních aplikací, je **Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Musíte se také seznámit s Unix editorem vi a mít základní znalosti o x11 (X Windows).

Následuje souhrn konfigurace prostředí:
> 
> |  | **Primární lokalita** | **Replikovat lokalitu** |
> | --- | --- | --- |
> | **Vydání oracle** |Oracle 12c Verze 2 – (12.1.0.2) |Oracle 12c Verze 2 – (12.1.0.2)|
> | **Název stroje** |myVM1 |myVM2 |
> | **Operační systém** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Schéma replikace** |TEST|TEST |
> | **Majitel/replika cenárské brány Golden Gate** |C##GGADMIN |REPUSER |
> | **Proces Zlaté brány** |EXTORA |Repora|


### <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se ke svému předplatnému Azure pomocí příkazu [az login.](/cli/azure/reference-index) Poté postupujte podle pokynů na obrazovce.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, do kterého se nasazují prostředky Azure a ze kterého se dají spravovat. 

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup` v umístění `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Následující krok je volitelný, ale doporučený. Další informace najdete v [tématu Azure dostupnost sady průvodce](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). 

Následující příklad vytvoří dva virtuální `myVM1` `myVM2`hod s názvem a . Vytvořte klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.

#### <a name="create-myvm1-primary"></a>Vytvořit myVM1 (primární):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Po vytvoření virtuálního počítače azure CLI zobrazí informace podobné v následujícím příkladu. (Vezměte na `publicIpAddress`vědomí . Tato adresa se používá pro přístup k virtuálnímu virtuálnímu serveru.)

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

#### <a name="create-myvm2-replicate"></a>Vytvořit myVM2 (replikovat):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Poznamenejte `publicIpAddress` si také poté, co byl vytvořen.

### <a name="open-the-tcp-port-for-connectivity"></a>Otevření portu TCP pro připojení

Dalším krokem je konfigurace externích koncových bodů, které umožňují vzdálený přístup k databázi Oracle. Chcete-li konfigurovat externí koncové body, spusťte následující příkazy.

#### <a name="open-the-port-for-myvm1"></a>Otevřete port pro myVM1:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Výsledky by měly vypadat podobně jako následující odpověď:

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

#### <a name="open-the-port-for-myvm2"></a>Otevřete port myVM2:

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

### <a name="create-the-database-on-myvm1-primary"></a>Vytvoření databáze na myVM1 (primární)

Software Oracle je již nainstalován na bitové kopii Marketplace, takže dalším krokem je instalace databáze. 

Spusťte software jako superuser "oracle":

```bash
sudo su - oracle
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

Výstupy by měly vypadat podobně jako následující odpověď:

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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Nastavte proměnné ORACLE_SID a ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Volitelně můžete do souboru .bashrc přidat ORACLE_HOME a ORACLE_SID, aby byla tato nastavení uložena pro budoucí přihlášení:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Spuštění posluchače Oracle

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Vytvoření databáze na myVM2 (replikovat)

```bash
sudo su - oracle
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

Nastavte proměnné ORACLE_SID a ORACLE_HOME.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Volitelně můžete přidat ORACLE_HOME a ORACLE_SID do souboru .bashrc, aby tato nastavení byla uložena pro budoucí přihlášení.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Spuštění posluchače Oracle

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Konfigurace Zlaté brány 
Chcete-li nakonfigurovat Golden Gate, postupujte podle pokynů v této části.

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Povolit režim protokolu archivu na myVM1 (primární)

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
Povolte protokolování vysilování a ujistěte se, že je k dispozici alespoň jeden soubor protokolu.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Stažení Softwaru Golden Gate
Chcete-li stáhnout a připravit software Oracle Golden Gate, proveďte následující kroky:

1. Stáhněte si soubor **fbo_ggs_Linux_x64_shiphome.zip** ze [stránky pro stažení Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). Pod názvem ke stažení **Oracle GoldenGate 12.x.x.x pro Oracle Linux x86-64**by měla být sada souborů ZIP ke stažení.

2. Po stažení souborů ZIP do klientského počítače zkopírujte soubory do virtuálního počítače pomocí protokolu SCP (Secure Copy Protocol):

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Přesuňte soubory ZIP do složky **/opt.** Potom změňte vlastníka souborů takto:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Rozbalte soubory (nainstalujte nástroj Pro rozbalit Linux, pokud ještě není nainstalován):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Změnit oprávnění:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Příprava klienta a virtuálního počítače ke spuštění x11 (jenom pro klienty Windows)
Jedná se o volitelný krok. Tento krok můžete přeskočit, pokud používáte klienta Linux nebo již máte nastavení x11.

1. Stáhněte si PuTTY a Xming do počítače se systémem Windows:

   * [Stažení PuTTY](https://www.putty.org/)
   * [Stažení Xming](https://xming.en.softonic.com/)

2. Po instalaci PuTTY, ve složce PuTTY (například C:\Program Files\PuTTY), spusťte puttygen.exe (PuTTY Key Generator).

3. V PutTY Key Generator:

   - Chcete-li generovat klíč, vyberte **tlačítko Generovat.**
   - Zkopírujte obsah klávesy (**Ctrl+C).**
   - Vyberte tlačítko **Uložit soukromý klíč.**
   - Ignorujte upozornění, které se zobrazí, a pak vyberte **OK**.

   ![Snímek obrazovky se stránkou generátoru kláves PuTTY](./media/oracle-golden-gate/puttykeygen.png)

4. Ve virtuálním počítači spusťte tyto příkazy:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. Vytvořte soubor s názvem **authorized_keys**. Vložte obsah klíče do tohoto souboru a uložte soubor.

   > [!NOTE]
   > Klíč musí obsahovat `ssh-rsa`řetězec . Obsah klíče musí být také jeden řádek textu.
   >  

6. Spusťte PuTTY. V podokně **Kategorie** vyberte **možnost Připojení** > **SSH** > **Auth**. V **souboru soukromého klíče pro ověřování** přejděte na klíč, který jste vygenerovali dříve.

   ![Snímek obrazovky se stránkou Nastavit soukromý klíč](./media/oracle-golden-gate/setprivatekey.png)

7. V podokně **Kategorie** vyberte **možnost Připojení** > **SSH** > **X11**. Pak vyberte pole **Povolit přesměrování X11.**

   ![Snímek obrazovky se stránkou Povolit X11](./media/oracle-golden-gate/enablex11.png)

8. V podokně **Kategorie** přejděte na **Session**. Zadejte informace o hostiteli a pak vyberte **Otevřít**.

   ![Snímek obrazovky se stránkou relace](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Instalace softwaru Golden Gate

Chcete-li nainstalovat službu Oracle Golden Gate, proveďte následující kroky:

1. Přihlaste se jako věštec. (Měli byste být schopni se přihlásit, aniž byste byli vyzváni k zadání hesla.) Před zahájením instalace se ujistěte, že je spuštěn a spuštěn systém Xming.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Vyberte možnost Oracle GoldenGate for Oracle Database 12c. Pak pokračujte výběrem možnosti **Další.**

   ![Snímek obrazovky se stránkou Vybrat instalaci instalačního programu](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Změňte umístění softwaru. Pak vyberte pole **Správce startů** a zadejte umístění databáze. Pokračujte výběrem tlačítka **Next** (Další).

   ![Snímek obrazovky se stránkou Vybrat instalaci](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Změňte adresář inventáře a pokračujte vyberte **Další.**

   ![Snímek obrazovky se stránkou Vybrat instalaci](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Na obrazovce **Souhrn** pokračujte výběrem **možnosti Instalovat.**

   ![Snímek obrazovky se stránkou Vybrat instalaci instalačního programu](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Můžete být vyzváni ke spuštění skriptu jako 'root'. Pokud ano, otevřete samostatnou relaci, ssh na virtuální ms, sudo root a pak spusťte skript. Vyberte **OK** pokračovat.

   ![Snímek obrazovky se stránkou Vybrat instalaci](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Po dokončení instalace, vyberte **Zavřít** k dokončení procesu.

   ![Snímek obrazovky se stránkou Vybrat instalaci](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Nastavit službu na myVM1 (primární)

1. Vytvořte nebo aktualizujte soubor tnsnames.ora:

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

2. Vytvořte vlastníka a uživatelské účty Golden Gate.

   > [!NOTE]
   > Účet vlastníka musí mít předponu C##.
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

3. Vytvořte testovací uživatelský účet Golden Gate:

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

4. Nakonfigurujte soubor parametrů extraktu.

   Spusťte rozhraní příkazového řádku Zlaté brány (ggsci):

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

5. Přidejte následující do souboru parametrů EXTRACT (pomocí příkazů vi). Stiskněte klávesu Esc, ':wq!' pro uložení souboru. 

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

6. Registrovat výpis -- integrovaný výpis:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Nastavte kontrolní body a začněte extrahovat v reálném čase:

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

   V tomto kroku najdete počáteční scn, který bude použit později, v jiné části:

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

### <a name="set-up-service-on-myvm2-replicate"></a>Nastavit službu na myVM2 (replikovat)


1. Vytvořte nebo aktualizujte soubor tnsnames.ora:

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

2. Vytvoření replikovaného účtu:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Vytvořte si uživatelský účet pro testování Golden Gate:

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

4. Soubor parametrů REPLICAT pro replikaci změn: 

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

5. Nastavení replikovaného kontrolního bodu:

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

Aktualizujte soubor následujícím:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Potom restartujte službu Správce:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Nastavení replikace na myVM1 (primární)

Spusťte počáteční zatížení a zkontrolujte chyby:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. Nastavení replikace na myVM2 (replikace)

Změňte číslo scn s číslem, které jste získali před:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

Replikace byla zahájena a můžete ji otestovat vložením nových záznamů do testovacích tabulek.


### <a name="view-job-status-and-troubleshooting"></a>Zobrazit stav úlohy a řešení potíží

#### <a name="view-reports"></a>Zobrazení sestav
Chcete-li zobrazit sestavy na myVM1, spusťte následující příkazy:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
Chcete-li zobrazit sestavy myVM2, spusťte následující příkazy:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Zobrazit stav a historii
Chcete-li zobrazit stav a historii na myVM1, spusťte následující příkazy:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

Chcete-li zobrazit stav a historii na myVM2, spusťte následující příkazy:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Tím je dokončena instalace a konfigurace golden gate na Oracle Linux.


## <a name="delete-the-virtual-machine"></a>Odstraňte virtuální počítač

Když už není potřeba, následující příkaz lze odebrat skupinu prostředků, virtuální hod a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz vytvoření virtuálního počítače s vysokou dostupností](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky nasazení virtuálního počítače pomocí rozhraní příkazového řádku](../../linux/cli-samples.md)
