---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246806"
---
## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například aplikace Function App, databáze a účty úložiště.

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.  
Pokud nepoužíváte službu Cloud Shell, nejprve se přihlaste pomocí `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko. Pokud chcete zobrazit všechna podporovaná umístění pro plány služby App Service, spusťte příkaz [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations).
