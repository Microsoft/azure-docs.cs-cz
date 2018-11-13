---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 23b92f7d1b1c3ef488e182a6443f275365a03d9a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571929"
---
## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu, použijte příkaz slouží k odebrání skupiny prostředků, instance Azure Redis Cache a všechny související prostředky ve skupině prostředků.

```azurecli
az group delete --name contosoGroup
```