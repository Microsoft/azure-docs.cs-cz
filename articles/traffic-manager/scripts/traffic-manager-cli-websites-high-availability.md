---
title: Provoz tras pro HA aplikací – Azure CLI - Traffic Manager
description: Ukázka skriptu Azure CLI – směrování provozu pro vysokou dostupnost aplikací
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: e108fbe54ce67d4eb354319450259fc410462c53
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460609"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Směrovat provoz pro vysokou dostupnost aplikací pomocí azure CLI

Tento skript vytvoří skupinu prostředků, dva plány služby app service, dvě webové aplikace, profil správce provozu a dva koncové body správce provozu. Traffic Manager směruje provoz do aplikace v jedné oblasti jako primární oblast a do sekundární oblasti, když aplikace v primární oblasti není k dispozici. Před spuštěním skriptu je nutné změnit hodnoty MyWebApp, MyWebAppL1 a MyWebAppL2 na jedinečné hodnoty v rámci Azure. Po spuštění skriptu můžete přistupovat k aplikaci v primární oblasti pomocí adresy URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázky skriptu lze příkaz follow použít k odebrání skupiny prostředků, aplikace App Service a všech souvisejících prostředků.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, profilu služby Traffic Manager a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Vytvoří plán služby App Service. Je to jako serverová farma pro vaši webovou aplikaci Azure. |
| [az webapp web vytvořit](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Vytvoří webovou aplikaci Azure v rámci plánu služby App Service. |
| [az profil správce provozu sítě vytvořit](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Vytvoří profil služby Azure Traffic Manager. |
| [az vytvoření koncového bodu správce provozu sítě](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Přidá do profilu služby Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázky skriptu příkazového příkazu služby App Service najdete v [dokumentaci k síti Azure](../cli-samples.md).
