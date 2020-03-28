---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: e5166934c4f7e1ecc917d2a5afcbb26ea9a637a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67174811"
---
## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázky skriptu lze příkaz follow použít k odebrání skupiny prostředků, instance Azure Cache for Redis a všech souvisejících prostředků ve skupině prostředků.

```azurecli
az group delete --name contosoGroup
```