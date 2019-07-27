---
title: Příklad rozhraní příkazového řádku – přidat jednu databázi do skupiny převzetí služeb při selhání – Azure SQL Database
description: Ukázkový skript Azure CLI, který vytvoří Azure SQL Database izolovanou databázi, přidá ji do skupiny převzetí služeb při selhání a otestuje převzetí služeb při selhání.
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
ms.openlocfilehash: b80be0db41ea8bd3996a7b8843c80f510289545f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570060"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Přesun databáze Azure SQL v elastickém fondu SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript PowerShellu vytvoří izolovanou databázi, vytvoří skupinu převzetí služeb při selhání, přidá do ní databázi a otestuje převzetí služeb při selhání. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [AZ Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Nastaví předplatné jako aktuální aktivní předplatné. | 
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Vytvoří server SQL Database hostující jednotlivé databáze a elastické fondy. |
| [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Vytvoří pravidla brány firewall serveru. | 
| [AZ SQL Failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Vytvoří skupinu převzetí služeb při selhání. | 
| [AZ SQL Failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Vypíše skupiny převzetí služeb při selhání na serveru. |
| [AZ SQL Failover-Group set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Nastavte primární skupinu převzetí služeb při selhání pomocí převzetí služeb při selhání všemi databázemi z aktuálního primárního serveru. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
