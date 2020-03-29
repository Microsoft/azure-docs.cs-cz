---
title: Konfigurace parametrů – Databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL – jeden server pomocí rozhraní příkazového řádku Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 4e029428a3709bacdbcd50a6ac3714e730377242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763619"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pro Azure Database for PostgreSQL – jeden server pomocí rozhraní příkazového řádku Azure
Můžete seznam, zobrazit a aktualizovat parametry konfigurace pro server Azure PostgreSQL pomocí rozhraní příkazového řádku (Azure CLI). Podmnožina konfigurací motoru je vystavena na úrovni serveru a může být změněna. 

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- Vytvoření databáze Azure pro postgreSQL server a databázi podle [vytvoření databáze Azure pro PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Nainstalujte rozhraní příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) do počítače nebo použijte [Azure Cloud Shell](../cloud-shell/overview.md) na webu Azure Portal pomocí prohlížeče.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Seznam parametrů konfigurace serveru pro Azure Database pro postgreSQL server
Chcete-li vypsat všechny upravitelné parametry na serveru a jejich hodnoty, spusťte příkaz [az postgres server configuration list.](/cli/azure/postgres/server/configuration)

Parametry konfigurace serveru pro server **mydemoserver.postgres.database.azure.com** v části skupina prostředků **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti o parametru konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace serveru, spusťte příkaz [az postgres server configuration show.](/cli/azure/postgres/server/configuration)

Tento příklad ukazuje podrobnosti o parametru konfigurace serveru **min\_min\_zpráv** pro server **mydemoserver.postgres.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Změnit hodnotu parametru konfigurace serveru
Můžete také upravit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní hodnotu konfigurace pro modul serveru PostgreSQL. Chcete-li aktualizovat konfiguraci, použijte příkaz [az postgres server configuration set.](/cli/azure/postgres/server/configuration) 

Aktualizace parametru konfigurace serveru **min\_min\_zpráv** serveru serveru **mydemoserver.postgres.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Pokud chcete obnovit hodnotu parametru konfigurace, jednoduše se rozhodnete `--value` vynechat volitelný parametr a služba použije výchozí hodnotu. Ve výše uvedeném příkladu by to vypadalo takto:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Tento příkaz obnoví konfiguraci **zpráv\_min\_protokolu** na výchozí hodnotu **UPOZORNĚNÍ**. Další informace o konfiguraci serveru a přípustné hodnoty naleznete v dokumentaci PostgreSQL na [konfigurace serveru](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Další kroky
- [Informace o restartování serveru](howto-restart-server-cli.md)
- Konfigurace a přístup k protokolům serveru najdete [v tématu Protokoly serveru v Azure Database for PostgreSQL](concepts-server-logs.md)
