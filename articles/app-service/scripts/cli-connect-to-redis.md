---
title: 'CLI: připojení aplikace k Redis'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak připojit aplikaci k mezipaměti Azure pro Redis.
author: msangapu-msft
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: e4d9fc6e597fb51f607a3e2833ae6ddd7d9281fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97006331"
---
# <a name="connect-an-app-service-app-to-an-azure-cache-for-redis-using-cli"></a>Připojení aplikace App Service k mezipaměti Azure pro Redis pomocí rozhraní příkazového řádku

Tento ukázkový skript vytvoří mezipaměť Azure pro Redis a aplikaci App Service. Pak pomocí nastavení aplikace propojí mezipaměť Azure pro Redis s aplikací.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, App Service aplikace, mezipaměti Azure pro Redis a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Vytvoří aplikaci App Service. |
| [`az redis create`](/cli/azure/redis#az-redis-create) | Vytvořte novou mezipaměť Azure pro instanci Redis. |
| [`az redis list-keys`](/cli/azure/redis#az-redis-list-keys) | Zobrazí přístupové klíče pro instanci Azure cache pro Redis. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Vytvoří nebo aktualizuje nastavení aplikace pro App Service aplikaci. Nastavení aplikace se pro vaši aplikaci zveřejní jako proměnné prostředí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
