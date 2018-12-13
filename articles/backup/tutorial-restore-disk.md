---
title: Obnovení disku virtuálního počítače pomocí služby Azure Backup
description: Zjistěte, jak obnovit disk a vytvořit obnovený virtuální počítač v Azure pomocí služeb Backup a Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 4/17/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 76b5a5743fd6ac715eca45e49cc08d5006522ad0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871537"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Obnovení disku a vytvoření obnoveného virtuálního počítače v Azure
Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jednotlivé soubory. Tento článek vysvětluje postup obnovení celého virtuálního počítače pomocí rozhraní příkazového řádku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Výpis a výběr bodů obnovení
> * Obnovení disku z bodu obnovení
> * Vytvoření virtuálního počítače z obnoveného disku

Informace o obnovení disku a vytvoření chráněného počítače pomocí PowerShellu najdete v tématu [Zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.18 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Požadavky
Tento kurz vyžaduje virtuální počítač s Linuxem chráněný službou Azure Backup. Pro simulaci náhodného odstranění virtuálního počítače a procesu obnovení vytvoříte virtuální počítač z disku v bodu obnovení. Pokud potřebujete virtuální počítač s Linuxem chráněný pomocí služby Azure Backup, přečtěte si téma [Zálohování virtuálního počítače v Azure pomocí rozhraní příkazového řádku](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Přehled služby Backup
Když Azure zahájí zálohování, rozšíření zálohování na virtuálním počítači pořídí snímek v daném okamžiku. Rozšíření zálohování se na virtuální počítač nainstaluje při vyžádání prvního zálohování. Azure Backup může pořídit také snímek základního úložiště, pokud virtuální počítač není při zálohování spuštěný.

Ve výchozím nastavení provede Azure Backup zálohování konzistentní vzhledem k systému souborů. Jakmile Azure Backup pořídí snímek, data se přenesou do trezoru služby Recovery Services. Pro maximalizaci efektivity Azure Backup identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.


## <a name="list-available-recovery-points"></a>Výpis dostupných bodů obnovení
Když chcete obnovit disk, vyberete bod obnovení jako zdroj dat obnovení. Vzhledem k tomu, že výchozí zásady vytváří body obnovení každý den a uchovávají je po dobu 30 dnů, můžete udržovat sadu bodů obnovení, které vám umožní pro obnovení vybrat konkrétní bod v čase. 

Seznam dostupných bodů obnovení zobrazíte pomocí příkazu [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). **Název** bodu obnovení slouží k obnovení disků. V tomto kurzu chceme nejnovější dostupný bod obnovení. Parametr `--query [0].name` následujícím způsobem vybere název nejnovějšího bodu obnovení:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Obnovení disku virtuálního počítače
Když chcete obnovit disk z bodu obnovení, nejprve vytvoříte účet úložiště Azure. Tento účet úložiště se použije k uložení obnoveného disku. V dalších krocích se obnovený disk použije k vytvoření virtuálního počítače.

1. Účet úložiště vytvoříte pomocí příkazu [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Název účtu úložiště musí být malými písmeny a globálně jedinečný. Nahraďte *mystorageaccount* vlastním jedinečným názvem:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Obnovte disk z bodu obnovení pomocí příkazu [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Nahraďte *mystorageaccount* názvem účtu úložiště, který jste vytvořili v předchozím příkazu. Nahraďte *myRecoveryPointName* názvem bodu obnovení, který jste získali z výstupu předchozího příkazu [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Monitorování úlohy obnovení
Pokud chcete monitorovat stav úlohy obnovení, použijte příkaz [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že je úloha obnovení ve stavu *InProgress* (Probíhající):

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Až se *Status* (Stav) úlohy obnovení změní na *Completed* (Dokončeno), disk bude obnovený v účtu úložiště.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Převod obnoveného disku na spravovaný disk
Úloha obnovení vytvoří nespravovaný disk. Pokud chcete z disku vytvořit virtuální počítač, musí se nejprve převést na spravovaný disk.

1. Získejte informace o připojení k vašemu účtu úložiště pomocí příkazu [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string). Následujícím způsobem nahraďte *mystorageaccount* názvem vašeho účtu úložiště:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Váš nespravovaný disk je zabezpečený v účtu úložiště. Následující příkazy získají informace o vašem nespravovaném disku a vytvoří proměnnou *uri*, kterou použijete v dalším kroku při vytváření spravovaného disku.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Teď ze svého obnoveného disku můžete vytvořit spravovaný disk pomocí příkazu [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create). Proměnná *uri* z předchozího kroku slouží jako zdroj pro váš spravovaný disk.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Když teď ze svého obnoveného disku máte spravovaný disk, vyčistěte nespravovaný disk a účet úložiště pomocí příkazu [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Následujícím způsobem nahraďte *mystorageaccount* názvem vašeho účtu úložiště:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Vytvoření virtuálního počítače z obnoveného disku
Posledním krokem je vytvoření virtuálního počítače ze spravovaného disku.

1. Následujícím způsobem vytvořte ze svého spravovaného disku virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Pokud chcete ověřit vytvoření virtuálního počítače z obnoveného disku, následujícím způsobem vypište virtuální počítače ve vaší skupině prostředků pomocí příkazu [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list):

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Další postup
V tomto kurzu jste obnovili disk z bodu obnovení a pak jste z tohoto disku vytvořili virtuální počítač. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Výpis a výběr bodů obnovení
> * Obnovení disku z bodu obnovení
> * Vytvoření virtuálního počítače z obnoveného disku

V dalším kurzu najdete informace o obnovování jednotlivých souborů z bodu obnovení.

> [!div class="nextstepaction"]
> [Obnovení souborů do virtuálního počítače v Azure](tutorial-restore-files.md)

