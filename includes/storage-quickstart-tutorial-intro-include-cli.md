---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: e479f2376668a2fc3824e733996c94cfab04c9ec
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466578"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště pro obecné účely vytvoříte příkazem [az storage account create](/cli/azure/storage/account). Účet úložiště pro obecné účely můžete použít pro všechny čtyři služby: objekty blob, soubory, tabulky a fronty.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Zadání přihlašovacích údajů účtu úložiště

Azure CLI pro většinu příkazů v tomto kurzu potřebuje přihlašovací údaje účtu úložiště. Existuje několik možností, jak to udělat. Jeden z nejjednodušších způsobů je nastavit proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY`.

> [!NOTE]
> Tento článek popisuje, jak nastavit proměnné prostředí pomocí bash. Jiná prostředí můžou vyžadovat úpravy syntaxe.

Nejdřív Zobrazte klíče účtu úložiště pomocí příkazu [AZ Storage Account Keys list](/cli/azure/storage/account/keys) . Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Nyní nastavte proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_KEY`. To můžete provést v prostředí bash pomocí příkazu `export`. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Další informace o tom, jak načíst přístupové klíče účtu pomocí Azure Portal, najdete v tématu [Správa přístupových klíčů účtu úložiště](../articles/storage/common/storage-account-keys-manage.md).