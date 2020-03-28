---
title: Příklad příkazového příkazu k selhání – skupina převzetí služeb při selhání – elastický fond databáze Azure SQL
description: Ukázkový skript Azure CLI k vytvoření elastického fondu Azure SQL Database, jeho přidání do skupiny převzetí služeb při selhání a testování převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 2d6f18e373327b758e766dffba341c080622301f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061940"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Přidání elastického fondu Azure SQL Database do skupiny s podporou převzetí služeb při selhání pomocí příkazového příkazu Azure SQL Database

Tento příklad skriptu Azure CLI vytvoří jednu databázi, přidá ji do elastického fondu, vytvoří skupinu převzetí služeb při selhání a testuje převzetí služeb při selhání.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [az sql elastický fond](/cli/azure/sql/elastic-pool) | Příkazy elastického fondu. |
| [az sql skupina převzetí služeb při selhání](/cli/azure/sql/failover-group) | Příkazy skupiny převzetí služeb při selhání. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure/overview).

Další ukázky skriptu SQL Database Azure CLI najdete ve [skriptech Nastavení příkazu Azure azure azure](../sql-database-cli-samples.md).
