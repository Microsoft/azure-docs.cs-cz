---
title: Vytvoření databáze Oracle ve virtuálním počítači Azure | Dokumenty společnosti Microsoft
description: Rychle zprovozněte databázi Oracle Database 12c ve vašem prostředí Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 901a6ed9075f7e368c0706ac6ba5fc2900bea9a8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262124"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Vytvoření databáze Oracle ve virtuálním počítači Azure

Tento průvodce podrobně popisuje použití azure cli k nasazení virtuálního počítače Azure z [image galerie Oracle Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) za účelem vytvoření databáze Oracle 12c. Jakmile je server nasazen, připojíte se přes SSH za účelem konfigurace databáze Oracle. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Chcete-li vytvořit virtuální počítač (VM), použijte příkaz [az vm create.](/cli/azure/vm) 

Následující příklad vytvoří virtuální počítač `myVM`. Také vytvoří klíče SSH, pokud již neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače azure CLI zobrazí informace podobné v následujícím příkladu. Poznamenejte `publicIpAddress`si hodnotu pro . Tuto adresu používáte pro přístup k virtuálnímu virtuálnímu serveru.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Chcete-li vytvořit relaci SSH s virtuálním virtuálním ms, použijte následující příkaz. Nahraďte IP `publicIpAddress` adresu hodnotou pro váš virtuální počítač.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Vytvoření databáze

Software Oracle je již nainstalován v bitové kopii Marketplace. Vytvořte ukázkovou databázi následujícím způsobem. 

1.  Přepněte na *naduživatele oracle* a potom inicializujte naslouchací proces pro protokolování:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Výstup je podobný tomuto:

    ```output
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

2.  Vytvořte databázi:

    ```bash
    dbca -silent \
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

    Vytvoření databáze trvá několik minut.

3. Nastavení proměnných Oracle

Před připojením je třeba nastavit dvě proměnné prostředí: *ORACLE_HOME* a *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

Do souboru .bashrc můžete také přidat ORACLE_HOME a ORACLE_SID proměnných. To by ukládalo proměnné prostředí pro budoucí přihlášení. `~/.bashrc`

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Konektivita Oracle EM Express

Nástroj pro správu grafického uživatelského rozhraní, který můžete použít k prozkoumání databáze, nastavte řešení Oracle EM Express. Chcete-li se připojit k řešení Oracle EM Express, musíte nejprve nastavit port v oracle. 

1. Připojte se k databázi pomocí sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Po připojení nastavte port 5502 pro EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otevřete kontejner PDB1, pokud již nebyl otevřen, ale nejprve zkontrolujte stav:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Výstup je podobný tomuto:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Pokud OPEN_MODE `PDB1` pro není čtení write, pak spusťte následující příkazy k otevření PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Musíte zadat `quit` ukončit relaci sqlplus `exit` a zadejte odhlášení od uživatele oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizace spuštění a vypnutí databáze

Databáze Oracle se ve výchozím nastavení nespustí automaticky při restartování virtuálního počítače. Chcete-li nastavit, aby se databáze Oracle spouštěla automaticky, přihlaste se nejprve jako root. Potom vytvořte a aktualizujte některé systémové soubory.

1. Přihlásit se jako root

    ```bash
    sudo su -
    ```

2.  Pomocí oblíbeného editoru `/etc/oratab` upravte soubor `N` `Y`a změňte výchozí hodnotu na :

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Vytvořte soubor `/etc/init.d/dbora` s názvem a vložte následující obsah:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

4.  Změňte oprávnění k souborům pomocí *chmodu* následujícím způsobem:

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

6.  Pokud chcete otestovat změny, restartujte virtuální počítač:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Otevřené porty pro připojení

Posledním úkolem je konfigurace některých externích koncových bodů. Chcete-li nastavit skupinu zabezpečení sítě Azure, která chrání virtuální počítač, nejprve ukončete relaci SSH ve virtuálním počítači (měl být vyhozen z SSH při restartování v předchozím kroku). 

1.  Chcete-li otevřít koncový bod, který používáte pro vzdálený přístup k databázi Oracle, vytvořte pravidlo skupiny zabezpečení sítě s [pravidlem az network nsg](/cli/azure/network/nsg/rule) takto: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Chcete-li otevřít koncový bod, který používáte pro vzdálený přístup k aplikaci Oracle EM Express, vytvořte pravidlo skupiny zabezpečení sítě s [pravidlem az network nsg](/cli/azure/network/nsg/rule) takto:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. V případě potřeby znovu získejte veřejnou IP adresu virtuálního počítače pomocí [veřejné ip show az network](/cli/azure/network/public-ip) takto:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Připojte EM Express z vašeho prohlížeče. Ujistěte se, že váš prohlížeč je kompatibilní s EM Express (Je vyžadována instalace Flash): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Můžete se přihlásit pomocí účtu **SYS** a zkontrolovat **políčko jako sysdba.** Použijte heslo **OraPasswd1,** které jste nastavili během instalace. 

![Snímek obrazovky přihlašovací stránky Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení zkoumání první databáze Oracle v Azure a virtuální počítač už není potřeba, můžete použít příkaz [odstranění skupiny az](/cli/azure/group) odebrat skupinu prostředků, virtuální počítač a všechny související prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Seznamte se s dalšími [řešeními Oracle v Azure](oracle-considerations.md). 

Vyzkoušejte kurz Instalace a konfigurace oracle [automated storage management.](configure-oracle-asm.md)
