---
title: Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux | Dokumenty společnosti Microsoft
description: Zjistěte, jak zálohovat a obnovit databázi Oracle Database 12c ve vašem prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: mimckitt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 9e2fcc9533c183a356b19c369777e1319ca7f9ae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263195"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux

Azure CLI můžete použít k vytvoření a správě prostředků Azure na příkazovém řádku nebo použít skripty. V tomto článku používáme skripty Azure CLI k nasazení databáze Oracle Database 12c z image galerie Azure Marketplace.

Než začnete, ujistěte se, že je nainstalován azure cli. Další informace naleznete v [průvodci instalací příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="step-1-prerequisites"></a>Krok 1: Požadavky

*   Chcete-li provést proces zálohování a obnovení, musíte nejprve vytvořit virtuální počítač s Linuxem, který má nainstalovanou instanci databáze Oracle Database 12c. Image Marketplace, kterou použijete k vytvoření virtuálního virtuálního trhu, se jmenuje *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Informace o vytvoření databáze Oracle naleznete v [tématu Oracle create database quickstart](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Připojení k virtuálnímu virtuálnímu mísu

*   Chcete-li vytvořit relaci zabezpečeného prostředí (SSH) s virtuálním virtuálním virtuálním virtuálním zabezpečeným virtuálním trhem, použijte následující příkaz. Nahraďte IP adresu a kombinaci názvu hostitele hodnotou `publicIpAddress` pro váš virtuální počítač.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Příprava databáze

1.  Tento krok předpokládá, že máte instanci Oracle (cdb1), která je spuštěna na virtuálním počítači s názvem *myVM*.

    Spusťte kořen *oracle* superuser a potom inicializovat naslouchací proces:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Nepovinné) Zkontrolujte, zda je databáze v režimu protokolu archivu:

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
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

3.  (Nepovinné) Vytvořte tabulku pro testování potvrzení:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```

4.  Ověřte nebo změňte umístění a velikost záložního souboru:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. K zálohování databáze použijte Správce obnovení oracle (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Zálohování konzistentní s aplikací pro virtuální počítače s Linuxem

Zálohování konzistentní s aplikacemi je nová funkce v Azure Backup. Můžete vytvořit a vybrat skripty, které se mají spustit před a po snímku virtuálního počítače (před snímek a post-snímek).

1. Stáhněte soubor JSON.

    Stáhnout vMSnapshotScriptPluginConfig.json https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfigz . Obsah souboru vypadá podobně jako následující:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Vytvořte složku /etc/azure na virtuálním počítači:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Zkopírujte soubor JSON.

    Zkopírujte soubor VMSnapshotScriptPluginConfig.json do složky /etc/azure.

4. Upravte soubor JSON.

    Upravte soubor VMSnapshotScriptPluginConfig.json `PreScriptLocation` tak, aby obsahoval parametry a. `PostScriptlocation` Příklad:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Vytvořte soubory skriptů před snímek a po snímku.

    Zde je příklad skriptů před snímkem a po snímku pro "studenou zálohu" (zálohování offline s vypnutím a restartováním):

    Pro /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Pro /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Zde je příklad skriptů před snímkem a po snímku pro "horkou zálohu" (zálohování online):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pro /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pro /etc/azure/pre_script.sql upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Pro /etc/azure/post_script.sql upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Změna oprávnění k souborům:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Otestujte skripty.

    Chcete-li skripty otestovat, přihlaste se nejprve jako root. Poté se ujistěte, že nedochází k chybám:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Další informace najdete v [tématu Zálohování konzistentní s aplikací pro virtuální počítače s Linuxem](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: K zálohování virtuálního počítače pomocí trezorů Služby Azure Recovery Services

1.  Na webu Azure Portal vyhledejte **trezory služby Recovery Services**.

    ![Stránka trezorů služby Recovery Services](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Chcete-li přidat nový trezor, klepněte na tlačítko **Přidat**v okně **Trezory služby Recovery Services** .

    ![Trezory služby Recovery Services přidávají stránku](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Chcete-li pokračovat, klepněte na **položku myVault**.

    ![Stránka podrobností trezorů služby Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  V okně **myVault** klepněte na tlačítko **Zálohovat**.

    ![Záložní stránka úložiště služby Recovery Services](./media/oracle-backup-recovery/recovery_service_04.png)

5.  V okně **Cíl zálohování** použijte výchozí hodnoty **Azure** a **Virtuální počítač**. Klikněte na tlačítko **OK**.

    ![Stránka podrobností trezorů služby Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  V **případě zásad zálohování**použijte výchozí **zásady**nebo vyberte **příkaz Vytvořit novou zásadu**. Klikněte na tlačítko **OK**.

    ![Stránka podrobností zásad zálohování služby Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  V okně **Vybrat virtuální počítače** zaškrtněte políčko **myVM1** a klikněte na **OK**. Klepněte na tlačítko **Povolit zálohování.**

    ![Úložiště položek služby Recovery Services na stránku podrobností zálohování](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po klepnutí na tlačítko **Povolit zálohování**se proces zálohování nespustí, dokud nevyprší naplánovaný čas. Chcete-li vytvořit okamžitou zálohu, proveďte další krok.

8.  V okně **myVault - Backup items** vyberte v části **BACKUP ITEM COUNT**počet položek zálohy.

    ![Stránka podrobností služby Recovery Services vaults myVault](./media/oracle-backup-recovery/recovery_service_08.png)

9.  V okně **Položky zálohování (Azure Virtual Machine)** na pravé straně stránky klikněte na tlačítko tři tečky (**...**) a potom klikněte na **tlačítko Zálohovat .**

    ![Trezory služby Recovery Services– Nyní příkaz Zálohování](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klepněte na tlačítko **Zálohovat.** Počkejte na dokončení procesu zálohování. Potom přejděte ke [kroku 6: Odeberte soubory databáze](#step-6-remove-the-database-files).

    Chcete-li zobrazit stav zálohovací úlohy, klepněte na tlačítko **Úlohy**.

    ![Stránka úlohy trezorů služby Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    Stav zálohovací úlohy se zobrazí na následujícím obrázku:

    ![Stránka úlohy služby Recovery Services trezory se stavem](./media/oracle-backup-recovery/recovery_service_11.png)

11. Pro zálohování konzistentní s aplikací, adresa všechny chyby v souboru protokolu. Soubor protokolu je umístěn na adrese /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Odebrání databázových souborů 
Dále v tomto článku se dozvíte, jak otestovat proces obnovení. Před testováním procesu obnovení je třeba odebrat soubory databáze.

1.  Odeberte tabulkový prostor a záložní soubory:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Nepovinné) Vypněte instanci Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Obnovení odstraněných souborů z trezorů služby Recovery Services
Chcete-li odstraněné soubory obnovit, proveďte následující kroky:

1. Na webu Azure Portal vyhledejte položku trezorů služby *myVault* Recovery Services. V okně **Přehled** vyberte v části **Zálohování položek**počet položek.

    ![Služba Recovery Services trezory myVault zálohovací položky](./media/oracle-backup-recovery/recovery_service_12.png)

2. V části **POČET POLOŽEK ZÁLOHOVÁNÍ**vyberte počet položek.

    ![Úložiště služby Recovery Services trezory Azure Virtual Machine počet položek zálohování](./media/oracle-backup-recovery/recovery_service_13.png)

3. V okně **myvm1** klikněte na **Obnovení souboru (náhled).**

    ![Snímek obrazovky se stránkou obnovení souborů služby Recovery Services služby Recovery](./media/oracle-backup-recovery/recovery_service_14.png)

4. V podokně **Obnovení souboru (náhled)** klepněte na **tlačítko Stáhnout skript**. Potom uložte soubor ke stažení (.sh) do složky v klientském počítači.

    ![Možnosti uložení souboru skriptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Zkopírujte soubor .sh do virtuálního soudu.

    Následující příklad ukazuje, jak použít příkaz zabezpečené kopie (scp) k přesunutí souboru do virtuálního účtu. Můžete také zkopírovat obsah do schránky a vložte obsah do nového souboru, který je nastaven na virtuálním počítači.

    > [!IMPORTANT]
    > V následujícím příkladu se ujistěte, že aktualizujete hodnoty IP adresy a složky. Hodnoty musí být namapovány do složky, do které je soubor uložen.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Změňte soubor tak, aby byl vlastněn kořenem.

    V následujícím příkladu změňte soubor tak, aby byl vlastněn kořenem. Potom změňte oprávnění.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    Následující příklad ukazuje, co byste měli vidět po spuštění předchozího skriptu. Po zobrazení výzvy k pokračování zadejte **hodnotu Y**.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. Přístup k připojeným svazkům je potvrzen.

    Chcete-li ukončit, zadejte **q**a vyhledejte připojené svazky. Chcete-li vytvořit seznam přidaných svazků, zadejte na příkazovém řádku **příkaz df -k**.

    ![Příkaz df -k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Pomocí následujícího skriptu zkopírujte chybějící soubory zpět do složek:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```

9. V následujícím skriptu použijte RMAN k obnovení databáze:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

10. Odpojte disk.

    Na webu Azure Portal klikněte v okně **Obnovení souborů (Preview)** na **odpojit disky**.

    ![Odpojit disky, příkaz](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Obnovení celého virtuálního mísa

Místo obnovení odstraněných souborů z trezorů služby Recovery Services můžete obnovit celý virtuální virtuální ms.

### <a name="step-1-delete-myvm"></a>Krok 1: Odstranění myVM

*   Na webu Azure Portal přejděte do trezoru **myVM1** a vyberte **Odstranit**.

    ![Odstranit trezor, příkaz](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Obnovení virtuálního virtuálního mísy

1.  Přejděte do **trezorů služby Recovery Services**a vyberte **myVault**.

    ![položka myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  V okně **Přehled** vyberte v části **Zálohování položek**počet položek.

    ![myVault zálohovat položky](./media/oracle-backup-recovery/recover_vm_03.png)

3.  V okně **Položky zálohování (Virtuální počítač Azure)** vyberte **myvm1**.

    ![Stránka virtuálního virtuálního virtuálního montovapro obnovení](./media/oracle-backup-recovery/recover_vm_04.png)

4.  V okně **myvm1** klikněte na tlačítko tři tečky (**...**) a potom klikněte na **Obnovit virtuální počítač**.

    ![Obnovit virtuální virtuální montov.](./media/oracle-backup-recovery/recover_vm_05.png)

5.  V okně **Vybrat bod obnovení** vyberte položku, kterou chcete obnovit, a klepněte na tlačítko **OK**.

    ![Výběr bodu obnovení](./media/oracle-backup-recovery/recover_vm_06.png)

    Pokud jste povolili zálohování konzistentní s aplikací, zobrazí se svislý modrý pruh.

6.  V okně **Obnovit konfiguraci** vyberte název virtuálního počítače, vyberte skupinu prostředků a klepněte na tlačítko **OK**.

    ![Obnovení hodnot konfigurace](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Pokud chcete virtuální ho obnovit, klikněte na tlačítko **Obnovit.**

8.  Chcete-li zobrazit stav procesu obnovení, klepněte na **položku Úlohy**a potom klepněte na položku **Zálohování úloh**.

    ![Příkaz Stav úloh zálohování](./media/oracle-backup-recovery/recover_vm_08.png)

    Následující obrázek znázorňuje stav procesu obnovení:

    ![Stav procesu obnovení](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Nastavení veřejné IP adresy
Po obnovení virtuálního počítačů nastavte veřejnou IP adresu.

1.  Do vyhledávacího pole zadejte **veřejnou IP adresu**.

    ![Seznam veřejných IP adres](./media/oracle-backup-recovery/create_ip_00.png)

2.  V okně **Veřejné IP adresy** klikněte na **Přidat**. V okně **Vytvořit veřejnou IP adresu** vyberte v části **Název**název veřejné IP adresy. V části **Skupina prostředků** vyberte **Použít existující**. Poté klikněte na možnost **Vytvořit**.

    ![Vytvořit IP adresu](./media/oracle-backup-recovery/create_ip_01.png)

3.  Chcete-li přidružit veřejnou IP adresu k síťovému rozhraní virtuálního počítačů, vyhledejte a vyberte **myVMip**. Potom klepněte na tlačítko **Přidružit**.

    ![Přidružená IP adresa](./media/oracle-backup-recovery/create_ip_02.png)

4.  V **případě typu Prostředek**vyberte možnost Síťové **rozhraní**. Vyberte síťové rozhraní používané instancí myVM a klepněte na tlačítko **OK**.

    ![Výběr hodnot typu prostředku a nic](./media/oracle-backup-recovery/create_ip_03.png)

5.  Vyhledejte a otevřete instanci myVM, která je přenesena z portálu. Ip adresa, která je přidružená k virtuálnímu počítači, se zobrazí v okně **přehled** myVM.

    ![Hodnota IP adresy](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Připojení k virtuálnímu virtuálnímu mísu

*   Chcete-li se připojit k virtuálnímu virtuálnímu provozu, použijte následující skript:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: Otestujte, zda je databáze přístupná
*   Chcete-li otestovat usnadnění přístupu, použijte následující skript:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Pokud příkaz **spuštění** databáze generuje chybu, chcete-li databázi obnovit, [přečtěte si informace o kroku 6: Obnovení databáze pomocí funkce RMAN](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (Volitelné) Použití RMAN k obnovení databáze
*   Chcete-li databázi obnovit, použijte následující skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux je teď dokončeno.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Když už virtuální hod nepotřebujete, můžete pomocí následujícího příkazu odebrat skupinu prostředků, virtuální hod a všechny související prostředky:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz: Vytvoření vysoce dostupných virtuálních měn](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky azure cli nasazení virtuálních montovek virtuálních montovek](../../linux/cli-samples.md)



