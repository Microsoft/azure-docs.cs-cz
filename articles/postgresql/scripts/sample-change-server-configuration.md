---
title: "Skript Azure CLI – Změna konfigurace serveru"
description: "Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné možnosti konfigurace serveru a aktualizuje hodnotu jedné z nich."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 9aff0dca1d09e31f0c6f761dd236a7ef7439486b
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Výpis a aktualizace konfigurace serveru Azure Database for PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné parametry konfigurace a jejich povolené hodnoty pro server Azure Database for PostgreSQL a nastaví parametr *log_retention_days* na jinou než výchozí hodnotu.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | Vypíše konfiguraci serveru Azure Database for PostgreSQL. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | Aktualizuje konfiguraci serveru Azure Database for PostgreSQL. |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_show) | Zobrazí konfiguraci serveru Azure Database for PostgreSQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o Azure CLI najdete tady: [Dokumentace k Azure CLI](/cli/azure/overview).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete v tématu [Konfigurace parametrů serveru na webu Azure Portal](../howto-configure-server-parameters-using-portal.md).
