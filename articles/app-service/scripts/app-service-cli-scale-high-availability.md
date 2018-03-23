---
title: Ukázkový skript Azure CLI – Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností | Microsoft Docs
description: Ukázkový skript Azure CLI – Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 5f9e0d17e4bf3c239e316d9e46199b59caf59295
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností

Tento ukázkový skript vytvoří skupinu prostředků, dva plány služby App Service, dvě webové aplikace, profil služby Traffic Manager a dva koncové body služby Traffic Manager. Na konci cvičení budete mít vysoce dostupnou architekturu zajišťující globální dostupnost vaší webové aplikace na základě nejnižší latence sítě.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, profilu služby Traffic Manager a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Vytvoří webovou aplikaci Azure. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az_network_traffic_manager_profile_create) | Vytvoří profil služby Azure Traffic Manager. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az_network_traffic_manager_endpoint_create) | Přidá do profilu služby Azure Traffic Manager koncový bod. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../app-service-cli-samples.md).
