---
title: 'Azure CLI: monitorování a škálování izolované databáze v Azure SQL Database'
description: Pomocí ukázkového skriptu Azure CLI můžete monitorovat a škálovat izolovanou databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: bd982ab6dc66674e705f080511282bcfeb909872
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787162"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Pomocí Azure CLI můžete monitorovat a škálovat izolovanou databázi v Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento ukázkový skript Azure CLI po dotazování na informace o velikosti databáze škáluje izolovanou databázi v Azure SQL Database na jinou výpočetní velikost.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Pomocí funkce [AZ SQL DB op list](/cli/azure/sql/db/op?#az_sql_db_op_list) získáte seznam operací provedených v databázi a pomocí funkce [AZ SQL DB op Cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) zrušíte operaci aktualizace databáze.

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Skript | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Příkazy serveru. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Zobrazí informace o využití velikosti databáze. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku najdete v [ukázkových skriptech Azure CLI](../az-cli-script-samples-content-guide.md).
