---
title: Zálohování a obnovení databáze Oracle Database 19c na virtuálním počítači Azure Linux pomocí Azure Backup
description: Naučte se, jak zálohovat a obnovit Oracle Database databázi 19c pomocí služby Azure Backup.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673898"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Zálohování a obnovení databáze Oracle Database 19c na virtuálním počítači Azure Linux pomocí Azure Backup

V tomto článku se dozvíte, jak Azure Backup využít snímky disků virtuálních počítačů, které zahrnují soubory databáze a oblast pro rychlé obnovení. Pomocí Azure Backup můžete provádět úplné snímky disků vhodné jako zálohy, které jsou uložené v [trezoru Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md).  Azure Backup taky poskytuje zálohy konzistentní s aplikacemi, které zajistí, že se pro obnovení dat nevyžadují další opravy. Obnovování dat konzistentních s aplikací zkracuje čas obnovení, což vám umožní rychle se vrátit do funkčního stavu.

> [!div class="checklist"]
>
> * Zálohování databáze pomocí zálohování konzistentního vzhledem k aplikacím
> * Obnovení a obnovení databáze z bodu obnovení
> * Obnovení virtuálního počítače z bodu obnovení

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

Chcete-li provést proces zálohování a obnovení, je třeba nejprve vytvořit virtuální počítač se systémem Linux s nainstalovanou instancí Oracle Database 19c. Image Marketplace, která se právě používá k vytvoření virtuálního počítače, je  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: nejnovější**. Postupujte podle kroků v [rychlém startu k vytvoření databáze Oracle](./oracle-database-quick-create.md) a vytvořte databázi Oracle pro dokončení tohoto kurzu.


## <a name="prepare-the-environment"></a>Příprava prostředí

Pro přípravu prostředí proveďte tyto kroky:

1. Připojte se k virtuálnímu počítači.
1. Připravte databázi.

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Pokud chcete vytvořit relaci Secure Shell (SSH) s virtuálním počítačem, použijte následující příkaz. Nahraďte IP adresu a název hostitele kombinací `<publicIpAddress>` hodnoty pro váš virtuální počítač.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. Přepněte na uživatele *root* :

   ```bash
   sudo su -
   ```
    
1. Přidejte uživatele Oracle do souboru */etc/sudoers* :

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>Příprava databáze

Tento krok předpokládá, že máte instanci Oracle (*test*), která je spuštěná na virtuálním počítači s názvem *vmoracle19c*.

1. Přepněte uživatele na uživatele *Oracle* :
 
   ```bash
    sudo su - oracle
    ```
    
2. Než se připojíte, musíte nastavit proměnnou prostředí ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```

    Měli byste taky přidat ORACLE_SID proměnnou do `oracle` `.bashrc` souboru uživatelů pro budoucí přihlášení pomocí následujícího příkazu:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Spusťte naslouchací proces Oracle, pokud ještě není spuštěný:

    ```output
    $ lsnrctl start
    ```

    Výstup by měl vypadat zhruba jako v tomto příkladu:

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  Vytvořte umístění oblasti rychlé obnovy (v/v):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  Připojit k databázi:

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  Spusťte databázi, pokud ještě není spuštěná:

    ```bash
    SQL> startup
    ```

7.  Nastavte proměnné prostředí databáze pro oblast rychlé obnovení:

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  Zajistěte, aby byla databáze v režimu protokolu archivace, aby bylo možné povolit online zálohování.

    Nejprve ověřte stav archivu protokolu:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    Pokud je v režimu NOARCHIVELOG, spusťte následující příkazy:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  Vytvořte tabulku pro otestování operací zálohování a obnovení:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. Nakonfigurujte RMAN pro zálohování do oblasti rychlého obnovení, která se nachází na disku virtuálního počítače:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. Potvrďte podrobnosti změny konfigurace:

    ```bash
    RMAN> show all;
    ```    

12.  Nyní spusťte zálohování. Následující příkaz provede úplnou zálohu databáze, včetně archivovaných protokolů, jako záložní soubory v komprimovaném formátu:

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Použití Azure Backup (Preview)

Služba Azure Backup poskytuje jednoduchá, zabezpečená a cenově výhodná řešení pro zálohování vašich dat a jejich obnovení z cloudu Microsoft Azure. Azure Backup poskytuje nezávislé a izolované zálohy pro ochranu proti náhodnému zničení původních dat. Zálohy jsou uloženy v trezoru služby Recovery Services s integrovanou správou bodů obnovení. Konfigurace a škálovatelnost jsou jednoduché, zálohy jsou optimalizované a můžete je snadno obnovit podle potřeby.

Služba Azure Backup poskytuje [rozhraní](../../../backup/backup-azure-linux-app-consistent.md) pro zajištění konzistence aplikací během zálohování virtuálních počítačů s Windows a Linux pro různé aplikace, jako je Oracle, MySQL, Mongo DB a PostGreSQL. To zahrnuje vyvolání předzálohovacího skriptu (k nečinnosti aplikací) před pořizováním snímku disků a voláním následného skriptu (příkazy pro uvolnění aplikací) po dokončení snímku, aby se aplikace vracely do normálního režimu. I když je na GitHubu k dispozici ukázka předzálohovacích a potištěných skriptů, je vytváření a údržba těchto skriptů vaše zodpovědnost.

Nyní Azure Backup poskytuje vylepšené předzálohovací skripty a skripty po skriptu (**které jsou aktuálně ve verzi Preview**), kde služba Azure Backup poskytne zabalené předběžné skripty a následné skripty pro vybrané aplikace. Azure Backup uživatelé pouze potřebují aplikaci pojmenovat a pak zálohování virtuálních počítačů Azure automaticky vyvolá příslušné skripty před odesláním. Zabalené předzálohovací skripty a post-Script budou spravovány Azure Backup týmem, aby se uživatelé mohli spolehnout na podporu, vlastnictví a platnost těchto skriptů. V současné době jsou podporované aplikace pro vylepšené rozhraní *Oracle* a *MySQL*.

V této části použijete Azure Backup Enhanced Framework k tomu, aby se snímky konzistentní vzhledem k aplikacím používaly ve spuštěném virtuálním počítači a databázi Oracle. Databáze bude umístěna do režimu zálohování, což umožňuje, aby při Azure Backup pořizování snímku disků virtuálních počítačů probíhalo reakční konzistentní online zálohování. Snímek bude úplnou kopií úložiště, a ne přírůstkovým nebo kopírovacím snímkem zápisu, takže se jedná o efektivní médium pro obnovení databáze z. Výhodou použití Azure Backup snímků konzistentních vzhledem k aplikacím je to, že jsou extrémně rychlé bez ohledu na to, jak velká je databáze, a snímek lze použít pro operace obnovení ihned po jejich pořízení, aniž by bylo nutné čekat na přenos do trezoru Recovery Services.

Pokud chcete použít Azure Backup k zálohování databáze, proveďte tyto kroky:

1. Připravte prostředí pro zálohování konzistentní vzhledem k aplikacím.
1. Nastavte zálohy konzistentní s aplikací.
1. Spusťte zálohování virtuálního počítače konzistentního vzhledem k aplikacím.

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>Příprava prostředí pro zálohování konzistentní vzhledem k aplikacím

1. Přepněte na uživatele *root* :

   ```bash
   sudo su -
   ```

1. Vytvořit nového uživatele zálohování:

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. Nastavte zálohovací prostředí uživatele:

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. Nastavte externí ověřování pro nového uživatele zálohování. Uživatel zálohování musí mít přístup k databázi pomocí externího ověřování, takže nemusíte být vyzváni heslem.

   Nejprve přepněte zpět na uživatele *Oracle* :

   ```bash
   su - oracle
   ```

   Přihlaste se k databázi pomocí sqlplus a podívejte se na výchozí nastavení pro externí ověřování:
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   Výstup by měl vypadat jako v tomto příkladu: 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   Nyní vytvořte uživatele databáze *azbackup* ověřený externě a udělte oprávnění sysbackup:
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > Pokud při `ORA-46953: The password file is not in the 12.2 format.`  spuštění `GRANT` příkazu dojde k chybě, postupujte podle těchto kroků a migrujte soubor orapwd do formátu 12,2:
   >
   > 1. Ukončete sqlplus.
   > 1. Přesuňte soubor hesla ve starém formátu do nového názvu.
   > 1. Migrujte soubor hesla.
   > 1. Odeberte starý soubor.
   > 1. Spusťte následující příkaz:
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. Spusťte `GRANT` operaci znovu v sqlplus.
   >
   
4. Vytvořte uloženou proceduru pro protokolování zpráv o zálohování do protokolu upozornění databáze:

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>Nastavení zálohování konzistentního vzhledem k aplikacím  

1. Přepněte na uživatele *root* :

   ```bash
   sudo su -
   ```

2. Vyhledejte složku atd/Azure. Pokud není k dispozici, vytvořte pracovní adresář zálohování konzistentní vzhledem k aplikacím:

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. Vyhledejte ve složce úlohu. conf. Pokud není k dispozici, vytvořte soubor v adresáři *složce/etc/Azure* s názvem *úlohy. conf* s následujícím obsahem, který musí začínat na `[workload]` . Pokud soubor již existuje, stačí upravit pole tak, aby odpovídalo následujícímu obsahu. V opačném případě následující příkaz vytvoří soubor a naplní obsah:

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>Aktivace zálohy virtuálního počítače konzistentního vzhledem k aplikacím

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1.  V Azure Portal přejděte do skupiny prostředků **RG-Oracle** a klikněte na **vmoracle19c** virtuálního počítače.

2.  V okně **zálohování** vytvořte nový **trezor Recovery Services** ve skupině prostředků **RG-Oracle** s názvem **myVault**.
    Pro **možnost zvolit zásadu zálohování** použijte **(New) DailyPolicy**. Pokud chcete změnit frekvenci zálohování nebo rozsah uchování, vyberte místo toho **vytvořit novou zásadu** .

    ![Přidat stránku na trezory Recovery Services](./media/oracle-backup-recovery/recovery-service-01.png)

3.  Pokud chcete pokračovat, klikněte na **Povolit zálohování**.

    > [!IMPORTANT]
    > Po kliknutí na **Povolit zálohování** se proces zálohování nespustí, dokud nevyprší naplánovaný čas. Pokud chcete nastavit okamžitou zálohu, proveďte další krok.

4. Na stránce skupina prostředků klikněte na nově vytvořený Recovery Services trezoru **myVault**. Nápověda: možná budete muset aktualizovat stránku, aby se zobrazila.

5.  V okně **myVault – zálohované položky** v části **počet zálohovaných** položek vyberte počet zálohovaných položek.

    ![MyVault trezory služby Recovery Services – stránka podrobností](./media/oracle-backup-recovery/recovery-service-02.png)

6.  V okně **zálohované položky (virtuální počítač Azure)** na pravé straně stránky klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Zálohovat nyní**.

    ![Zálohování Recovery Services trezory nyní – příkaz](./media/oracle-backup-recovery/recovery-service-03.png)

7.  Přijměte výchozí hodnotu zachovat zálohu do hodnoty a klikněte na tlačítko **OK** . Počkejte na dokončení procesu zálohování. 

    Pokud chcete zobrazit stav úlohy zálohování, klikněte na **úlohy zálohování**.

    ![Stránka úlohy trezory Recovery Services](./media/oracle-backup-recovery/recovery-service-04.png)

    Stav úlohy zálohování se zobrazí na následujícím obrázku:

    ![Stránka úlohy trezory Recovery Services se stavem](./media/oracle-backup-recovery/recovery-service-05.png)
    
    Mějte na paměti, že když se snímek spustí jenom v sekundách, může nějakou dobu trvat, než se převede do trezoru, a úloha zálohování se nedokončí až do chvíle, kdy se přenos nedokončí.

8. V případě zálohování konzistentního vzhledem k aplikacím vyřešte všechny chyby v souboru protokolu. Soubor protokolu se nachází na adrese/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Vytvoření trezoru služby Recovery Services:

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. Povolit ochranu před zálohováním pro virtuální počítač:

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. Aktivovat zálohování, které se má spustit nyní, místo čekání na aktivaci služby Backup ve výchozím plánu (5 UTC): 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   Průběh úlohy zálohování můžete monitorovat pomocí `az backup job list` a `az backup job show` .

---

## <a name="recovery"></a>Obnovovací

Chcete-li obnovit databázi, proveďte následující kroky:

1. Odeberte soubory databáze.
1. Vygenerujte skript obnovení z trezoru Recovery Services.
1. Připojte bod obnovení.
1. Proveďte obnovení.

### <a name="remove-the-database-files"></a>Odebrat soubory databáze 

Později v tomto článku se dozvíte, jak otestovat proces obnovení. Než budete moci otestovat proces obnovení, je nutné odebrat soubory databáze.

1.  Vypněte instanci Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Odeberte soubory a zálohy:

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Generování skriptu pro obnovení z trezoru Recovery Services

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V Azure Portal vyhledejte položku trezory Recovery Services *myVault* a vyberte ji.

    ![Trezory Recovery Services myVault zálohované položky](./media/oracle-backup-recovery/recovery-service-06.png)

2. V okně **Přehled** vyberte **zálohované položky** a vyberte **virtuální počítač Azure**, který by měl být v seznamu anon – počet zálohovaných položek.

    ![Počet zálohovaných položek virtuálních počítačů Azure v trezoru Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

3. Na stránce zálohované položky (Azure Virtual Machines) se zobrazí váš **vmoracle19c** virtuálního počítače. Kliknutím na tři tečky na pravé straně zobrazte nabídku a vyberte **obnovení souboru**.

    ![Snímek obrazovky se stránkou pro obnovení souborů trezorů Recovery Services](./media/oracle-backup-recovery/recovery-service-08.png)

4. V podokně **obnovení souboru (Preview)** klikněte na **stáhnout skript**. Pak uložte soubor ke stažení (. py) do složky v klientském počítači. Heslo je vygenerováno pro spuštění skriptu. Zkopírujte heslo do souboru pro pozdější použití. 

    ![Stáhnout soubor skriptu – možnosti uložení](./media/oracle-backup-recovery/recovery-service-09.png)

5. Zkopírujte soubor. py do virtuálního počítače.

    Následující příklad ukazuje, jak pomocí příkazu zabezpečeného kopírování (SCP) přesunout soubor do virtuálního počítače. Obsah můžete také zkopírovat do schránky a pak obsah vložit do nového souboru, který je nastavený na virtuálním počítači.

    > [!IMPORTANT]
    > V následujícím příkladu se ujistěte, že aktualizujete hodnoty IP adresy a složky. Hodnoty musí být namapovány do složky, kde je soubor uložen.
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li zobrazit body obnovení pro virtuální počítač, použijte příkaz AZ Backup Point Recovery list. V tomto příkladu vybereme nejnovější bod obnovení pro virtuální počítač s názvem myVM, který je chráněný v trezoru myrecoveryservicesvault:

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

K získání skriptu, který připojí bod obnovení k virtuálnímu počítači, použijte příkaz az backup restore files mount-rp. Následující příklad získá skript pro virtuální počítač s názvem myVM, který je chráněn v trezoru myrecoveryservicesvault.

Nahraďte myRecoveryPointName názvem bodu obnovení, který jste získali předchozím příkazem:

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

Skript se stáhne a zobrazí se heslo jako v následujícím příkladu:

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

Zkopírujte soubor. py do virtuálního počítače.

Následující příklad ukazuje, jak pomocí příkazu zabezpečeného kopírování (SCP) přesunout soubor do virtuálního počítače. Obsah můžete také zkopírovat do schránky a pak obsah vložit do nového souboru, který je nastavený na virtuálním počítači.

> [!IMPORTANT]
> V následujícím příkladu se ujistěte, že aktualizujete hodnoty IP adresy a složky. Hodnoty musí být namapovány do složky, kde je soubor uložen.
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>Připojit bod obnovení

1. Vytvořte přípojný bod obnovení a zkopírujte do něj skript.

    V následujícím příkladu vytvořte adresář */Restore* , ke kterému se má snímek připojit, přesuňte soubor do adresáře a změňte soubor tak, aby jeho vlastníkem bylo kořenový uživatel a měl spustitelný soubor.

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    Nyní spusťte skript pro obnovení zálohy. Zobrazí se výzva k zadání hesla generovaného v Azure Portal. 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    Následující příklad ukazuje, co byste měli vidět po spuštění předchozího skriptu. Až budete vyzváni k pokračování, zadejte **Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. Přístup k připojeným svazkům se potvrdí.

    Pokud chcete skončit, zadejte **q** a pak vyhledejte připojené svazky. Pokud chcete vytvořit seznam přidaných svazků, zadejte na příkazovém řádku **df-h**.
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>Provést obnovení

1. Zkopírujte chybějící záložní soubory zpátky do oblasti rychlé obnovení:

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. Následující příkazy používají RMAN k obnovení chybějících souborů datafiles a obnovení databáze:

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. Ověřte, zda byl obsah databáze zcela obnoven:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. Odpojte bod obnovení.

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    V okně Azure Portal klikněte v okně **obnovení souboru (Preview)** na **Odpojit disky**.

    ![Odpojit disky – příkaz](./media/oracle-backup-recovery/recovery-service-10.png)
    
    Svazky pro obnovení můžete také odpojit spuštěním skriptu Pythonu s možností **-clean** .

## <a name="restore-the-entire-vm"></a>Obnovit celý virtuální počítač

Místo obnovení odstraněných souborů z trezorů Recovery Services můžete obnovit celý virtuální počítač.

Pro obnovení celého virtuálního počítače proveďte tyto kroky:

1. Zastavte a odstraňte vmoracle19c.
1. Obnovte virtuální počítač.
1. Nastavte veřejnou IP adresu.
1. Připojte se k virtuálnímu počítači.
1. Spusťte databázi pro připojení fáze a proveďte obnovení.

### <a name="stop-and-delete-vmoracle19c"></a>Zastavení a odstranění vmoracle19c

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V Azure Portal přejdete na virtuální počítač **vmoracle19c** a vyberte **zastavit**.

1. Pokud už virtuální počítač není spuštěný, vyberte **Odstranit** a pak **Ano**.

   ![Odstranit trezor – příkaz](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Zastavení a zrušení přidělení virtuálního počítače:

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. Odstraňte virtuální počítač. Po zobrazení výzvy zadejte ' y ':

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>Obnovení virtuálního počítače

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vytvořte účet úložiště pro přípravu v Azure Portal.

   1. V Azure Portal vyberte **+ vytvořit prostředek** a vyhledejte a vyberte **účet úložiště**.
    
      ![Snímek obrazovky, který ukazuje, kde vytvořit prostředek.](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. Na stránce Vytvořit účet úložiště vyberte existující skupinu prostředků **RG-Oracle**, pojmenujte svůj účet úložiště **Oracrestore** a pro druh účtu vyberte **Storage v2 (GeneralPurpose v2)** . Změňte replikaci na **místně redundantní úložiště (LRS)** a nastavte výkon na **standardní**. Zajistěte, aby se umístění nastavilo na stejnou oblast jako všechny ostatní prostředky ve skupině prostředků. 
    
      ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. Klikněte na tlačítko revize + vytvořit a potom klikněte na tlačítko vytvořit.

2. V Azure Portal vyhledejte položku trezory Recovery Services *myVault* a klikněte na ni.

    ![Trezory Recovery Services myVault zálohované položky](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  V okně **Přehled** vyberte **zálohované položky** a vyberte **virtuální počítač Azure**, který by měl být v seznamu anon – počet zálohovaných položek.

    ![Počet zálohovaných položek virtuálních počítačů Azure v trezoru Recovery Services](./media/oracle-backup-recovery/recovery-service-07.png)

4.  V části zálohované položky (Azure Virtual Machines) se zobrazí stránka **vmoracle19c** vašeho virtuálního počítače. Klikněte na název virtuálního počítače.

    ![Stránka virtuálního počítače pro obnovení](./media/oracle-backup-recovery/recover-vm-02.png)

5.  V okně **vmoracle19c** vyberte bod obnovení s **konzistentním** typem konzistence aplikace a kliknutím na tlačítko se třemi tečkami (**...**) na pravé straně zobrazte nabídku.  V nabídce klikněte na **obnovit virtuální počítač**.

    ![Příkaz obnovit virtuální počítač](./media/oracle-backup-recovery/recover-vm-03.png)

6.  V okně **obnovit virtuální počítač** vyberte **vytvořit novou** a **vytvořte nový virtuální počítač**. Zadejte název virtuálního počítače **vmoracle19c** a vyberte virtuální síť **vmoracle19cVNET**, na základě výběru virtuální sítě se automaticky vyplní podsíť. Proces obnovení virtuálního počítače vyžaduje účet úložiště Azure ve stejné skupině prostředků a oblasti. Můžete zvolit účet úložiště, který jste **orarestore** nastavení dříve.

    ![Obnovení hodnot konfigurace](./media/oracle-backup-recovery/recover-vm-04.png)

7.  Pokud chcete virtuální počítač obnovit, klikněte na tlačítko **obnovit** .

8.  Chcete-li zobrazit stav procesu obnovení, klikněte na možnost **úlohy** a potom klikněte na položku **úlohy zálohování**.

    ![Příkaz stavu úloh zálohování](./media/oracle-backup-recovery/recover-vm-05.png)

    Kliknutím na **probíhající** operaci obnovení zobrazíte stav procesu obnovení:

    ![Stav procesu obnovení](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nastavit účet úložiště a sdílení souborů, spusťte v Azure CLI následující příkazy.

1. Vytvořte účet úložiště ve stejné skupině prostředků a umístění jako váš virtuální počítač:

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. Načte seznam bodů obnovení, které jsou k dispozici. 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. Obnovte bod obnovení účtu úložiště. Nahraďte `<myRecoveryPointName>` bodem obnovení ze seznamu, který jste vygenerovali v předchozím kroku:

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. Načtěte Podrobnosti úlohy obnovení. Následující příkaz získá více podrobností pro spouštěcí obnovenou úlohu, včetně jejího názvu, který je potřeba k načtení identifikátoru URI šablony. 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   Výstup bude vypadat nějak `(Note down the name of the restore job)` takto:

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. Načtěte podrobnosti identifikátoru URI, který se použije k opětovnému vytvoření virtuálního počítače. Nahraďte název úlohy obnovení z předchozího kroku pro `<RestoreJobName>` .

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   Výstup je podobný tomuto:

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   Název šablony, který je na konci identifikátoru URI objektu BLOB šablony, který je v tomto příkladu `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json` , a název kontejneru objektů blob, který je `vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2` uveden v seznamu. 

   Pomocí těchto hodnot v následujícím příkazu přiřaďte proměnné v přípravě pro vytvoření virtuálního počítače. Klíč SAS se vygeneruje pro kontejner úložiště s dobou trvání 30 minut.  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   Teď nasaďte šablonu a vytvořte virtuální počítač.

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   Zobrazí se výzva k zadání názvu virtuálního počítače.

---

### <a name="set-the-public-ip-address"></a>Nastavení veřejné IP adresy

Po obnovení virtuálního počítače byste měli původní IP adresu znovu přiřadit novému virtuálnímu počítači.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1.  V Azure Portal přejdete na virtuální počítač **vmoracle19c**. Všimněte si, že má přiřazenou novou veřejnou IP adresu a síťovou kartu jako vmoracle19c-nic-XXXXXXXXXXXX, ale nemá adresu DNS. Když se původní virtuální počítač odstranil z jeho veřejné IP adresy a síťový adaptér se zachová a další kroky se znovu připojí k novému virtuálnímu počítači.

    ![Seznam veřejných IP adres](./media/oracle-backup-recovery/create-ip-01.png)

2.  Zastavení virtuálního počítače

    ![Vytvořit IP adresu](./media/oracle-backup-recovery/create-ip-02.png)

3.  Přejít na **síť**

    ![Přidružit IP adresu](./media/oracle-backup-recovery/create-ip-03.png)

4.  Klikněte na **připojit síťové rozhraní**, vyberte původní síťovou kartu * * vmoracle19cVMNic, ke které je původní veřejná IP adresa stále přidružená, a klikněte na **OK** .

    ![Vyberte typ prostředku a hodnoty síťových adaptérů.](./media/oracle-backup-recovery/create-ip-04.png)

5.  Teď musíte odpojit síťovou kartu, která se vytvořila s operací obnovení virtuálního počítače, protože je nakonfigurovaná jako primární rozhraní. Klikněte na **Odpojit síťové rozhraní** a zvolte novou síťovou kartu, která se podobá **vmoracle19c-nic-xxxxxxxxxxxx**, a pak klikněte na **OK** .

    ![Snímek obrazovky, který ukazuje, kde vybrat odpojit síťové rozhraní](./media/oracle-backup-recovery/create-ip-05.png)
    
    Nově vytvořený virtuální počítač teď bude mít původní síťovou kartu, která je přidružená k původní IP adrese a pravidlům skupiny zabezpečení sítě.
    
    ![Hodnota IP adresy](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  Vraťte se do **přehledu** a klikněte na **Spustit** . 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Zastavení a zrušení přidělení virtuálního počítače:

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. Vypíše aktuální, obnovit vygenerovanou síťovou kartu virtuálního počítače.

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   Výstup bude vypadat podobně jako v tomto příkladu, který vypíše název síťové karty pro obnovení, který se vygeneroval jako `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf`

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. V tomto případě připojte původní síťovou kartu, která by měla mít název `<VMName>VMNic` `vmoracle19cVMNic` . Původní veřejná IP adresa je stále připojena k tomuto síťovému rozhraní a bude obnovena na virtuální počítač, pokud je síťové rozhraní znovu připojeno. 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. Odpojení vygenerované síťové karty pro obnovení

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. Spusťte virtuální počítač:

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Pokud se chcete připojit k virtuálnímu počítači, použijte tento skript:

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>Spusťte databázi pro připojení fáze a proveďte obnovení.

1. Může se stát, že instance je spuštěná, protože se automaticky spustí pokus o spuštění databáze při spuštění virtuálního počítače. Databáze však vyžaduje obnovení a je pravděpodobně pouze v rámci fáze připojení, proto je nejprve spuštěno přípravné vypnutí.

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. Ověřte, zda byl obnoven obsah databáze:

    ```bash
    SQL> select * from scott.scott_table;
    ```

Zálohování a obnovení databáze Oracle Database 19c na virtuálním počítači Azure Linux je nyní dokončeno.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud už virtuální počítač nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít následující příkaz:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Další kroky

[Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)

[Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)