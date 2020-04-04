---
title: Konfigurace parametrů serveru – Azure CLI – databáze Azure pro MariaDB
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MariaDB pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 3ba06ea592d51eedbe827e1ab6418f65722d579c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632304"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pomocí rozhraní příkazového řádku Azure
Můžete seznam, zobrazit a aktualizovat parametry konfigurace pro azure databáze pro mariadb server pomocí Azure CLI, nástroj příkazového řádku Azure. Podmnožina konfigurací motoru je vystavena na úrovni serveru a může být změněna.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Databáze Azure pro server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Seznam parametrů konfigurace serveru pro Azure Database pro server MariaDB
Chcete-li vypsat všechny upravitelné parametry na serveru a jejich hodnoty, spusťte příkaz [seznamu konfiguračního seznamu serveru az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list)

Parametry konfigurace serveru pro server **mydemoserver.mariadb.database.azure.com** můžete uvést v části skupina prostředků **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Definici každého z uvedených parametrů naleznete v referenční části MariaDB v části [Systémové proměnné serveru](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti o parametru konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace serveru, spusťte příkaz [az mariadb server configuration show.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show)

Tento příklad ukazuje podrobnosti o parametru konfigurace serveru **protokolu pomalý\_\_dotaz** pro server **mydemoserver.mariadb.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru
Můžete také upravit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní hodnotu konfigurace pro serverový modul MariaDB. Chcete-li aktualizovat konfiguraci, použijte příkaz [konfigurační sady serveru az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 

Aktualizace parametru konfigurace **serveru protokolu protokolu\_pomalýdotaz\_** serveru serveru **mydemoserver.mariadb.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Pokud chcete obnovit hodnotu konfiguračního parametru, vyneche volitelný `--value` parametr a služba použije výchozí hodnotu. Pro výše uvedený příklad by to vypadalo takto:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Tento kód obnoví **\_pomalou\_** konfiguraci protokolu dotazu na výchozí hodnotu **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Vyplnění tabulek časových pásem

Tabulky časových pásem na serveru mohou `mysql.az_load_timezone` být naplněny voláním uložené procedury z nástroje, jako je příkazový řádek MariaDB nebo Pracovní plocha MariaDB.

> [!NOTE]
> Pokud používáte `mysql.az_load_timezone` příkaz z MariaDB Workbench, možná budete muset nejprve vypnout režim nouzové aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`aplikace .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Měli byste restartovat server, abyste zajistili, že tabulky časových pásem jsou správně naplněny. Chcete-li restartovat server, použijte [portál Azure](howto-restart-server-portal.md) nebo [cli](howto-restart-server-cli.md).

Chcete-li zobrazit dostupné hodnoty časových pásem, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit pomocí příkazu [konfigurační sady serveru az mariadb.](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set)

Následující příkaz aktualizuje parametr konfigurace serveru **časového\_pásma** serveru **mydemoserver.mariadb.database.azure.com** v rámci skupiny prostředků **myresourcegroup** na **US/Pacific**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace lze `SET time_zone` nastavit spuštěním příkazu z nástroje, jako je příkazový řádek MariaDB nebo pracovní plocha MariaDB. Následující příklad nastaví časové pásmo na **us/tichomořské** časové pásmo.  

```sql
SET time_zone = 'US/Pacific';
```

Funkce [data a času](https://mariadb.com/kb/en/library/date-time-functions/)naleznete v dokumentaci MariaDB .

## <a name="next-steps"></a>Další kroky

- Konfigurace [parametrů serveru na webu Azure Portal](howto-server-parameters.md)
