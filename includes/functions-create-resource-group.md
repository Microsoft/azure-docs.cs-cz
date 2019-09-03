---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "68444115"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například aplikace Function App, databáze a účty úložiště.

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.  
Pokud nepoužíváte službu Cloud Shell, nejprve se přihlaste pomocí `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Obecně vytvoříte skupinu prostředků a prostředky v [oblasti](https://azure.microsoft.com/global-infrastructure/regions/) , která je blízko vás. 
