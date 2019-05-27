---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132762"
---
## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu, použijte příkaz slouží k odebrání skupiny prostředků, mezipaměti Azure pro instanci Redis a všechny související prostředky ve skupině prostředků.

```azurecli
az group delete --name contosoGroup
```