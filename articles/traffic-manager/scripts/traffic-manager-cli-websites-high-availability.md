---
title: Směrování provozu pro HA aplikací – Azure CLI – Traffic Manager
description: Ukázkový skript Azure CLI – směrování provozu pro vysokou dostupnost aplikací
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: abac2e5b1fcba65db92edd2bcc2f390ec0114f59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185338"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Směrování provozu pro vysokou dostupnost aplikací pomocí Azure CLI

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
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az appservice plan create](/cli/azure/appservice/plan) | Vytvoří plán služby App Service. Toto je jako serverová farma pro webovou aplikaci Azure. |
| [AZ WebApp web Create](/cli/azure/webapp#az-webapp-create) | Vytvoří webovou aplikaci Azure v rámci plánu App Service. |
| [AZ Network Traffic-Manager Profile Create](/cli/azure/network/traffic-manager/profile) | Vytvoří profil služby Azure Traffic Manager. |
| [AZ Network Traffic-Manager Endpoint Create](/cli/azure/network/traffic-manager/endpoint) | Přidá do profilu služby Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty App Service CLI najdete v [dokumentaci k síti Azure](../cli-samples.md).