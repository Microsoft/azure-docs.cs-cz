---
title: Ukázkový skript Azure CLI – směrování provozu pro zajištění vysoké dostupnosti aplikací | Dokumentace Microsoftu
description: Ukázkový skript Azure CLI – směrování provozu pro zajištění vysoké dostupnosti aplikace
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 284e0817023c13cee441d50dc226d5c2aa5f54c2
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891513"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Směrování provozu pro zajištění vysoké dostupnosti aplikací pomocí rozhraní příkazového řádku Azure

Tento skript vytvoří skupinu prostředků, dva plány služby app service, dvě webové aplikace, profil služby traffic manager a dva koncové body traffic Manageru. Traffic Manager směruje provoz do aplikace v jedné oblasti jako primární oblasti a do sekundární oblasti, když je k dispozici aplikace v primární oblasti. Před spuštěním skriptu, musíte změnit hodnoty MyWebApp, MyWebAppL1 a MyWebAppL2 na jedinečné hodnoty v Azure. Po spuštění skriptu, můžete přístup k aplikaci v primární oblasti s mywebapp.trafficmanager.net adresy URL.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu, použijte příkaz lze použít k odebrání skupiny prostředků, aplikace služby App Service a všechny související prostředky.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, profilu služby Traffic Manager a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Vytvoří plán služby App Service. Podobá se trochu serverové farmy pro vaši webovou aplikaci Azure. |
| [Vytvoření webové az webapp](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Vytvoří webovou aplikaci Azure v rámci plánu služby App Service. |
| [Vytvoření profilu traffic Manageru az sítě](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Vytvoří profil služby Azure Traffic Manager. |
| [Vytvoření koncového bodu az network traffic Manageru](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Přidá do profilu Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku App Service najdete v [sítí Azure dokumentaci](../cli-samples.md).
