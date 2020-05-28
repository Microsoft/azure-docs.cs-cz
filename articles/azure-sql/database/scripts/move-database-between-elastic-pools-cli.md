---
title: 'Azure CLI: Přesun databáze mezi elastickými fondy'
description: Ukázkový skript Azure CLI, který vytvoří dva elastické fondy a přesouvá databázi v SQL Database z jednoho elastického fondu na jiný pomocí Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 2bcf9f4218f535556ff83ccaad130bb1da28ef69
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054057"
---
# <a name="use-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Přesun databáze v SQL Database do elastického fondu SQL pomocí rozhraní příkazového řádku


Tento ukázkový skript Azure CLI vytvoří dva elastické fondy a přesune databázi v SQL Database z jednoho elastického fondu SQL do jiného elastického fondu SQL a pak přesune databázi z elastického fondu na výpočetní velikost databáze.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

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
| [AZ SQL elastické fondy](/cli/azure/sql/elastic-pool) | Příkazy elastického fondu. |
| [AZ SQL DB](/cli/azure/sql/db) | Příkazy databáze. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../az-cli-script-samples-content-guide.md).
