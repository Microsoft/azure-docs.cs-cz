---
title: Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak zálohovat a obnovit databázi Oracle Database 12c v prostředí Azure.
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
ms.openlocfilehash: 93fbd5bbba91b45e1afd123a2466b249302e2354
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492836"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Zálohování a obnovení databáze Oracle Database 12c na virtuálním počítači Azure s Linuxem

Rozhraní příkazového řádku Azure můžete použít k vytvoření a správě prostředků Azure z příkazového řádku nebo pomocí skriptů. V tomto článku používáme skripty Azure CLI nasadit databázi Oracle Database 12c z image Galerie Azure Marketplace.

Než začnete, ujistěte se, že je nainstalované rozhraní příkazového řádku Azure. Další informace najdete v tématu [Průvodce instalací Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Příprava prostředí

### <a name="step-1-prerequisites"></a>Krok 1: požadavky

*   Chcete-li provést proces zálohování a obnovení, musíte nejdřív vytvořit virtuální počítač s Linuxem, který má nainstalované instance Oracle Database 12c. Název image Marketplace, který použijete k vytvoření virtuálního počítače *Oracle: Oracle – databáze-Ee:12.1.0.2:latest*.

    Zjistěte, jak vytvořit databázi Oracle, najdete v článku [vytvořit rychlý start databáze Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Krok 2: Připojení k virtuálnímu počítači

*   Vytvořte s virtuálním Počítačem relaci Secure Shell (SSH), použijte následující příkaz. Nahraďte IP adresu a kombinace názvu hostitele s `publicIpAddress` hodnoty pro váš virtuální počítač.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Krok 3: Příprava databáze

1.  Tento krok předpokládá, že máte instanci Oracle (cdb1), na kterém běží na virtuálním počítači s názvem *myVM*.

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

2.  (Volitelné) Ujistěte se, že databáze je v režimu archivaci protokolu:

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
3.  (Volitelné) Vytvořte tabulku pro testování potvrzení změn:

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
4.  Ověřit nebo změnit umístění souboru zálohy a velikost:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Vytvořte zálohu databáze pomocí Správce obnovení Oracle (RMAN):

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Krok 4: Konzistentní zálohování virtuálních počítačů s Linuxem

Zálohy konzistentní s aplikací je nová funkce ve službě Azure Backup. Můžete vytvořit a vybrat skripty před a po snímek virtuálního počítače (předsnímkových a po pořízení snímku).

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

2. Vytvořte složku /etc/azure na virtuálním počítači:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Zkopírujte soubor JSON.

    Zkopírujte do složky /etc/azure VMSnapshotScriptPluginConfig.json.

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

5. Vytvoření souborů předsnímkového a posnímkových skriptů.

    Tady je příklad předsnímkového a posnímkových skriptů pro "úplné zálohování" (offline zálohování, vypnutí a restartování):

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

    Tady je příklad předsnímkového a posnímkových skriptů pro "hot zálohování" (online zálohování):

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

    /Etc/azure/pre_script.sql upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /Etc/azure/post_script.sql upravte obsah souboru podle vašich požadavků:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Změňte oprávnění k souboru:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Otestujte skripty.

    Otestovat skripty, nejdřív přihlaste jako uživatel root. Zkontrolujte, že zde nejsou žádné chyby:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Další informace najdete v tématu [konzistentní zálohování virtuálních počítačů s Linuxem](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Krok 5: Použití Azure trezory služby Recovery Services pro zálohování virtuálního počítače

1.  Na webu Azure Portal vyhledejte **trezory služby Recovery Services**.

    ![Stránce trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Na **trezory služby Recovery Services** okno pro přidání nového trezoru, klikněte na tlačítko **přidat**.

    ![Trezory služby Recovery Services přidat stránku](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Chcete-li pokračovat, klikněte na tlačítko **myVault**.

    ![Stránku podrobností trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Na **myVault** okna, klikněte na tlačítko **zálohování**.

    ![Stránce zálohování pro trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Na **cíl zálohování** okně použít výchozí hodnoty **Azure** a **virtuálního počítače**. Klikněte na **OK**.

    ![Stránku podrobností trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Pro **zásady zálohování**, použijte **DefaultPolicy**, nebo vyberte **vytvořit novou zásadu**. Klikněte na **OK**.

    ![Stránky podrobností zásad zálohování trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Na **výběr virtuálních počítačů** okno, vyberte **myVM1** zaškrtněte políčko a potom klikněte na tlačítko **OK**. Klikněte na tlačítko **povolit zálohování** tlačítko.

    ![Položek trezory služeb zotavení na stránku podrobností zálohování](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Po kliknutí na **povolit zálohování**, proces zálohování se nespustí, dokud nevyprší platnost naplánovaném čase. Chcete-li nastavit okamžitého zálohování, dokončení dalšího kroku.

8.  Na **myVault - zálohování položek** okně v části **zálohování počet položek**, vyberte počet zálohovaných položek.

    ![Stránku podrobností myVault trezory služeb zotavení](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Na **položky zálohování (virtuální počítač Azure)** okna na pravé straně stránky klikněte na tlačítko se třemi tečkami (**...** ) tlačítko a pak klikněte na tlačítko **zálohovat nyní**.

    ![Příkaz Backup trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_09.png)

10. Klikněte na tlačítko **zálohování** tlačítko. Počkejte na dokončení procesu zálohování. Přejděte ke [krok 6: odebrat soubory databáze](#step-6-remove-the-database-files).

    Chcete-li zobrazit stav úlohy zálohování, klikněte na tlačítko **úlohy**.

    ![Trezory služby Recovery Services úlohu stránky](./media/oracle-backup-recovery/recovery_service_10.png)

    Stav úlohy zálohování se zobrazí na následujícím obrázku:

    ![Trezory služby Recovery Services úlohu stránku se stavem](./media/oracle-backup-recovery/recovery_service_11.png)

11. Konzistentní zálohování vyřešit všechny chyby v souboru protokolu. Soubor protokolu je umístěn v /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Krok 6: Odebrat soubory databáze 
Dále v tomto článku se dozvíte, jak otestovat proces obnovení. Abyste ji mohli otestovat proces obnovení, budete muset odebrat soubory databáze.

1.  Odeberte soubory tabulkového prostoru a zálohování:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Volitelné) Vypnutí Oracle instance:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Obnovení odstraněných souborů z trezorů služby Recovery Services
Pokud chcete obnovit odstraněné soubory, proveďte následující kroky:

1. Na webu Azure Portal, vyhledejte *myVault* položku trezory služby Recovery Services. Na **přehled** okně v části **zálohování položek**, vyberte počet položek.

    ![Obnovení služby trezory myVault zálohované položky](./media/oracle-backup-recovery/recovery_service_12.png)

2. V části **počet položek zálohování**, vyberte počet položek.

    ![Počet zálohovaných položek virtuálního počítače Azure trezory služby Recovery Services](./media/oracle-backup-recovery/recovery_service_13.png)

3. Na **myvm1** okna, klikněte na tlačítko **obnovení souborů (Preview)**.

    ![Snímek obrazovky služby Recovery Services trezory Recovery Services stránku obnovení souboru](./media/oracle-backup-recovery/recovery_service_14.png)

4. Na **obnovení souborů (Preview)** podokně klikněte na tlačítko **stáhnout skript**. Potom uložte soubor ke stažení (.sh) do složky v klientském počítači.

    ![Stáhněte si možnosti ukládání souboru skriptu](./media/oracle-backup-recovery/recovery_service_15.png)

5. Zkopírujte soubor .sh k virtuálnímu počítači.

    Následující příklad ukazuje, jak můžete používat zabezpečené kopírování (scp) příkaz přesunout soubor do virtuálního počítače. Také můžete zkopírovat obsah do schránky a vložte obsah do nového souboru, který je nastaven na virtuálním počítači.

    > [!IMPORTANT]
    > V následujícím příkladu Ujistěte se, aktualizaci hodnoty IP adres a složky. Hodnoty musí být namapovaný na složku, ve kterém je soubor uložen.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Soubor, změňte tak, aby vlastníkem je kořenový adresář.

    V následujícím příkladu změňte ho tak, aby vlastníkem je kořenový adresář. Změňte oprávnění.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    Následující příklad ukazuje, co byste měli vidět po spuštění předchozího skriptu. Když se zobrazí výzva, abyste mohli pokračovat, zadejte **Y**.

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

7. Přístup k připojené svazky je potvrzeno.

    Chcete-li ukončit, zadejte **q**a poté vyhledejte připojené svazky. Chcete-li vytvořit seznam přidaných svazků, z příkazového řádku, zadejte **df -k**.

    ![Příkaz df – k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Pomocí následujícího skriptu zkopírujte chybějících souborů zpět do složek:

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
9. V následujícím skriptu použijte RMAN pro obnovení databáze:

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

    Na webu Azure Portal na **obnovení souborů (Preview)** okna, klikněte na tlačítko **odpojit disky**.

    ![Odpojte disky příkaz](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Obnovit celý virtuální počítač

Místo obnovení odstraněných souborů z trezorů služby Recovery Services, můžete obnovit celý virtuální počítač.

### <a name="step-1-delete-myvm"></a>Krok 1: Odstranění myVM

*   Na webu Azure Portal, přejděte **myVM1** trezoru a pak vyberte **odstranit**.

    ![Příkazem k odstranění trezoru](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Krok 2: Obnovení virtuálního počítače

1.  Přejděte na **trezory služby Recovery Services**a pak vyberte **myVault**.

    ![Položka myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Na **přehled** okně v části **zálohování položek**, vyberte počet položek.

    ![myVault zálohování položek](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Na **položky zálohování (virtuální počítač Azure)** okně vyberte **myvm1**.

    ![Obnovení virtuálního počítače stránky](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Na **myvm1** okna, klikněte na tlačítko se třemi tečkami (**...** ) tlačítko a pak klikněte na tlačítko **obnovení virtuálního počítače**.

    ![Obnovení virtuálního počítače příkaz](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Na **vyberte bod obnovení** okno, vyberte položku, kterou chcete obnovit a pak klikněte na tlačítko **OK**.

    ![Vyberte bod obnovení](./media/oracle-backup-recovery/recover_vm_06.png)

    Pokud jste povolili zálohování konzistentní s aplikací, se zobrazí modré svislý pruh.

6.  Na **obnovit konfiguraci** okno, vyberte název virtuálního počítače, vyberte skupinu prostředků a pak klikněte na tlačítko **OK**.

    ![Obnovení hodnoty konfigurace](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Pokud chcete obnovit virtuální počítač, klikněte na tlačítko **obnovení** tlačítko.

8.  Chcete-li zobrazit stav procesu obnovení, klikněte na tlačítko **úlohy**a potom klikněte na tlačítko **úlohy zálohování**.

    ![Příkaz Stav úlohy zálohování](./media/oracle-backup-recovery/recover_vm_08.png)

    Následující obrázek ukazuje stav procesu obnovení:

    ![Stav obnovení procesu](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Krok 3: Nastavte veřejnou IP adresu
Po obnovení virtuálního počítače nastavte veřejnou IP adresu.

1.  Do vyhledávacího pole zadejte **veřejnou IP adresu**.

    ![Seznam veřejných IP adres](./media/oracle-backup-recovery/create_ip_00.png)

2.  Na **veřejné IP adresy** okna, klikněte na tlačítko **přidat**. Na **vytvoření veřejné IP adresy** okně pro **název**, vyberte název veřejné IP. V části **Skupina prostředků** vyberte **Použít existující**. Poté klikněte na možnost **Vytvořit**.

    ![Vytvoření IP adresy](./media/oracle-backup-recovery/create_ip_01.png)

3.  Přidružit veřejnou IP adresu pro virtuální počítač se síťovým rozhraním, vyhledejte a vyberte **myVMip**. Potom klikněte na **přidružit**.

    ![Přidružení IP adresy](./media/oracle-backup-recovery/create_ip_02.png)

4.  Pro **typ prostředku**vyberte **síťové rozhraní**. Vyberte síťové rozhraní, které používá myVM instance a potom klikněte na tlačítko **OK**.

    ![Vyberte typ prostředku a hodnoty síťové karty](./media/oracle-backup-recovery/create_ip_03.png)

5.  Vyhledejte a spusťte instanci myVM, který se přenáší z portálu. IP adresa, která souvisí s virtuálním Počítačem se zobrazí na myVM **přehled** okno.

    ![Hodnotu IP adresy](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Krok 4: Připojení k virtuálnímu počítači

*   Pro připojení k virtuálnímu počítači, použijte tento skript:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Krok 5: Testování, zda databáze je přístupná
*   K otestování přístupnost, použijte následující skript:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Pokud databáze **spuštění** příkaz vygeneruje chybu, pokud chcete obnovit databázi, naleznete v tématu [krok 6: použití RMAN pro obnovení databáze](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Krok 6: (Volitelné) použít RMAN pro obnovení databáze
*   Pokud chcete obnovit databázi, použijte tento skript:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

Zálohování a obnovení databáze Oracle Database 12c na Linuxovém virtuálním počítači Azure je nyní dokončena.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud už nepotřebujete, virtuálnímu počítači, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků příkaz:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

[Kurz: Vytvoření vysoce dostupných virtuálních počítačů](../../linux/create-cli-complete.md)

[Prozkoumejte ukázky nasazení virtuálního počítače pomocí Azure CLI](../../linux/cli-samples.md)



