---
title: Ukázkový skript Azure CLI – Připojení webové aplikace k databázi SQL | Microsoft Docs
description: Ukázkový skript Azure CLI – Připojení webové aplikace k databázi SQL
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 7c2efdd0-f553-4038-a77a-e953021b3f77
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 8356785582657811dbf745637c7216cfa9b3f445
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connect-a-web-app-to-a-sql-database"></a>Připojení webové aplikace k databázi SQL

Tento ukázkový skript vytvoří databázi SQL Azure a webovou aplikaci Azure. Pak pomocí nastavení aplikace propojí databázi SQL s webovou aplikací.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-sql/connect-to-sql.sh?highlight=9-10 "SQL Database")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření skupiny prostředků, webové aplikace, služby SQL Database a všech souvisejících prostředků používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) | Vytvoří webovou aplikaci Azure. |
| [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create) | Vytvoří server služby SQL Database.  |
| [`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create) | Vytvoří na serveru služby SQL Database novou databázi. |
| [`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_show_connection_string) | Vygeneruje připojovací řetězec k databázi. |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) | Vytvoří nebo aktualizuje nastavení aplikace pro webovou aplikaci Azure. Nastavení aplikace se pro vaši aplikaci zveřejní jako proměnné prostředí. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../app-service-cli-samples.md).
