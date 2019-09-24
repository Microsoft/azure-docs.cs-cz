---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203182"
---
## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Aplikace Functions používá účet pro obecné účely ve službě Azure Storage k zachování stavu a dalších informací o vašich funkcích. Ve skupině prostředků, kterou jste vytvořili, vytvořte účet úložiště pro obecné účely pomocí příkazu [az storage account create](/cli/azure/storage/account#az-storage-account-create).

V následujícím příkazu nahraďte zástupný text `<storage_name>` globálně jedinečným názvem účtu úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
