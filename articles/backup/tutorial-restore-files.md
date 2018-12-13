---
title: Obnovení souborů do virtuálního počítače pomocí služby Azure Backup
description: Zjistěte, jak na virtuálním počítači Azure provádět obnovení na úrovni souborů pomocí služeb Backup a Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a8f9fd5a81f8e95d9787d9800be9962484e8436a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871843"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Obnovení souborů do virtuálního počítače v Azure
Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jednotlivé soubory. Tento článek podrobně popisuje, jak obnovit jednotlivé soubory. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Výpis a výběr bodů obnovení
> * Připojení bodu obnovení k virtuálnímu počítači
> * Obnovení souborů z bodu obnovení

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.18 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Požadavky
Tento kurz vyžaduje virtuální počítač s Linuxem chráněný službou Azure Backup. K simulaci náhodného odstranění souboru a procesu obnovení odstraníte stránku z webového serveru. Pokud potřebujete virtuální počítač s Linuxem a webovým serverem, který je chráněný pomocí služby Azure Backup, přečtěte si téma [Zálohování virtuálního počítače v Azure pomocí rozhraní příkazového řádku](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Přehled služby Backup
Když Azure zahájí zálohování, rozšíření zálohování na virtuálním počítači pořídí snímek v daném okamžiku. Rozšíření zálohování se na virtuální počítač nainstaluje při vyžádání prvního zálohování. Azure Backup může pořídit také snímek základního úložiště, pokud virtuální počítač není při zálohování spuštěný.

Ve výchozím nastavení provede Azure Backup zálohování konzistentní vzhledem k systému souborů. Jakmile Azure Backup pořídí snímek, data se přenesou do trezoru služby Recovery Services. Pro maximalizaci efektivity Azure Backup identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.


## <a name="delete-a-file-from-a-vm"></a>Odstranění souboru z virtuálního počítače
Pokud omylem odstraníte nebo změníte soubor, můžete jednotlivé soubory obnovit z bodu obnovení. Tento proces umožňuje procházet zálohované soubory v bodu obnovení a obnovit pouze soubory, které potřebujete. V tomto příkladu odstraníme soubor z webového serveru, abychom předvedli proces obnovení na úrovni souborů.

1. Abyste se mohli připojit ke svému virtuálnímu počítači, získejte jeho IP adresu pomocí příkazu [az vm show](/cli/azure/vm?view=azure-cli-latest#az-vm-show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Pokud chcete potvrdit, že váš web aktuálně funguje, otevřete webový prohlížeč a přejděte na veřejnou IP adresu vašeho virtuálního počítače. Toto okno webového prohlížeče nechte otevřené.

    ![Výchozí webová stránka serveru NGINX](./media/tutorial-restore-files/nginx-working.png)

3. Připojte se ke svému virtuálnímu počítači přes SSH. Nahraďte *publicIpAddress* veřejnou IP adresou, kterou jste získali předchozím příkazem:

    ```bash
    ssh publicIpAddress
    ```

4. Následujícím způsobem odstraňte z webového serveru výchozí stránku */var/www/html/index.nginx-debian.html*:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Ve webovém prohlížeči aktualizujte webovou stránku. Web už stránku nenačte, jak je znázorněno v následujícím příkladu:

    ![Web serveru NGINX už nenačte výchozí stránku](./media/tutorial-restore-files/nginx-broken.png)

6. Následujícím způsobem ukončete relaci SSH k vašemu virtuálnímu počítači:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Generování skriptu pro obnovení souborů
Pro obnovení souborů poskytuje Azure Backup skript, který můžete spustit na svém virtuálním počítači a který připojí bod obnovení jako místní jednotku. Tuto místní jednotku můžete procházet, obnovit soubory do virtuálního počítače a pak bod obnovení odpojit. Azure Backup pokračuje v zálohování vašich dat na základě přiřazené zásady určující plán a uchovávání.

1. Pokud chcete vypsat body obnovení pro váš virtuální počítač, použijte příkaz [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). V tomto příkladu vybíráme nejnovější bod obnovení pro virtuální počítač *myVM*, který je chráněný v trezoru *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. K získání skriptu, který připojí bod obnovení k virtuálnímu počítači, použijte příkaz [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp). Následující příklad získá skript pro virtuální počítač *myVM*, který je chráněný v trezoru *myRecoveryServicesVault*.

    Nahraďte *myRecoveryPointName* názvem bodu obnovení, který jste získali předchozím příkazem:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Skript se stáhne a zobrazí se heslo jako v následujícím příkladu:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. K přenosu skriptu do svého virtuálního počítače použijte nástroj Secure Copy (SCP). Zadejte název staženého skriptu a nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače. Nezapomeňte na konec příkazu SCP zadat i koncový znak `:`, jak je znázorněno níže:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Obnovení souboru do virtuálního počítače
Po zkopírování skriptu pro obnovení do svého virtuálního počítače teď můžete připojit bod obnovení a obnovit soubory.

1. Připojte se ke svému virtuálnímu počítači přes SSH. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače:

    ```bash
    ssh publicIpAddress
    ```

2. Pro zajištění správného spuštění skriptu přidejte pomocí příkazu **chmod** oprávnění ke spouštění. Zadejte název vašeho vlastního skriptu:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Bod obnovení připojíte spuštěním skriptu. Zadejte název vašeho vlastního skriptu:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Po spuštění skriptu se zobrazí výzva k zadání hesla pro přístup k bodu obnovení. Zadejte heslo, které se zobrazilo ve výstupu z předchozího příkazu [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp), kterým se vygeneroval skript pro obnovení.

    Výstup skriptu obsahuje cestu k bodu obnovení. Následující příklad výstupu ukazuje, že je bod obnovení připojený v umístění */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Pomocí příkazu **cp** zkopírujte výchozí webovou stránku serveru NGINX z připojeného bodu obnovení zpět do původního umístění souboru. Přípojný bod */home/azureuser/myVM-20170919213536/Volume1* nahraďte vlastním umístěním:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. Ve webovém prohlížeči aktualizujte webovou stránku. Web se teď opět načte správně, jak je znázorněno v následujícím příkladu:

    ![Web serveru NGINX se teď načte správně](./media/tutorial-restore-files/nginx-restored.png)

7. Následujícím způsobem ukončete relaci SSH k vašemu virtuálnímu počítači:

    ```bash
    exit
    ```

8. Odpojte bod obnovení ze svého virtuálního počítače pomocí příkazu [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-unmount-rp). Následující příklad odpojí bod obnovení z virtuálního počítače *myVM* v trezoru *myRecoveryServicesVault*.

    Nahraďte *myRecoveryPointName* názvem vašeho bodu obnovení, který jste získali v předchozích příkazech:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Další postup
V tomto kurzu jste připojili bod obnovení k virtuálnímu počítači a obnovili jste soubory pro webový server. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Výpis a výběr bodů obnovení
> * Připojení bodu obnovení k virtuálnímu počítači
> * Obnovení souborů z bodu obnovení

Přejděte k dalšímu kurzu, kde se dozvíte, jak zálohovat Windows Server do Azure.

> [!div class="nextstepaction"]
> [Zálohování Windows Serveru do Azure](tutorial-backup-windows-server-to-azure.md)

