---
title: Skript Azure CLI – škálování a monitorování Azure Database for PostgreSQL
description: Ukázkový skript Azure CLI – Škálování serveru Azure Database for PostgreSQL na jinou úroveň výkonu po dotazování metrik.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: b0a17b12c51c08cd39d0c4fb7860021b8c68e606
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606398"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorování a škálování jednoho serveru PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku škáluje výpočetní výkon a úložiště pro jeden Azure Database for PostgreSQL Server po dotazování metrik. Výpočetní výkon se může škálovat nahoru nebo dolů. Velikost úložiště se dá škálovat jenom nahoru. 

> [!IMPORTANT] 
> Úložiště se dá škálovat jenom nahoru, ne dolů.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript
Aktualizujte skript pomocí ID vašeho předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [AZ Postgres Server Update](/cli/azure/postgres/server#az-postgres-server-update) | Aktualizuje vlastnosti serveru PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o [Azure Database for PostgreSQL COMPUTE a úložišti](../concepts-pricing-tiers.md)
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- Další informace o [Azure CLI](/cli/azure)
