---
title: 'CLI: monitorování aplikace pomocí protokolů webového serveru'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak monitorovat aplikaci pomocí protokolů webového serveru.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 995ca98a21e5ec392182bb48932765cca35acf3a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562453"
---
# <a name="monitor-an-app-service-app-with-web-server-logs-using-azure-cli"></a>Monitorování aplikace App Service s využitím protokolů webového serveru pomocí Azure CLI

Tento ukázkový skript vytvoří skupinu prostředků, App Service plán a aplikaci, a nakonfiguruje aplikaci tak, aby povolovala protokoly webového serveru. Pak stáhne soubory protokolů ke kontrole.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Tento kurz vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, App Service aplikace a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Vytvoří aplikaci App Service. |
| [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) | Nakonfiguruje, které protokoly bude aplikace App Service dál trvat. |
| [`az webapp log download`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-download) | Stáhne protokoly App Service aplikace do místního počítače. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).