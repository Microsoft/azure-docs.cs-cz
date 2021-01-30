---
title: Vytvoření databáze Oracle ve virtuálním počítači Azure | Microsoft Docs
description: Rychle získáte Oracle Database databázi 12c v prostředí Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: a202c8d176d6b9a8893a7bc5aaad6771942dda04
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063058"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Vytvoření Oracle Database na virtuálním počítači Azure

Tato příručka podrobně popisuje použití rozhraní příkazového řádku Azure k nasazení virtuálního počítače Azure z [Image Galerie Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) , aby bylo možné vytvořit databázi Oracle 19c. Po nasazení serveru se připojíte přes SSH, aby se nakonfigurovala databáze Oracle. 

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků s názvem *RG-Oracle* v umístění *eastus* .

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Pokud chcete vytvořit virtuální počítač, použijte příkaz [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří virtuální počítač `vmoracle19c`. Také vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí podobné informace jako v následujícím příkladu. Všimněte si hodnoty pro `publicIpAddress` . Tuto adresu použijete pro přístup k virtuálnímu počítači.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Vytvoření a připojení nového disku pro DataFile a Oracle pro Oracle

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>Otevření portů pro připojení
V této úloze musíte nakonfigurovat některé externí koncové body pro naslouchací proces databáze a EM Express, aby je bylo možné použít, nastavením skupiny zabezpečení sítě Azure, která chrání virtuální počítač. 

1. Chcete-li otevřít koncový bod, který používáte pro vzdálený přístup k databázi Oracle, vytvořte pravidlo skupiny zabezpečení sítě následujícím způsobem:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. Pokud chcete otevřít koncový bod, který používáte pro přístup k Oracle EM Express vzdáleně, vytvořte pravidlo skupiny zabezpečení sítě pomocí příkazu AZ Network NSG Rule Create následujícím způsobem:
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. V případě potřeby Získejte veřejnou IP adresu virtuálního počítače znovu pomocí příkazu AZ Network Public-IP show takto:

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>Příprava prostředí virtuálních počítačů

1. Připojení k virtuálnímu počítači

   Pokud chcete vytvořit relaci SSH s virtuálním počítačem, použijte následující příkaz. Nahraďte IP adresu `publicIpAddress` hodnotou svého virtuálního počítače.

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. Přepnout na uživatele root

   ```bash
   sudo su -
   ```

3. Vyhledejte naposledy vytvořené diskové zařízení, ve kterém naformátujete, aby se používaly soubory Oracle DataFile.

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   Výstup bude vypadat přibližně takto:
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. Naformátujte zařízení. 
   Jak se na zařízení spouští uživatel root 
   
   Nejdřív vytvořte Popisek disku:
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   Pak vytvořte primární oddíl zahrnující celý disk:
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   Nakonec se podívejte na podrobnosti o zařízení vytištěním jeho metadat:
   ```bash
   parted /dev/sdc print
   ```
   Výstup by měl vypadat zhruba takto:
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. Vytvoření systému souborů v oddílu zařízení

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. Vytvořit přípojný bod
   ```bash
   mkdir /u02
   ```

7. Připojit disk

   ```bash
   mount /dev/sdc1 /u02
   ```

8. Změna oprávnění pro přípojný bod

   ```bash
   chmod 777 /u02
   ```

9. Přidejte připojení k souboru/etc/fstab. 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. Aktualizujte soubor ***/etc/hosts** _ pomocí veřejné IP adresy a názvu hostitele.

    Změňte _*_veřejnou IP adresu a VMname_*_ tak, aby odrážely vaše skutečné hodnoty:
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. Aktualizace souboru názvu hostitele
    
    Pomocí následujícího příkazu přidejte do souboru _ */etc/hostname** název domény virtuálního počítače. Tím se předpokládá, že jste vytvořili skupinu prostředků a virtuální počítač v oblasti **eastus** :
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. Otevřít porty brány firewall
    
    Ve výchozím nastavení je na imagi na webu Marketplace povolená služba SELinux, a proto je nutné otevřít bránu firewall pro přenos dat pro port naslouchání databáze 1521 a Enterprise Manager Express port 5502. Pro uživatele root spusťte následující příkazy:

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>Vytvoření databáze

V imagi na webu Marketplace už je nainstalovaný software Oracle. Vytvořte ukázkovou databázi následujícím způsobem. 

1.  Přepněte na uživatele **Oracle** :

    ```bash
    $ sudo su - oracle
    ```
2. Spustit naslouchací proces databáze

   ```bash
   $ lsnrctl start
   ```
   Výstup je podobný tomuto:
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Vytvořte datový adresář pro datové soubory Oracle:

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Spusťte pomocníka pro vytváření databáze:

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    Vytvoření databáze trvá několik minut.

    Zobrazí se výstup, který vypadá podobně jako následující:

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Nastavit proměnné Oracle

    Než se připojíte, musíte nastavit proměnnou prostředí *ORACLE_SID*:

    ```bash
        export ORACLE_SID=test
    ```

    Měli byste taky přidat ORACLE_SID proměnnou do `oracle` `.bashrc` souboru uživatelů pro budoucí přihlášení pomocí následujícího příkazu:

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Připojení Oracle EM Express

Pro nástroj pro správu grafického uživatelského rozhraní, který můžete použít k prozkoumání databáze, nastavte Oracle EM Express. Pokud se chcete připojit k Oracle EM Express, musíte nejdřív nastavit port v Oracle. 

1. Připojte se k databázi pomocí sqlplus:

    ```bash
    sqlplus sys as sysdba
    ```

2. Po připojení nastavte port 5502 pro EM Express.

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  Připojte EM Express z prohlížeče. Ujistěte se, že je váš prohlížeč kompatibilní s EM Express (vyžaduje se instalace Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    Můžete se přihlásit pomocí účtu **sys** a zaškrtnout políčko **jako SYSDBA** . Použijte **OraPasswd1** hesla, které jste nastavili během instalace. 

    ![Snímek obrazovky se stránkou pro přihlášení Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>Automatizace spuštění a vypnutí databáze

Databáze Oracle se ve výchozím nastavení automaticky nespustí po restartování virtuálního počítače. Chcete-li nastavit automatické spuštění databáze Oracle, nejprve se přihlaste jako kořenový adresář. Pak vytvořte a aktualizujte některé systémové soubory.

1. Přihlásit se jako kořen

    ```bash
    sudo su -
    ```

2.  Spusťte následující příkaz pro změnu automatického příznaku spuštění z `N` na `Y` v `/etc/oratab` souboru:

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  Vytvořte soubor s názvem `/etc/init.d/dbora` a vložte následující obsah:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Změňte oprávnění k souborům pomocí *chmod* následujícím způsobem:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Vytvořte symbolické odkazy pro spuštění a vypnutí následujícím způsobem:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Chcete-li otestovat změny, restartujte virtuální počítač:

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile dokončíte zkoumání první databáze Oracle v Azure a virtuální počítač už není potřeba, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít příkaz [AZ Group Delete](/cli/azure/group) .

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak chránit vaši databázi v Azure pomocí [strategií zálohování Oracle](./oracle-database-backup-strategies.md)

Seznamte se [s dalšími řešeními Oracle v Azure](./oracle-overview.md). 

Vyzkoušejte kurz [instalace a konfigurace Oracle Automated Storage Management](configure-oracle-asm.md) .
