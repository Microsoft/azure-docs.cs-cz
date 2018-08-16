---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: b8b8b1a593328197c61f8edba9f8f849518dd2e5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "40129092"
---
## <a name="clean-up"></a>Vyčištění
Pokud chcete z clusteru úplně odstranit instanci Azure Dev Spaces včetně všech vývojových prostorů a služeb v nich spuštěných, použijte příkaz `az aks remove-dev-spaces`. Mějte na paměti, že tato akce je nevratná. Do clusteru můžete znovu přidat podporu Azure Dev Spaces, ale bude to, jako byste začínali znovu. Vaše staré služby a prostory se neobnoví.

Následující příklad vypíše kontrolery Azure Dev Spaces v aktivním předplatném a pak odstraní kontroler Azure Dev Spaces přidružený ke clusteru AKS myaks ve skupině prostředků myaks-rg.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```

