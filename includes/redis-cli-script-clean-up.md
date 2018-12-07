---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: b0b188e10fe0893734185ced186e3ce24dfd0d21
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019377"
---
## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu, použijte příkaz slouží k odebrání skupiny prostředků, Azure Cache Azure pro instanci Redis a všechny související prostředky ve skupině prostředků.

```azurecli
az group delete --name contosoGroup
```