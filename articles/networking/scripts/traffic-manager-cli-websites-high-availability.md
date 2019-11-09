---
title: Ukázkový skript Azure CLI – směrování provozu pro vysokou dostupnost aplikací | Microsoft Docs
description: Ukázkový skript Azure CLI – směrování provozu pro vysokou dostupnost aplikací
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: fdd54039cd3e039439c70e02b962e56d72efa715
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890264"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Směrování provozu pro vysokou dostupnost aplikací

Tento skript vytvoří skupinu prostředků, dva plány služby App Service, dvě webové aplikace, profil služby Traffic Manager a dva koncové body Traffic Manageru. Traffic Manager směruje provoz do aplikace v jedné oblasti jako primární oblast a do sekundární oblasti, když aplikace v primární oblasti není k dispozici. Před spuštěním skriptu musíte změnit hodnoty MyWebApp, MyWebAppL1 a MyWebAppL2 na jedinečné hodnoty v rámci Azure. Po spuštění skriptu máte přístup k aplikaci v primární oblasti s adresou URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete k odebrání skupiny prostředků, App Service aplikace a všech souvisejících prostředků použít příkaz následného.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, profilu služby Traffic Manager a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Vytvoří plán služby App Service. Toto je jako serverová farma pro webovou aplikaci Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří webovou aplikaci Azure v rámci plánu App Service. |
| [AZ Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Vytvoří profil služby Azure Traffic Manager. |
| [AZ Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Přidá do profilu služby Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty App Service CLI najdete v [dokumentaci k síti Azure](../cli-samples.md).
