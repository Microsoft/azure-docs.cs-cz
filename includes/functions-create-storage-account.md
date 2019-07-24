---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444130"
---
## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Aplikace Functions používá účet pro obecné účely ve službě Azure Storage k zachování stavu a dalších informací o vašich funkcích. Ve skupině prostředků, kterou jste vytvořili, vytvořte účet úložiště pro obecné účely pomocí příkazu [az storage account create](/cli/azure/storage/account).

V následujícím příkazu nahraďte zástupný text `<storage_name>` globálně jedinečným názvem účtu úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
