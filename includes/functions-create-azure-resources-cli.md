---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500408"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Vytvoření podpory prostředků Azure pro vaši funkci

Než budete moct nasadit kód funkce do Azure, musíte vytvořit tři prostředky:

- [Skupina prostředků](../articles/azure-resource-manager/management/overview.md), což je logický kontejner pro související prostředky.
- [Účet úložiště](../articles/storage/common/storage-account-create.md), který se používá k údržbě stavu a dalších informací o vašich funkcích.
- Aplikace Function App, která poskytuje prostředí pro spuštění kódu vaší funkce. Aplikace Function App se mapuje na váš místní projekt funkce a umožňuje seskupit funkce jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

Tyto položky vytvoříte pomocí následujících příkazů. Podporují se rozhraní příkazového řádku Azure a PowerShellu.

1. Pokud jste to ještě neudělali, přihlaste se k Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Příkaz [AZ Login](/cli/azure/reference-index#az_login) vás přihlásí k účtu Azure.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Rutina [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) vás přihlásí k účtu Azure.

    ---

1. Vytvořte skupinu prostředků s názvem `AzureFunctionsQuickstart-rg` v `westeurope` oblasti:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Pomocí příkazu [AZ Group Create](/cli/azure/group#az_group_create) se vytvoří skupina prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené z `az account list-locations` příkazu.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvoří skupinu prostředků. Obecně vytvoříte skupinu prostředků a prostředky v oblasti blízko vás pomocí dostupné oblasti vrácené rutinou [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

1. Vytvořte účet úložiště pro obecné účely ve vaší skupině prostředků a oblasti:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Příkaz [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) vytvoří účet úložiště. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Rutina [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) vytvoří účet úložiště.

    ---

    V předchozím příkladu nahraďte `<STORAGE_NAME>` názvem, který je vhodný pro vás a jedinečný v Azure Storage. Názvy musí obsahovat tři až 24 znaků a jenom malá písmena. `Standard_LRS` Určuje účet pro obecné účely, který je [podporován funkcemi](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
