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
ms.openlocfilehash: 44ce986fcfdf079d3077c007a378f3467073d00d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823176"
---
### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurace clusteru AKS prostory Dev Azure

Otevřete okno příkazového řádku a zadejte následující příkaz rozhraní příkazového řádku Azure, pomocí skupinu prostředků, která obsahuje váš cluster AKS a AKS název clusteru. Příkaz nakonfiguruje váš cluster s podporou Azure Dev prostory.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

