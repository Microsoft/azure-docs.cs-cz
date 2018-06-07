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
ms.openlocfilehash: 7f4dced6f82622ba735b1b059f30d88830347fba
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625844"
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurace clusteru AKS prostory Dev Azure

Otevřete okno příkazového řádku a zadejte následující příkaz rozhraní příkazového řádku Azure, pomocí skupinu prostředků, která obsahuje váš cluster AKS a AKS název clusteru. Příkaz nakonfiguruje váš cluster s podporou Azure Dev prostory.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

