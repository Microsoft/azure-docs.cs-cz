---
title: Konfigurace parametrů-Azure Database for PostgreSQL-Single server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL-Single server pomocí Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4231f348f99073406fcb6a5bef9bf0f84cacf2eb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005567"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pro Azure Database for PostgreSQL pro jeden server pomocí Azure CLI
Můžete vypsat, zobrazit a aktualizovat parametry konfigurace pro server Azure PostgreSQL pomocí rozhraní příkazového řádku (Azure CLI). Podmnožina konfigurací modulu se zveřejňuje na úrovni serveru a dá se upravit. 

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- Vytvoření serveru a databáze Azure Database for PostgreSQL pomocí následujících kroků vytvořte [Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Nainstalujte na svém počítači rozhraní příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte [Azure Cloud Shell](../cloud-shell/overview.md) v Azure Portal pomocí prohlížeče.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Vypsat parametry konfigurace serveru pro Azure Database for PostgreSQL Server
Pokud chcete zobrazit seznam všech parametrů, které lze upravovat na serveru a jejich hodnotách, spusťte příkaz [AZ Postgres Server Configuration list](/cli/azure/postgres/server/configuration) .

Můžete uvést parametry konfigurace serveru pro server **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti parametru konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace pro server, spusťte příkaz [AZ Postgres Server Configuration show](/cli/azure/postgres/server/configuration)  .

Tento příklad ukazuje podrobnosti parametru konfigurace **serveru \_ zprávy o minimálních \_ zprávách** pro server **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Změnit hodnotu parametru konfigurace serveru
Můžete také změnit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní konfigurační hodnotu pro modul PostgreSQL serveru. Chcete-li aktualizovat konfiguraci, použijte příkaz [AZ Postgres Server Configuration set](/cli/azure/postgres/server/configuration) . 

Chcete-li aktualizovat parametr konfigurace serveru pro **\_ minimální \_ zprávy protokolu** serveru **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Pokud chcete resetovat hodnotu konfiguračního parametru, stačí vybrat volitelný `--value` parametr a služba použije výchozí hodnotu. Ve výše uvedeném příkladu by vypadalo takto:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Tento příkaz obnoví konfiguraci **\_ minimálních \_ zpráv protokolu** na **Upozornění** na výchozí hodnotu. Další informace o konfiguraci serveru a přípustných hodnotách najdete v dokumentaci k PostgreSQL v tématu věnovaném [konfiguraci serveru](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Další kroky
- [Informace o tom, jak restartovat server](howto-restart-server-cli.md)
- Konfigurace a přístup k protokolům serveru najdete v tématu [protokoly serveru v nástroji Azure Database for PostgreSQL](concepts-server-logs.md)
