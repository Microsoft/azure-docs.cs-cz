---
title: Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux | Microsoft Docs
description: Naučte se, jak zálohovat a obnovit databázi Oracle Database 12c v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: c5f02117d3af7fb411c75d783df82f6008d8104e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687000"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux

Pomocí Azure CLI můžete vytvořit a spravovat prostředky Azure na příkazovém řádku nebo použít skripty. V tomto článku používáme skripty Azure CLI k nasazení Oracle Database databáze 12c z Image Galerie Azure Marketplace.

Než začnete, ujistěte se, že je rozhraní Azure CLI nainstalované. Další informace najdete v příručce pro [instalaci Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="step-1-prerequisites"></a>Krok 1: předpoklady

*   Chcete-li provést proces zálohování a obnovení, je třeba nejprve vytvořit virtuální počítač se systémem Linux s nainstalovanou instancí Oracle Database 12c. Image Marketplace, kterou použijete k vytvoření virtuálního počítače, se jmenuje *Oracle: Oracle-Database-EE: 12.1.0.2: nejnovější*.

    Informace o tom, jak vytvořit databázi Oracle, najdete v tématu [rychlý Start k vytvoření databáze Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: připojení k virtuálnímu počítači

*   Pokud chcete vytvořit relaci Secure Shell (SSH) s virtuálním počítačem, použijte následující příkaz. Nahraďte IP adresu a název hostitele kombinací `publicIpAddress` hodnoty pro váš virtuální počítač.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: příprava databáze

1.  Tento krok předpokládá, že máte instanci Oracle (cdb1), která je spuštěná na virtuálním počítači s názvem *myVM*.

    Spusťte kořenový adresář *Oracle* a pak inicializujte naslouchací proces:

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

2.  Volitelné Ujistěte se, že je databáze v režimu protokolu archivace:

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

3.  Volitelné Vytvořte tabulku pro otestování potvrzení:

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

5. K zálohování databáze použijte Oracle Recovery Manager (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Zálohování konzistentní vzhledem k aplikacím pro virtuální počítače se systémem Linux

Zálohování konzistentní s aplikací je nová funkce v Azure Backup. Můžete vytvořit a vybrat skripty, které se spustí před a po snímku virtuálního počítače (pre-Snapshot a post-snapshot).

1. Stáhněte soubor JSON.

    Stáhnout VMSnapshotScriptPluginConfig.jsz https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig . Obsah souboru vypadá podobně jako v následujícím příkladu:

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

2. Vytvořte na virtuálním počítači složku složce/etc/Azure:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Zkopírujte soubor JSON.

    Zkopírujte VMSnapshotScriptPluginConfig.jsdo složky složce/etc/Azure.

4. Upravte soubor JSON.

    Upravte VMSnapshotScriptPluginConfig.jsv souboru tak, aby obsahovala `PreScriptLocation` `PostScriptlocation` parametry a. Příklad:

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

5. Vytvořte soubory skriptu předsnímkování a po snímku.

    Zde je příklad předsnímkových a posnímkových skriptů pro "studenou zálohu" (offline zálohování při vypnutí a restartování počítače):

    Pro/etc/Azure/pre_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Pro/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Tady je příklad předsnímkových a posnímkových skriptů pro "horkou zálohu" (online zálohování):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pro/etc/Azure/post_script. sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pro/etc/Azure/pre_script. SQL upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Pro/etc/Azure/post_script. SQL upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Změnit oprávnění k souboru:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Otestujte skripty.

    Chcete-li otestovat skripty, nejprve se přihlaste jako kořenový adresář. Pak zajistěte, aby nedocházelo k chybám:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Další informace najdete v tématu [Zálohování konzistentní vzhledem k aplikacím pro virtuální počítače se systémem Linux](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: použití trezorů služby Azure Recovery Services k zálohování virtuálního počítače

1.  V Azure Portal vyhledejte **Recovery Services trezory**.

    ![Stránka trezorů Recovery Services](./media/oracle-backup-recovery/recovery_service_01.png)

2.  V okně **trezory Recovery Services** přidejte nový trezor kliknutím na tlačítko **Přidat**.

    ![Přidat stránku na trezory Recovery Services](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Chcete-li pokračovat, klikněte na **myVault**.

    ![Stránka s podrobnostmi o úložištích Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  V okně **myVault** klikněte na **zálohovat**.

    ![Stránka zálohování Recovery Services trezory](./media/oracle-backup-recovery/recovery_service_04.png)

5.  V okně **cíl zálohování** použijte výchozí hodnoty **Azure** a **virtuálního počítače**. Klikněte na **OK**.

    ![Stránka s podrobnostmi o úložištích Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  V případě **zásad zálohování**použijte **DefaultPolicy**nebo vyberte **vytvořit novou zásadu**. Klikněte na **OK**.

    ![Stránka s podrobnostmi o zásadách zálohování Recovery Services trezory](./media/oracle-backup-recovery/recovery_service_06.png)

7.  V okně **Výběr virtuálních počítačů** zaškrtněte políčko **myVM1** a pak klikněte na **OK**. Klikněte na tlačítko **Povolit zálohování** .

    ![Recovery Services trezory položky na stránku podrobností zálohování](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknutí na **Povolit zálohování**se proces zálohování nespustí, dokud nevyprší naplánovaný čas. Pokud chcete nastavit okamžitou zálohu, proveďte další krok.

8.  V okně **myVault – zálohované položky** v části **počet zálohovaných**položek vyberte počet zálohovaných položek.

    ![MyVault trezory služby Recovery Services – stránka podrobností](./media/oracle-backup-recovery/recovery_service_08.png)

9.  V okně **zálohované položky (virtuální počítač Azure)** na pravé straně stránky klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Zálohovat nyní**.

    ![Zálohování Recovery Services trezory nyní – příkaz](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klikněte na tlačítko **zálohování** . Počkejte na dokončení procesu zálohování. Pak přejít ke [kroku 6: odebrání souborů databáze](#step-6-remove-the-database-files).

    Pokud chcete zobrazit stav úlohy zálohování, klikněte na **úlohy**.

    ![Stránka úlohy trezory Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    Stav úlohy zálohování se zobrazí na následujícím obrázku:

    ![Stránka úlohy trezory Recovery Services se stavem](./media/oracle-backup-recovery/recovery_service_11.png)

11. V případě zálohování konzistentního vzhledem k aplikacím vyřešte všechny chyby v souboru protokolu. Soubor protokolu se nachází na adrese/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: odebrání souborů databáze 
Později v tomto článku se dozvíte, jak otestovat proces obnovení. Než budete moci otestovat proces obnovení, je nutné odebrat soubory databáze.

1.  Odebrat tabulkový prostor a záložní soubory:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Volitelné Vypněte instanci Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Obnovení odstraněných souborů z trezorů Recovery Services
Chcete-li obnovit odstraněné soubory, proveďte následující kroky:

1. V Azure Portal vyhledejte položku trezory Recovery Services *myVault* . V okně **Přehled** v části **zálohované položky**vyberte počet položek.

    ![Trezory Recovery Services myVault zálohované položky](./media/oracle-backup-recovery/recovery_service_12.png)

2. V části **počet zálohovaných položek**vyberte počet položek.

    ![Počet zálohovaných položek virtuálních počítačů Azure v trezoru Recovery Services](./media/oracle-backup-recovery/recovery_service_13.png)

3. V okně **myvm1** klikněte na **obnovení souborů (Preview)**.

    ![Snímek obrazovky se stránkou pro obnovení souborů trezorů Recovery Services](./media/oracle-backup-recovery/recovery_service_14.png)

4. V podokně **obnovení souboru (Preview)** klikněte na **stáhnout skript**. Pak uložte soubor ke stažení (. sh) do složky v klientském počítači.

    ![Stáhnout soubor skriptu – možnosti uložení](./media/oracle-backup-recovery/recovery_service_15.png)

5. Zkopírujte soubor. sh do virtuálního počítače.

    Následující příklad ukazuje, jak pomocí příkazu zabezpečeného kopírování (SCP) přesunout soubor do virtuálního počítače. Obsah můžete také zkopírovat do schránky a pak obsah vložit do nového souboru, který je nastavený na virtuálním počítači.

    > [!IMPORTANT]
    > V následujícím příkladu se ujistěte, že aktualizujete hodnoty IP adresy a složky. Hodnoty musí být namapovány do složky, kde je soubor uložen.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Změňte soubor tak, aby byl vlastněn kořenem.

    V následujícím příkladu změňte soubor tak, aby byl vlastněn kořenovým adresářem. Pak změňte oprávnění.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    Následující příklad ukazuje, co byste měli vidět po spuštění předchozího skriptu. Až budete vyzváni k pokračování, zadejte **Y**.

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

7. Přístup k připojeným svazkům se potvrdí.

    Pokud chcete skončit, zadejte **q**a pak vyhledejte připojené svazky. Pokud chcete vytvořit seznam přidaných svazků, zadejte na příkazovém řádku **DF-k**.

    ![Příkaz df-k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Pomocí následujícího skriptu zkopírujte chybějící soubory zpátky do složky:

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

9. V následujícím skriptu obnovte databázi pomocí RMAN:

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

    V okně Azure Portal klikněte v okně **obnovení souboru (Preview)** na **Odpojit disky**.

    ![Odpojit disky – příkaz](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Obnovit celý virtuální počítač

Místo obnovení odstraněných souborů z trezorů Recovery Services můžete obnovit celý virtuální počítač.

### <a name="step-1-delete-myvm"></a>Krok 1: odstranění myVM

*   V Azure Portal otevřete trezor **myVM1** a pak vyberte **Odstranit**.

    ![Odstranit trezor – příkaz](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: obnovení virtuálního počítače

1.  Přejít na **Recovery Services trezory**a pak vyberte **myVault**.

    ![záznam myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  V okně **Přehled** v části **zálohované položky**vyberte počet položek.

    ![myVault zálohované položky](./media/oracle-backup-recovery/recover_vm_03.png)

3.  V okně **zálohované položky (virtuální počítač Azure)** vyberte **myvm1**.

    ![Stránka virtuálního počítače pro obnovení](./media/oracle-backup-recovery/recover_vm_04.png)

4.  V okně **myvm1** klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **obnovit virtuální počítač**.

    ![Příkaz obnovit virtuální počítač](./media/oracle-backup-recovery/recover_vm_05.png)

5.  V okně **Vybrat bod obnovení** vyberte položku, kterou chcete obnovit, a klikněte na tlačítko **OK**.

    ![Vybrat bod obnovení](./media/oracle-backup-recovery/recover_vm_06.png)

    Pokud jste povolili zálohování konzistentní s aplikací, zobrazí se svislá modrá čára.

6.  V okně **obnovit konfiguraci** vyberte název virtuálního počítače, vyberte skupinu prostředků a pak klikněte na **OK**.

    ![Obnovení hodnot konfigurace](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Pokud chcete virtuální počítač obnovit, klikněte na tlačítko **obnovit** .

8.  Chcete-li zobrazit stav procesu obnovení, klikněte na možnost **úlohy**a potom klikněte na položku **úlohy zálohování**.

    ![Příkaz stavu úloh zálohování](./media/oracle-backup-recovery/recover_vm_08.png)

    Následující obrázek ukazuje stav procesu obnovení:

    ![Stav procesu obnovení](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: nastavení veřejné IP adresy
Po obnovení virtuálního počítače nastavte veřejnou IP adresu.

1.  Do vyhledávacího pole zadejte **veřejnou IP adresu**.

    ![Seznam veřejných IP adres](./media/oracle-backup-recovery/create_ip_00.png)

2.  V okně **veřejné IP adresy** klikněte na **Přidat**. V okně **vytvořit veřejnou IP adresu** **Vyberte název veřejné**IP adresy. V části **Skupina prostředků** vyberte **Použít existující**. Potom klikněte na **Vytvořit**.

    ![Vytvořit IP adresu](./media/oracle-backup-recovery/create_ip_01.png)

3.  Pokud chcete k síťovému rozhraní virtuálního počítače přidružit veřejnou IP adresu, vyhledejte a vyberte **myVMip**. Pak klikněte na tlačítko **přidružit**.

    ![Přidružit IP adresu](./media/oracle-backup-recovery/create_ip_02.png)

4.  Jako **typ prostředku**vyberte **síťové rozhraní**. Vyberte síťové rozhraní používané instancí myVM a pak klikněte na **OK**.

    ![Vyberte typ prostředku a hodnoty síťových adaptérů.](./media/oracle-backup-recovery/create_ip_03.png)

5.  Vyhledejte a otevřete instanci myVM, která je naportovaná z portálu. V okně **přehledu** myVM se zobrazí IP adresa, která je přidružená k virtuálnímu počítači.

    ![Hodnota IP adresy](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: připojení k virtuálnímu počítači

*   Pokud se chcete připojit k virtuálnímu počítači, použijte tento skript:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: testování, zda je databáze přístupná
*   K otestování dostupnosti použijte tento skript:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Pokud **spouštěcí** příkaz databáze vygeneruje chybu, k obnovení databáze se podívejte na [Krok 6: použití rman k obnovení databáze](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (volitelné) použijte k obnovení databáze RMAN
*   K obnovení databáze použijte tento skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure Linux je nyní dokončeno.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud už virtuální počítač nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít následující příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

[Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)

[Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](../../linux/cli-samples.md)



