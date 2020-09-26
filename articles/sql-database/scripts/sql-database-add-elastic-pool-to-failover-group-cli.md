---
title: Příklad rozhraní příkazového řádku – skupina převzetí služeb při selhání Azure SQL Database elastický fond
description: Ukázkový skript Azure CLI, který vytvoří Azure SQL Database elastický fond, přidá ho do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 07/16/2019
ms.openlocfilehash: a5814bfe3bd6ec2d97a068ea8ce71fa7ffea8ec0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323579"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Přidání elastického fondu Azure SQL Database do skupiny převzetí služeb při selhání pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI vytvoří izolovanou databázi, přidá ji do elastického fondu, vytvoří skupinu převzetí služeb při selhání a otestuje převzetí služeb při selhání.

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

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [AZ SQL elastický fond](/cli/azure/sql/elastic-pool) | Příkazy elastického fondu. |
| [AZ SQL Failover-Group ](/cli/azure/sql/failover-group) | Příkazy skupiny převzetí služeb při selhání. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure/overview).

Další SQL Database ukázkových skriptech rozhraní příkazového řádku Azure CLI najdete v [Azure SQL Database skriptech](../../azure-sql/database/az-cli-script-samples-content-guide.md)rozhraní příkazového řádku Azure.
