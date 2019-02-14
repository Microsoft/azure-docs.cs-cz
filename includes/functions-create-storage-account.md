---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 889b9c0cf944085f5f42ece892d5cac747a27240
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246785"
---
## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Aplikace Functions používá účet pro obecné účely ve službě Azure Storage k zachování stavu a dalších informací o vašich funkcích. Ve skupině prostředků, kterou jste vytvořili, vytvořte účet úložiště pro obecné účely pomocí příkazu [az storage account create](/cli/azure/storage/account).

V následujícím příkazu nahraďte zástupný text `<storage_name>` globálně jedinečným názvem účtu úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

Po vytvoření účtu úložiště se v rozhraní Azure CLI zobrazí podobné informace jako v následujícím příkladu:

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```
