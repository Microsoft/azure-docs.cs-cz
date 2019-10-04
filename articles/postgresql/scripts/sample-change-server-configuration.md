---
title: Skript Azure CLI – Změna konfigurace serveru (PostgreSQL)
description: Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné možnosti konfigurace serveru a aktualizuje hodnotu jedné z možností.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: d2b54d1173b9591de2482f4b3368d3dde8b8c766
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947799"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Seznam a aktualizace konfigurací Azure Database for PostgreSQL serveru pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné parametry konfigurace, jakož i jejich přípustné hodnoty pro Azure Database for PostgreSQL Server a nastaví *log_retention_days* na jinou hodnotu než výchozí.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete spustit rozhraní příkazového řádku místně, vyžaduje tento článek Azure CLI verze 2,0 nebo novější. Ověřte verzi spuštěním `az --version`. Pokud chcete nainstalovat nebo upgradovat verzi Azure CLI, přečtěte si téma [instalace Azure CLI]( /cli/azure/install-azure-cli) . 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na vlastní.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Pomocí následujícího příkazu odeberte skupinu prostředků a všechny prostředky, které jsou k ní přidružené, po spuštění skriptu. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy, které jsou uvedené v následující tabulce:

| **Systému** | **Poznámky** |
|---|---|
| [AZ Group Create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které jsou uložené všechny prostředky. |
| [AZ Postgres Server Create](/cli/azure/postgres/server) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [AZ Postgres Server Configuration list](/cli/azure/postgres/server/configuration) | Vypíše konfiguraci serveru Azure Database for PostgreSQL. |
| [AZ Postgres Server Configuration set](/cli/azure/postgres/server/configuration) | Aktualizujte konfiguraci serveru Azure Database for PostgreSQL. |
| [AZ Postgres Server Configuration show](/cli/azure/postgres/server/configuration) | Zobrazit konfiguraci serveru Azure Database for PostgreSQL. |
| [AZ Group DELETE](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte další skripty: [ukázky v Azure CLI pro Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete v tématu [Postup konfigurace parametrů serveru v Azure Portal](../howto-configure-server-parameters-using-portal.md).
