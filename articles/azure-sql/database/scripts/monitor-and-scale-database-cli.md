---
title: 'Azure CLI: sledování & škálování databáze v Azure SQL Database'
description: Ukázkový skript Azure CLI pro monitorování a škálování izolované databáze v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: be57309e4b327027ed0185c8eabf783a18cc957e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053721"
---
# <a name="use-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Pomocí rozhraní příkazového řádku můžete monitorovat a škálovat izolovanou databázi v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento ukázkový skript Azure CLI po dotazování na informace o velikosti databáze škáluje izolovanou databázi v Azure SQL Database na jinou výpočetní velikost.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a single database in Azure SQL Database")]

> [!TIP]
> Pomocí [AZ SQL DB op list](/cli/azure/sql/db/op?#az-sql-db-op-list) získáte seznam operací provedených v databázi a [AZ SQL DB op Cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) pro zrušení operace aktualizace databáze.

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Příkazy serveru. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Zobrazuje informace o využití velikosti pro jednu nebo sdruženou databázi. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku najdete v [ukázkových skriptech Azure CLI](../az-cli-script-samples-content-guide.md).
