---
title: Kurz – obnovení virtuálního počítače pomocí Azure CLI
description: Zjistěte, jak obnovit disk a vytvořit obnovený virtuální počítač v Azure pomocí služeb Backup a Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 999682c9bf4a4d70d886f0e85cede99f215aa046
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97694722"
---
# <a name="restore-a-vm-with-azure-cli"></a>Obnovení virtuálního počítače s využitím Azure CLI

Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jednotlivé soubory. Tento článek vysvětluje postup obnovení celého virtuálního počítače pomocí rozhraní příkazového řádku. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> * Výpis a výběr bodů obnovení
> * Obnovení disku z bodu obnovení
> * Vytvoření virtuálního počítače z obnoveného disku

Informace o obnovení disku a vytvoření chráněného počítače pomocí PowerShellu najdete v tématu [Zálohování a obnovení virtuálních počítačů Azure pomocí PowerShellu](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.18 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

 - Tento kurz vyžaduje virtuální počítač s Linuxem chráněný službou Azure Backup. Pro simulaci náhodného odstranění virtuálního počítače a procesu obnovení vytvoříte virtuální počítač z disku v bodu obnovení. Pokud potřebujete virtuální počítač s Linuxem chráněný pomocí služby Azure Backup, přečtěte si téma [Zálohování virtuálního počítače v Azure pomocí rozhraní příkazového řádku](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Přehled služby Backup

Když Azure zahájí zálohování, rozšíření zálohování na virtuálním počítači pořídí snímek v daném okamžiku. Rozšíření zálohování se na virtuální počítač nainstaluje při vyžádání prvního zálohování. Azure Backup může také pořídit snímek základního úložiště, pokud virtuální počítač neběží, když dojde k zálohování.

Ve výchozím nastavení provede Azure Backup zálohování konzistentní vzhledem k systému souborů. Jakmile Azure Backup pořídí snímek, data se přenesou do trezoru služby Recovery Services. Pro maximalizaci efektivity Azure Backup identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

## <a name="list-available-recovery-points"></a>Výpis dostupných bodů obnovení

Když chcete obnovit disk, vyberete bod obnovení jako zdroj dat obnovení. Vzhledem k tomu, že výchozí zásady vytváří body obnovení každý den a uchovávají je po dobu 30 dnů, můžete udržovat sadu bodů obnovení, které vám umožní pro obnovení vybrat konkrétní bod v čase.

Seznam dostupných bodů obnovení zobrazíte pomocí příkazu [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). **Název** bodu obnovení slouží k obnovení disků. V tomto kurzu chceme nejnovější dostupný bod obnovení. Parametr `--query [0].name` následujícím způsobem vybere název nejnovějšího bodu obnovení:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Obnovení disku virtuálního počítače

> [!IMPORTANT]
> Pro získání všech výhod rychlého obnovení, včetně obnovení spravovaného disku, se velmi důrazně doporučuje použít AZ CLI verze 2.0.74 nebo novější. Je to nejlepší, pokud vždycky používáte nejnovější verzi.

### <a name="managed-disk-restore"></a>Obnovení spravovaného disku

Pokud má zálohovaný virtuální počítač spravované disky a záměr obnovovat spravované disky z bodu obnovení, musíte nejdřív zadat účet úložiště Azure. Tento účet úložiště se používá k uložení konfigurace virtuálního počítače a šablony nasazení, které se dají později použít k nasazení virtuálního počítače z obnovených disků. Pak zadáte také cílovou skupinu prostředků, do které se mají načíst spravované disky.

1. Účet úložiště vytvoříte pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create). Název účtu úložiště musí být malými písmeny a globálně jedinečný. Nahraďte *mystorageaccount* vlastním jedinečným názvem:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Obnovte disk z bodu obnovení pomocí příkazu [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Nahraďte *mystorageaccount* názvem účtu úložiště, který jste vytvořili v předchozím příkazu. Nahraďte *myrecoverypointname názvem* názvem bodu obnovení, který jste získali ve výstupu z předchozího příkazu [AZ Backup RecoveryPoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) . ***Zadejte taky cílovou skupinu prostředků, do které se budou spravované disky obnovují***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Pokud není zadaná **cílová skupina prostředků** , pak se spravované disky obnoví jako nespravované disky do daného účtu úložiště. To bude mít významné důsledky k době obnovení, protože doba potřebná k obnovení disků zcela závisí na daném účtu úložiště. Výhody okamžitého obnovení získáte jenom v případě, že je zadaný parametr Target-Resource-Group. Pokud je záměrem obnovit spravované disky jako nespravované, Neposkytněte parametr **target-Resource-Group** a místo toho zadejte parametr **Restore-as-unmanaged-disk** , jak je uvedeno níže. Tento parametr je k dispozici z AZ 3.4.0 a dál.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Tato akce obnoví spravované disky jako nespravované disky do daného účtu úložiště a nebude využívat funkci okamžitého obnovení. V budoucích verzích rozhraní příkazového řádku bude povinná zadat parametr **target-Resource-Group** nebo **Restore-as-unmanaged-disk** .

### <a name="unmanaged-disks-restore"></a>Obnovení nespravovaných disků

Pokud má zálohovaný virtuální počítač nespravované disky a záměr obnovovat disky z bodu obnovení, nejprve zadáte účet úložiště Azure. Tento účet úložiště se používá k uložení konfigurace virtuálního počítače a šablony nasazení, které se dají později použít k nasazení virtuálního počítače z obnovených disků. Ve výchozím nastavení se nespravované disky obnoví do původních účtů úložiště. Pokud chcete obnovit všechny nespravované disky na jediném místě, můžete daný účet úložiště použít také jako pracovní umístění pro tyto disky.

V dalších krocích se obnovený disk použije k vytvoření virtuálního počítače.

1. Účet úložiště vytvoříte pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create). Název účtu úložiště musí být malými písmeny a globálně jedinečný. Nahraďte *mystorageaccount* vlastním jedinečným názvem:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Obnovte disk z bodu obnovení pomocí příkazu [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Nahraďte *mystorageaccount* názvem účtu úložiště, který jste vytvořili v předchozím příkazu. Nahraďte *myRecoveryPointName* názvem bodu obnovení, který jste získali z výstupu předchozího příkazu [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Jak bylo uvedeno výše, nespravované disky budou obnoveny do původního účtu úložiště. To poskytuje nejlepší výkon při obnovení. Pokud ale potřebujete obnovit všechny nespravované disky do daného účtu úložiště, použijte odpovídající příznak, jak je uvedeno níže.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Výstup bude vypadat podobně jako v následujícím příkladu, který ukazuje, že je úloha obnovení ve stavu *InProgress* (Probíhající):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Po dokončení *stavu* sestav úlohy obnovení budou v účtu úložiště obnoveny potřebné informace (konfigurace virtuálních počítačů a šablona nasazení).

## <a name="create-a-vm-from-the-restored-disk"></a>Vytvoření virtuálního počítače z obnoveného disku

Posledním krokem je vytvoření virtuálního počítače z obnovených disků. K vytvoření virtuálního počítače můžete použít šablonu nasazení staženou do daného účtu úložiště.

### <a name="fetch-the-job-details"></a>Načtení podrobností úlohy

Výsledné Podrobnosti úlohy poskytují identifikátor URI šablony, který lze dotazovat a nasadit. Další podrobnosti o aktivované obnovené úloze získáte pomocí příkazu úlohy show.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Výstup tohoto dotazu poskytne všechny podrobnosti, ale zajímá Vás jenom s obsahem účtu úložiště. K načtení relevantních podrobností můžeme použít [Možnosti dotazů](/cli/azure/query-azure-cli) rozhraní příkazového řádku Azure CLI.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Načtení šablony nasazení

Šablona není přímo přístupná, protože je pod účtem úložiště zákazníka a zadaným kontejnerem. Pro přístup k této šabloně potřebujeme úplnou adresu URL (spolu s dočasným tokenem SAS).

Nejdřív z podrobností úlohy rozbalte identifikátor URI objektu BLOB šablony.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Identifikátor URI objektu BLOB šablony bude v tomto formátu a extrahuje název šablony.

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Proto název šablony z výše uvedeného příkladu bude ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` a název kontejneru bude ```myVM-daa1931199fd4a22ae601f46d8812276```

Nyní Získejte token SAS pro tento kontejner a šablonu, jak je popsáno [zde](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment) .

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Nasazením šablony vytvoříte virtuální počítač.

Nyní nasaďte šablonu k vytvoření virtuálního počítače, jak je vysvětleno [zde](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Pokud chcete ověřit vytvoření virtuálního počítače z obnoveného disku, následujícím způsobem vypište virtuální počítače ve vaší skupině prostředků pomocí příkazu [az vm list](/cli/azure/vm#az-vm-list):

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste obnovili disk z bodu obnovení a pak jste z tohoto disku vytvořili virtuální počítač. Naučili jste se:

> [!div class="checklist"]
>
> * Výpis a výběr bodů obnovení
> * Obnovení disku z bodu obnovení
> * Vytvoření virtuálního počítače z obnoveného disku

V dalším kurzu najdete informace o obnovování jednotlivých souborů z bodu obnovení.

> [!div class="nextstepaction"]
> [Obnovení souborů do virtuálního počítače v Azure](tutorial-restore-files.md)
