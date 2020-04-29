---
title: Příklad rozhraní příkazového řádku – monitorování a škálování – jedna databáze SQL Azure
description: Ukázkový skript Azure CLI pro monitorování a škálování izolované databáze Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 191de1fdbbee3e31bfcd366cbec8a70732b23b5c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061823"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Monitorování a škálování izolované databáze SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI škáluje izolovanou databázi Azure SQL na jinou velikost výpočetních prostředků poté, co se odeslal dotaz na informace o velikosti databáze.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

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

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
