---
title: Zálohování a obnovení databáze 12c databáze Oracle na virtuálním počítači Azure Linux | Microsoft Docs
description: Zjistěte, jak zálohovat a obnovit databázi Oracle Database 12c v prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: e804fd17c3dbe9df9e9dc258e67b8f9192c1f8ad
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Zálohování a obnovení databáze 12c databáze Oracle na virtuálním počítači Azure Linux

Rozhraní příkazového řádku Azure můžete vytvořit a spravovat prostředky Azure na příkazovém řádku nebo pomocí skriptů. V tomto článku používáme skripty rozhraní příkazového řádku Azure k nasazení databázi Oracle Database 12c z Galerie image Azure Marketplace.

Než začnete, ujistěte se, že je nainstalované rozhraní příkazového řádku Azure. Další informace najdete v tématu [Průvodce instalací Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="step-1-prerequisites"></a>Krok 1: požadavky

*   Chcete-li provést proces zálohování a obnovení, musíte nejdřív vytvořit virtuální počítač Linux, který má nainstalovanou instanci databáze Oracle 12c. Název bitové kopie Marketplace, které použijete k vytvoření virtuálního počítače *Oracle: Oracle – databáze-Ee:12.1.0.2:latest*.

    Naučte se vytvořit databázi Oracle, najdete v článku [Oracle vytvořit rychlý start databáze](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Připojení k virtuálnímu počítači

*   Pro vytvoření relace Secure Shell (SSH) s virtuálním Počítačem, použijte následující příkaz. Nahraďte adresu IP a názvem hostitele s `publicIpAddress` hodnotu pro virtuální počítač.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Příprava databáze

1.  Tento krok předpokládá, že máte instanci Oracle (cdb1), která běží na virtuálním počítači s názvem *Můjvp*.

    Spustit *oracle* kořenové superuživatele a pak inicializovat naslouchací proces:

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

2.  (Volitelné) Ujistěte se, že databáze je v režimu protokolu archivu:

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
3.  (Volitelné) Vytvořte tabulku pro testování potvrzení:

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
4.  Ověřte nebo změňte umístění záložního souboru a velikost:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Použijte k zálohování databáze Oracle obnovení správce (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Zálohování konzistentní s aplikací pro virtuální počítače s Linuxem

Zálohování konzistentní s aplikací je nová funkce v Azure Backup. Můžete vytvořit a vyberte skripty provést před a po snímek virtuálního počítače (snímek před a po pořízení snímku).

1. Stažení souboru JSON.

    Stáhněte si VMSnapshotScriptPluginConfig.json z https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Obsah souboru vypadat nějak takto:

    ```azurecli
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

2. Vytvořte složku /etc/azure na virtuální počítač:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Zkopírujte soubor JSON.

    Zkopírujte VMSnapshotScriptPluginConfig.json do složky /etc/azure.

4. Upravte soubor JSON.

    Upravte soubor VMSnapshotScriptPluginConfig.json zahrnout `PreScriptLocation` a `PostScriptlocation` parametry. Příklad:

    ```azurecli
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

5. Vytvoření snímku před a po pořízení snímku skriptu souborů.

    Tady je příklad snímku před a po pořízení snímku skripty pro "cold zálohování" (zálohu do offline režimu, vypnutí a restartování):

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

    Tady je příklad snímku před a po pořízení snímku skripty pro "za zálohování" (online zálohování):

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

    /Etc/azure/pre_script.sql upravte obsah souboru podle požadavků:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /Etc/azure/post_script.sql upravte obsah souboru podle požadavků:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Změna oprávnění k souboru:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testovat skripty.

    K testování skriptů, nejprve, přihlaste se jako kořenový. Potom zkontrolujte, že nejsou žádné chyby:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Další informace najdete v tématu [zálohování konzistentní s aplikací pro virtuální počítače s Linuxem](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: Trezory služeb zotavení Azure pomocí zálohování virtuálního počítače

1.  Na portálu Azure, vyhledejte **trezory služeb zotavení**.

    ![Stránka trezory služby zotavení](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na **trezory služeb zotavení** okno pro přidání nové úložiště, klikněte na tlačítko **přidat**.

    ![Trezory služeb zotavení přidat stránku](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Chcete-li pokračovat, klikněte na tlačítko **myVault**.

    ![Stránku podrobností trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na **myVault** okně klikněte na tlačítko **zálohování**.

    ![Trezory služeb zotavení zálohování stránky](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na **cíl zálohování** okně použít výchozí hodnoty **Azure** a **virtuální počítač**. Klikněte na **OK**.

    ![Stránku podrobností trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Pro **zálohování zásad**, použijte **DefaultPolicy**, nebo vyberte **vytvořit novou zásadu**. Klikněte na **OK**.

    ![Stránka podrobnosti zásady zálohování trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na **vybrat virtuální počítače** okně, vyberte **myVM1** zaškrtněte políčko a potom klikněte na **OK**. Klikněte **povolit zálohování** tlačítko.

    ![Položek trezory služeb zotavení na stránku podrobností zálohování](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknutí na tlačítko **povolit zálohování**, proces zálohování nelze spustit, až do vypršení platnosti naplánovaném čase. Nastavit okamžitou zálohu, proveďte další krok.

8.  Na **myVault - zálohování položek** okno, v části **zálohování počet položek**, vyberte počet zálohování položek.

    ![Stránka Podrobnosti o obnovení služby trezory myVault](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na **zálohování položek (virtuální počítač Azure)** okno na pravé straně stránky klikněte na tlačítko se třemi tečkami (**...** ) tlačítko a potom klikněte na **zálohovat nyní**.

    ![Zálohování teď příkaz trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klikněte **zálohování** tlačítko. Počkejte na dokončení procesu zálohování. Potom přejděte na [krok 6: odebrat soubory databáze](#step-6-remove-the-database-files).

    Chcete-li zobrazit stav úlohy zálohování, klikněte na tlačítko **úlohy**.

    ![Trezory služeb zotavení úlohy stránky](./media/oracle-backup-recovery/recovery_service_10.png)

    Na následujícím obrázku se zobrazí stav úlohy zálohování:

    ![Stránka se stavem úlohy trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_11.png)

11. Zálohování konzistentní s aplikací vyřešte všechny chyby v souboru protokolu. Soubor protokolu se nachází v /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Odebrat soubory databáze 
Později v tomto článku se dozvíte testování procesu obnovení. Chcete-li otestovat proces obnovení, budete muset odebrat soubory databáze.

1.  Odebrání souborů tabulkového prostoru a zálohování:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Volitelné) Vypněte instanci Oracle:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Obnovení odstraněných souborů z trezory služeb zotavení
Obnovení odstraněných souborů, proveďte následující kroky:

1. Na portálu Azure, vyhledejte *myVault* položky trezory služeb zotavení. Na **přehled** okno, v části **zálohování položek**, vyberte počet položek.

    ![Obnovení služby trezory myVault zálohování položek](./media/oracle-backup-recovery/recovery_service_12.png)

2. V části **počet položek zálohování**, vyberte počet položek.

    ![Počet položek. zálohování virtuálního počítače Azure trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na **myvm1** okně klikněte na tlačítko **obnovení souboru (Preview)**.

    ![Snímek obrazovky služeb zotavení trezory stránka obnovení souboru](./media/oracle-backup-recovery/recovery_service_14.png)

4. Na **obnovení souboru (Preview)** podokně klikněte na tlačítko **stáhnout skript**. Potom uložte soubor ke stažení (.sh) do složky v klientském počítači.

    ![Stáhnout možnosti uloží soubor skriptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Zkopírujte soubor .sh k virtuálnímu počítači.

    Následující příklad ukazuje, jak používat zabezpečený kopie (scp) příkaz přesunout soubor k virtuálnímu počítači. Také můžete zkopírovat obsah do schránky a vložte obsah do nového souboru, který je nastavený na virtuálním počítači.

    > [!IMPORTANT]
    > V následujícím příkladu Ujistěte se, aktualizaci hodnoty IP adres a složky. Hodnoty musí být mapována na složku, kam je uložena souboru.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Změňte souboru tak, aby vlastníkem kořenu.

    V následujícím příkladu změňte soubor tak, aby vlastníkem kořenu. Potom změňte oprávnění.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    Následující příklad ukazuje, co byste měli vidět po spuštění uvedený skript. Když se zobrazí výzva, chcete-li pokračovat, zadejte **Y**.

    ```bash
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

7. Přístup k připojené svazky je potvrzen.

    Chcete-li ukončit, zadejte **q**a poté vyhledejte připojených svazků. Chcete-li vytvořit seznam přidaných svazky, na příkazovém řádku, zadejte **df – KB**.

    ![-K DF – příkaz](./media/oracle-backup-recovery/recovery_service_16.png)

8. Ke zkopírování chybějící soubory zpět do složky použijte následující skript:

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
9. V následující skript použijte k obnovení databázi RMAN:

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

    Na portálu Azure na **obnovení souboru (Preview)** okně klikněte na tlačítko **odpojit disky**.

    ![Odpojení příkaz disky](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Obnovit celý virtuální počítač

Místo obnovení odstraněných souborů ze trezory služeb zotavení, můžete obnovit celý virtuální počítač.

### <a name="step-1-delete-myvm"></a>Krok 1: Odstranění Můjvp

*   V portálu Azure přejděte do **myVM1** trezoru a potom vyberte **odstranit**.

    ![Příkaz delete trezoru](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Obnovení virtuálního počítače

1.  Přejděte na **trezory služeb zotavení**a potom vyberte **myVault**.

    ![Položka myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na **přehled** okno, v části **zálohování položek**, vyberte počet položek.

    ![myVault zálohování položek](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na **zálohování položek (virtuální počítač Azure)** vyberte **myvm1**.

    ![Stránku obnovení virtuálního počítače](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na **myvm1** okně klikněte na tlačítko se třemi tečkami (**...** ) tlačítko a potom klikněte na **obnovit virtuální počítač**.

    ![Virtuální počítač příkazu Obnovit](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na **vyberte bod obnovení** okno, vyberte položku, kterou chcete obnovit a pak klikněte na tlačítko **OK**.

    ![Vyberte bod obnovení](./media/oracle-backup-recovery/recover_vm_06.png)

    Pokud jste povolili zálohování konzistentní s aplikací, se zobrazí modré svislá čára.

6.  Na **obnovit konfiguraci** okně, vyberte název virtuálního počítače, vyberte skupinu prostředků a pak klikněte na tlačítko **OK**.

    ![Obnovení hodnoty konfigurace](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Chcete-li obnovit virtuální počítač, klikněte na tlačítko **obnovení** tlačítko.

8.  Chcete-li zobrazit stav procesu obnovení, klikněte na tlačítko **úlohy**a potom klikněte na **úlohy zálohování**.

    ![Příkaz Stav úlohy zálohování](./media/oracle-backup-recovery/recover_vm_08.png)

    Následující obrázek ukazuje stav procesu obnovení:

    ![Stav procesu obnovení](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Nastavte veřejnou IP adresu
Po obnovení virtuálního počítače, nastavte veřejnou IP adresu.

1.  Do vyhledávacího pole zadejte **veřejnou IP adresu**.

    ![Seznam veřejné IP adresy](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na **veřejné IP adresy** okně klikněte na tlačítko **přidat**. Na **vytvoření veřejné IP adresy** okně pro **název**, vyberte název veřejné IP adresy. V části **Skupina prostředků** vyberte **Použít existující**. Poté klikněte na možnost **Vytvořit**.

    ![Vytvoření IP adresy](./media/oracle-backup-recovery/create_ip_01.png)

3.  Chcete-li přidružit veřejnou IP adresu pro virtuální počítač se síťovým rozhraním, vyhledejte a vyberte **myVMip**. Potom klikněte na **přidružit**.

    ![Přiřadit IP adresu](./media/oracle-backup-recovery/create_ip_02.png)

4.  Pro **typ prostředku**, vyberte **síťové rozhraní**. Vyberte síťové rozhraní, které používá Můjvp instance a pak klikněte na tlačítko **OK**.

    ![Vyberte typ prostředku a hodnoty síťový adaptér](./media/oracle-backup-recovery/create_ip_03.png)

5.  Vyhledejte a otevřete instanci Můjvp, který je přenést z portálu. IP adresu, která souvisí s virtuálním Počítačem se zobrazí na Můjvp **přehled** okno.

    ![IP adresa hodnotu](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Připojení k virtuálnímu počítači

*   Pokud chcete připojit k virtuálnímu počítači, použijte následující skript:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: Test, zda databáze je přístupná
*   K otestování usnadnění, použijte následující skript:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Pokud databázi **spuštění** příkaz, vygeneruje se chyba, chcete-li obnovit databázi, přečtěte si téma [krok 6: RMAN použijte k obnovení databázi](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (Volitelné) použijte RMAN k obnovení databáze
*   Chcete-li obnovit databázi, použijte následující skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Zálohování a obnovení databáze 12c databáze Oracle na virtuální počítač Azure Linux je nyní dokončena.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Když virtuální počítač již nepotřebujete, můžete odebrat skupinu prostředků, virtuální počítač a všechny související prostředky následující příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

[Kurz: Vytvoření vysoce dostupné virtuální počítače](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky rozhraní příkazového řádku Azure nasazení virtuálních počítačů](../../linux/cli-samples.md)



