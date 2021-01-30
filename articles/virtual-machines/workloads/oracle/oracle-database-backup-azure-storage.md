---
title: Zálohování Oracle Database databáze 19c na virtuálním počítači Azure Linux pomocí RMAN a Azure Storage
description: Naučte se, jak zálohovat databázi Oracle Database 19c do cloudového úložiště Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 695f151e6d6cc0a677942f60c751567da0cfca7c
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063879"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Zálohování a obnovení databáze Oracle Database 19c na virtuálním počítači Azure Linux pomocí Azure Storage

Tento článek ukazuje použití Azure Storage jako média k zálohování a obnovení databáze Oracle běžící na virtuálním počítači Azure. Databázi zazálohujte pomocí Oracle RMAN do služby Azure File Storage připojené k virtuálnímu počítači pomocí protokolu SMB. Použití Azure Storage pro záložní média je mimořádně nákladově efektivní a výkonné. U velmi rozsáhlých databází ale Azure Backup nabízí lepší řešení.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- Chcete-li provést proces zálohování a obnovení, je třeba nejprve vytvořit virtuální počítač se systémem Linux s nainstalovanou instancí Oracle Database 19c. Image Marketplace, která se právě používá k vytvoření virtuálního počítače, je  **Oracle: Oracle-Database-19-3: Oracle-Database-19-0904: nejnovější**. Postupujte podle kroků v [rychlém startu k vytvoření databáze Oracle](./oracle-database-quick-create.md) a vytvořte databázi Oracle pro dokončení tohoto kurzu.

## <a name="prepare-the-database-environment"></a>Příprava databázového prostředí

1. Pokud chcete vytvořit relaci Secure Shell (SSH) s virtuálním počítačem, použijte následující příkaz. Nahraďte IP adresu a název hostitele kombinací `publicIpAddress` hodnoty pro váš virtuální počítač.
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. Přepněte na uživatele *_root_* _:
 
   ```bash
   sudo su -
   ```
    
3. Přidejte uživatele Oracle do _*_ souboru/etc/sudoers_ * _:

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. Tento krok předpokládá, že máte instanci Oracle (test), která je spuštěná na virtuálním počítači s názvem _vmoracle19c *.

   Přepněte uživatele na uživatele *Oracle* :

   ```bash
   sudo su - oracle
   ```
    
5. Než se připojíte, musíte nastavit proměnnou prostředí ORACLE_SID:
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   Měli byste taky přidat ORACLE_SID proměnnou do `oracle` `.bashrc` souboru uživatelů pro budoucí přihlášení pomocí následujícího příkazu:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Spusťte naslouchací proces Oracle, pokud ještě není spuštěný:
    
   ```bash
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

7.  Vytvořte umístění oblasti rychlé obnovy (v/v):

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  Připojit k databázi:

    ```bash
    sqlplus / as sysdba
    ```

9.  Spusťte databázi, pokud ještě není spuštěná:

    ```bash
    SQL> startup
    ```

10. Nastavte proměnné prostředí databáze pro oblast rychlé obnovení:

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. Zajistěte, aby byla databáze v režimu protokolu archivace, aby bylo možné povolit online zálohování.
    Nejprve ověřte stav archivu protokolu:

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    A v režimu NOARCHIVELOG spusťte v sqlplus následující příkazy:

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. Vytvořte tabulku pro otestování operací zálohování a obnovení:

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Zálohování do souborů Azure

Pokud chcete zálohovat do služby soubory Azure, proveďte tyto kroky:

1. Nastavte Azure File Storage.
1. Připojte sdílenou složku Azure Storage k vašemu VIRTUÁLNÍmu počítači.
1. Zazálohujte databázi.
1. Obnovte a obnovte databázi.

### <a name="set-up-azure-file-storage"></a>Nastavení File Storage Azure

V tomto kroku zálohujete databázi Oracle pomocí Oracle Recovery Manager (RMAN) do služby Azure File Storage. Sdílené složky Azure jsou plně spravované sdílené složky, které jsou v cloudu živé. K nim lze přistupovat buď pomocí protokolu SMB (Server Message Block), nebo protokolu NFS (Network File System). Tento krok popisuje vytvoření sdílené složky, která pro připojení k VIRTUÁLNÍmu počítači používá protokol SMB. Informace o tom, jak se připojit pomocí systému souborů NFS, najdete v tématu [připojení úložiště objektů BLOB pomocí protokolu nfs 3,0](../../../storage/blobs/network-file-system-protocol-support-how-to.md).

Při připojování souborů Azure použijeme `cache=none` k zakázání ukládání dat sdílené složky do mezipaměti. A pokud chcete zajistit, aby soubory vytvořené ve sdílené složce byly vlastněné `uid=oracle` možnostmi a i uživateli Oracle `gid=oinstall` . 

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Nejdřív nastavte svůj účet úložiště.

1. Konfigurace File Storage v Azure Portal

    V Azure Portal vyberte ***+ vytvořit prostředek** _ a vyhledejte a vyberte _*_účet úložiště_*_ .
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/storage-1.png)
    
2. Na stránce Vytvořit účet úložiště vyberte existující skupinu prostředků _*_RG-Oracle_*_, pojmenujte svůj účet úložiště _*_Oracbkup1_*_ a pro druh účtu vyberte _*_Storage v2 (GeneralPurpose v2)_*_ . Změňte replikaci na _*_místně redundantní úložiště (LRS)_*_ a nastavte výkon na _*_standardní_*_. Zajistěte, aby se umístění nastavilo na stejnou oblast jako všechny ostatní prostředky ve skupině prostředků. 
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. Klikněte na kartu _*_Upřesnit_*_ a v části soubory Azure nastavte možnost _*_velké sdílené složky_*_ na _*_povoleno_*_. Klikněte na tlačítko revize + vytvořit a potom klikněte na tlačítko vytvořit.
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. Po vytvoření účtu úložiště přejít do prostředku a vybrat _*_sdílené složky_*_
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/file-storage-3.png)
    
5. Klikněte na _*_ + soubor share_ *_ a v okně _*_Nová sdílená složka_ _ *pojmenujte svůj sdílený soubor _*_orabkup1_*_. Nastavte*_kvótu __*na _*_10240_*_ GIB a kontrolu*_transakce_ _ *jako vrstvu. Kvóta odráží horní hranici, na kterou může sdílená složka dosáhnout. Když používáme úložiště úrovně Standard, prostředky se PAYG a nezřídí, takže nastavením na 10 TiB se neúčtují náklady nad rámec toho, co využijete. Pokud vaše strategie zálohování vyžaduje větší úložiště, je nutné nastavit kvótu na odpovídající úroveň pro uchování všech záloh.   Až dokončíte okno Nová sdílená složka, klikněte na _*_vytvořit_* _.
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. Po vytvoření klikněte na _*_orabkup1_*_ na stránce nastavení sdílení souborů. 
    Kliknutím na kartu _*_připojit_*_ otevřete okno připojit a pak klikněte na kartu _*_Linux_*_ . Zkopírujte zadané příkazy pro připojení sdílené složky pomocí protokolu SMB. 
    
    ![Přidat stránku účtu úložiště](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nastavit účet úložiště a sdílení souborů, spusťte v Azure CLI následující příkazy.

1. Vytvořte účet úložiště ve stejné skupině prostředků a umístění jako váš virtuální počítač:
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. V účtu úložiště vytvořte sdílenou složku s kvótou 10 TiB:

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. Načtěte primární klíč účtu úložiště (klíč1), který je potřeba při připojování sdílené složky k VIRTUÁLNÍmu počítači:

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Připojení sdílené složky Azure Storage k VIRTUÁLNÍmu počítači

1. Vytvořte přípojný bod:

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. Nastavit přihlašovací údaje:

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   `<Your Storage Account Key1>`Před spuštěním následujícího příkazu nahraďte dříve načtený klíč účtu úložiště:
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. Změnit oprávnění k souboru s přihlašovacími údaji:

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. Přidejte připojení k/etc/fstab:

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. Spusťte příkazy pro připojení úložiště Azure Files pomocí protokolu SMB:

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   Pokud se zobrazí chybová zpráva podobná této:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   balíček CIFS se pak nemusí nainstalovat na hostitele se systémem Linux. 
   
   Pokud chcete zjistit, jestli je nainstalovaná služba CI, spusťte následující příkaz:

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   Pokud příkaz nevrátí žádný výstup, nainstalujte balíček CIFS pomocí následujícího příkazu:

   ```bash 
   sudo yum install cifs-utils
   ```

   Teď znovu spusťte příkaz Mount a připojte úložiště služby soubory Azure.

6. Ověřte, zda je sdílená složka správně připojena pomocí následujícího příkazu:

   ```bash
   df -h
   ```

   Výstup by měl vypadat zhruba takto:

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>Zálohování databáze

V této části budeme pomocí Oracle Recovery Manager (RMAN) pořizovat kompletní zálohu databáze a protokolů archivace a zapíšeme zálohu jako zálohovací sklad, který jste připojili k dříve připojenému sdílenému souboru Azure. 

1. Nakonfigurujte RMAN pro zálohování do přípojného bodu služby soubory Azure:

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Vzhledem k tomu, že standardní sdílené složky Azure mají maximální velikost souboru 1 TiB, omezíme velikost částí zálohy RMAN na 1 TiB. (U sdílených souborů úrovně Premium je maximální velikost souboru 4 TiB. Další informace najdete v tématu [škálovatelnost a cíle výkonnosti souborů Azure](../../../storage/files/storage-files-scale-targets.md).)

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. Potvrďte podrobnosti změny konfigurace:

    ```bash
    RMAN> show all;
    ```

4. Nyní spusťte zálohování. Následující příkaz provede úplnou zálohu databáze, včetně archivovaných protokolů, jako záložní soubory v komprimovaném formátu:   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

Nyní jste zálohovali databázi online pomocí Oracle RMAN se zálohou uloženou ve službě Azure File Storage. Tato metoda má za následek využití funkcí RMAN při ukládání záloh do služby Azure File Storage, kde se dají dostat z jiných virtuálních počítačů, které jsou užitečné, pokud potřebujete klonovat databázi.  
    
Při použití RMAN a služby Azure File Storage pro zálohování databáze má mnoho výhod, ale čas zálohování a obnovení je propojen s velikostí databáze, takže pro velmi velké databáze může pro tyto operace trvat značnou dobu.  Alternativním zálohovacím mechanismem, který používá Azure Backup zálohování virtuálních počítačů konzistentních vzhledem k aplikacím, používá k zálohování technologii snímků, která využívá velmi rychlé zálohování bez ohledu na velikost databáze. Integrace s Recovery Services trezor poskytuje zabezpečené úložiště vašich Oracle Database zálohování v cloudovém úložišti Azure přístupném z jiných virtuálních počítačů a oblastí Azure. 

### <a name="restore-and-recover-the-database"></a>Obnovení a obnovení databáze

1.  Vypněte instanci Oracle:

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  Odeberte soubory a zálohy:

    ```bash
    cd /u02/oradata/TEST
    rm -f _.dbf
    ```

3. Následující příkazy používají RMAN k obnovení chybějících souborů datafiles a obnovení databáze:

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. Ověřte, zda byl obsah databáze zcela obnoven:

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


Zálohování a obnovení databáze Oracle Database 19c na virtuálním počítači Azure Linux je nyní dokončeno.

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud už virtuální počítač nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít následující příkaz:

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>Další kroky

[Kurz: Vytvoření virtuálních počítačů s vysokou dostupností](../../linux/create-cli-complete.md)

[Ukázky ukázek Azure CLI pro nasazení virtuálních počítačů](../../linux/cli-samples.md)
