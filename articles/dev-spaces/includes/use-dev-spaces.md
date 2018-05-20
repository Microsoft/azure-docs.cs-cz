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
ms.openlocfilehash: 2563f7c36283521541562bcd88f973d86a6f672a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
## <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Konfigurace clusteru AKS prostory Dev Azure

Otevřete okno příkazového řádku a zadejte následující příkazy rozhraní příkazového řádku Azure, pomocí skupinu prostředků, která obsahuje váš cluster AKS a AKS název clusteru:

   ```cmd
   az extension add --name dev-spaces-preview 
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```
První příkaz nainstaluje rozšíření do příkazového řádku Azure CLI pro přidání podpory pro Azure Dev prostory a druhý nakonfiguruje váš cluster s podporou Azure Dev prostory.
