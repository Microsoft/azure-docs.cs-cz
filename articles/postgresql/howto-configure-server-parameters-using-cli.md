---
title: Nastavit parametry služby ve službě Azure Database for PostgreSQL
description: Tento článek popisuje, jak nastavit parametry služby ve službě Azure Database for PostgreSQL pomocí příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fb9123d792c3b522debb3b1c8d539cbd6cd24f59
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843630"
---
# <a name="customize-server-configuration-parameters-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pomocí Azure CLI
Seznam, zobrazit a aktualizovat parametry konfigurace pro server Azure PostgreSQL pomocí rozhraní příkazového řádku (Azure CLI). Podmnožina stroj konfigurace je zveřejněný na úrovni serveru a je možné upravit. 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- Vytvoření serveru Azure Database for PostgreSQL a databáze pomocí následujících [vytvoření Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) rozhraní příkazového řádku na počítači nebo použití [Azure Cloud Shell](../cloud-shell/overview.md) na webu Azure Portal pomocí prohlížeče.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Seznam parametrů konfigurace serveru pro službu Azure Database pro PostgreSQL server
Seznam všech parametrů upravitelná v serveru a jejich hodnoty, spusťte [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) příkazu.

Můžete vytvořit seznam parametrů konfigurace serveru pro server **mydemoserver.postgres.database.azure.com** ve skupině prostředků **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Detaily parametr konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétní konfiguraci parametru pro server, spusťte [az postgres server configuration show](/cli/azure/postgres/server/configuration) příkazu.

Tento příklad ukazuje podrobnosti o **protokolu\_min\_zprávy** parametr konfigurace serveru pro server **mydemoserver.postgres.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Změnit hodnotu parametru konfigurace serveru
Můžete také změnit hodnotu parametru určité server konfigurace, která aktualizuje hodnotu základní konfigurace pro server stroje PostgreSQL. Chcete-li aktualizovat konfiguraci, použijte [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) příkazu. 

Aktualizovat **protokolu\_min\_zprávy** server konfigurační parametr serveru **mydemoserver.postgres.database.azure.com** ve skupině prostředků  **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Pokud chcete resetovat hodnotu parametru konfigurace, zvolíte jednoduše vynechte nepovinný `--value` parametr a službou použije výchozí hodnotu. Ve výše například to vypadat nějak takto:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Tento příkaz obnoví **protokolu\_min\_zprávy** konfigurace na výchozí hodnotu **upozornění**. Další informace o konfiguraci serveru a přípustné hodnoty, najdete v dokumentaci PostgreSQL na [konfigurace serveru](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Další postup
- Konfigurace a získat přístup k protokolům serveru najdete v tématu [protokolů serveru ve službě Azure Database for PostgreSQL](concepts-server-logs.md)
