---
title: Vytvoření databáze Oracle na Virtuálním počítači Azure | Dokumentace Microsoftu
description: Rychle zprovoznit databázi Oracle Database 12c nahoru v prostředí Azure.
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
ms.openlocfilehash: 516552111cc21cacf87fd8179ef49b939e2820d9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46985074"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Vytvoření databáze Oracle na Virtuálním počítači Azure

Tato příručka podrobně popisuje použití Azure CLI pro nasazení virtuálního počítače z Azure [image z Galerie marketplace Oracle](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) Chcete-li vytvořit databázi Oracle 12 c. Po nasazení serveru se připojí přes protokol SSH aby bylo možné konfigurovat databáze Oracle. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Chcete-li vytvořit virtuální počítač (VM), použijte [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazu. 

Následující příklad vytvoří virtuální počítač `myVM`. Také vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíčů. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Po vytvoření virtuálního počítače se zobrazí Azure CLI informace podobně jako v následujícím příkladu. Poznamenejte si hodnotu pro `publicIpAddress`. Tuto adresu použijete pro přístup k virtuálnímu počítači.

```azurecli
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

Chcete-li vytvořit relaci SSH s virtuálním Počítačem, použijte následující příkaz. Nahraďte IP adresu s `publicIpAddress` hodnoty pro váš virtuální počítač.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Vytvoření databáze

Oracle software je již nainstalována na Marketplace image. Vytvoření ukázkové databáze následujícím způsobem. 

1.  Přepněte *oracle* superuživatele a inicializovat naslouchací proces pro protokolování:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    Výstup je podobný tomuto:

    ```bash
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

2.  Vytvoření databáze:

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

    Trvá několik minut pro vytvoření databáze.

3. Nastavení proměnných Oracle

Než připojíte, je nutné nastavit dvě proměnné prostředí: *ORACLE_HOME* a *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Také můžete přidat proměnné ORACLE_HOME a ORACLE_SID .bashrc souboru. To by uložit proměnné prostředí pro budoucí přihlášení. Potvrďte následující příkazy jsou přidané do `~/.bashrc` soubor pomocí editoru podle vašeho výběru.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Připojení Oracle EM Express

Pro nástroj pro správu grafického uživatelského rozhraní, který vám pomůže prozkoumat databázi nastavte Oracle EM Express. Pro připojení k Oracle EM Express, musíte nejdřív nastavit port v databázi Oracle. 

1. Připojení k databázi pomocí sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Jakmile budete připojeni, nastavte port 5502 pro EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Otevřete kontejneru PDB1 Nepřihlášený otevřené, ale první kontrola stavu:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    Výstup je podobný tomuto:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Pokud OPEN_MODE pro `PDB1` není čtení ZAPSAT, spusťte příkazy tady otevřete PDB1:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Je potřeba zadat `quit` sqlplus relace a typ `exit` odhlášení uživatele oracle.

## <a name="automate-database-startup-and-shutdown"></a>Automatizace databáze spuštění a vypnutí

Oracle database ve výchozím nastavení nespustí automaticky při restartování virtuálního počítače. K nastavení databáze Oracle na automatické spouštění, nejdřív přihlásit jako uživatel root. Vytvořte a aktualizovat některé soubory systému.

1. Přihlásit jako uživatel root
    ```bash
    sudo su -
    ```

2.  Pomocí oblíbeného editoru, upravte soubor `/etc/oratab` a změnit výchozí `N` k `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Vytvořte soubor s názvem `/etc/init.d/dbora` a vložte následující obsah:

    ```
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

4.  Změna oprávnění u souborů s *chmod* následujícím způsobem:

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

Poslední úkol je konfigurace některé externí koncové body. Nastavení skupiny zabezpečení sítě Azure, které chrání virtuální počítač, nejprve ukončete relaci SSH ve virtuálním počítači (by měl mít byla spuštěna z SSH při restartování v předchozím kroku). 

1.  Otevřete koncový bod, který používáte pro přístup k databázi Oracle vzdáleně, vytvořte pravidlo skupiny zabezpečení sítě pomocí [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) následujícím způsobem: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Otevřete koncový bod, který používáte pro přístup k Oracle EM Express vzdáleně, vytvořte pravidlo skupiny zabezpečení sítě pomocí [az network nsg pravidlo vytvořte](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) následujícím způsobem:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. V případě potřeby získat veřejnou IP adresu vašeho virtuálního počítače s [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) následujícím způsobem:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Připojte EM Express z prohlížeče. Ujistěte se, že váš prohlížeč je kompatibilní s EM Express (je vyžadována instalace Flash): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Můžete se přihlásit pomocí **SYS** účtu a podívejte se **jako sysdba** zaškrtávací políčko. Použijte heslo **OraPasswd1** , kterou jste nastavili během instalace. 

![Snímek obrazovky s přihlašovací stránku Express Oracle výrobce OEM](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení zkoumání svoji první databázi Oracle v Azure a virtuální počítač je už je nepotřebujete, můžete použít [odstranění skupiny az](/cli/azure/group#az_group_delete) příkazu k odebrání skupiny prostředků, virtuálního počítače, a všechny související prostředky.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

Další informace o dalších [řešení Oracle v Azure](oracle-considerations.md). 

Zkuste [instalace a konfigurace Oracle automatizované Storage Management](configure-oracle-asm.md) kurzu.