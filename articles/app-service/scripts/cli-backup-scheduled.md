---
title: 'CLI: vytvoření naplánovaného zálohování'
description: Naučte se používat rozhraní příkazového řádku Azure k automatizaci nasazení a správy aplikace App Service. V této ukázce se dozvíte, jak vytvořit naplánované zálohování pro aplikaci.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: db1d0558f93b203af1605663533847d32afbcffb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782544"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Vytvoření naplánovaného zálohování pro App Service aplikaci pomocí rozhraní příkazového řádku

Tento ukázkový skript vytvoří aplikaci v App Service se souvisejícími prostředky a pak pro ni vytvoří naplánované zálohování. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Vytvoří účet úložiště. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Vytvoří kontejner úložiště Azure. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Vygeneruje token SAS pro kontejner úložiště Azure.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Vytvoří plán služby App Service. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Vytvoří aplikaci App Service. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az_webapp_config_backup_update) | Nakonfiguruje nový plán zálohování pro aplikaci App Service. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az_webapp_config_backup_show) | Zobrazuje plán zálohování pro aplikaci App Service. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Načte seznam záloh pro aplikaci App Service. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu App Service najdete v [dokumentaci ke službě Azure App Service](../samples-cli.md).
