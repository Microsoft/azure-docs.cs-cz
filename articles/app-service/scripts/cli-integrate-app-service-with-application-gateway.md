---
title: Ukázkový skript Azure CLI – integrace App Service s Application Gateway | Microsoft Docs
description: Ukázkový skript Azure CLI – integrace App Service s Application Gateway
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97006074"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrace App Service s Application Gateway pomocí rozhraní příkazového řádku

Tento ukázkový skript vytvoří Azure App Service webovou aplikaci, Virtual Network Azure a Application Gateway. Pak omezí provoz webové aplikace tak, aby vznikla pouze z Application Gateway podsíti.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.0.74 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, App Service aplikace, Cosmos DB a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | Vytvoří virtuální síť. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | Vytvoří veřejnou IP adresu. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | Zobrazí podrobnosti veřejné IP adresy. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Vytvoří App Service webovou aplikaci. |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | Zobrazí podrobnosti o App Service webové aplikaci. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | Přidá omezení přístupu do webové aplikace App Service. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | Vytvoří Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | Aktualizuje nastavení protokolu HTTP Application Gateway. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
