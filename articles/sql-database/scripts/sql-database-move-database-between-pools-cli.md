---
title: Příklad rozhraní příkazového řádku – Přesun databáze SQL Azure – Elastický fond SQL | Microsoft Docs
description: Ukázkový skript Azure CLI pro přesun databáze SQL v elastickém fondu SQL
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: janeng
ms.openlocfilehash: 37b083a2da74ba2f097dd49eb877f606b9a9cfc8
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Přesun databáze SQL Azure v elastickém fondu SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI vytvoří dva elastické fondy, přesune databázi SQL Azure z jednoho elastického fondu SQL do druhého elastického fondu SQL a pak přesune databázi z elastického fondu na úroveň výkonu izolované databáze Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | Vytvoří logický server, který je hostitelem databáze nebo elastického fondu. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) | Vytvoří v rámci logického serveru elastický fond. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_create) | Vytvoří databázi na logickém serveru jako izolovanou databázi nebo databázi ve fondu. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | Aktualizuje vlastnosti databáze nebo přesune databázi do nebo z elastického fondu nebo mezi elastickými fondy. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).


