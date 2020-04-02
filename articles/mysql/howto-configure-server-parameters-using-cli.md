---
title: Konfigurace parametrů serveru – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: ca5f80e57f90e4dd26ac2e4a175998ff3de2c102
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546423"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Přizpůsobení parametrů serveru pomocí rozhraní příkazového řádku Azure
Můžete seznam, zobrazit a aktualizovat parametry konfigurace pro Azure Database pro server MySQL pomocí Azure CLI, nástroj příkazového řádku Azure. Podmnožina konfigurací motoru je vystavena na úrovni serveru a může být změněna. 

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Seznam parametrů konfigurace serveru pro Azure Database pro server MySQL
Chcete-li vypsat všechny upravitelné parametry na serveru a jejich hodnoty, spusťte příkaz [az mysql server configuration list.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list)

Parametry konfigurace serveru pro server **mydemoserver.mysql.database.azure.com** v části skupina prostředků **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definici každého z uvedených parametrů naleznete v části Reference MySQL v části [Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti o parametru konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace serveru, spusťte příkaz [az mysql server configuration show.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show)

Tento příklad ukazuje podrobnosti o parametru konfigurace serveru **protokolu pomalý\_dotaz\_** pro server **mydemoserver.mysql.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru
Můžete také upravit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní hodnotu konfigurace pro serverový modul MySQL. Chcete-li aktualizovat konfiguraci, použijte příkaz [az mysql server configuration set.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) 

Aktualizace parametru konfigurace **serveru protokolu protokolu\_pomalýdotaz\_** serveru serveru **mydemoserver.mysql.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Pokud chcete obnovit hodnotu konfiguračního parametru, vyneche volitelný `--value` parametr a služba použije výchozí hodnotu. Pro výše uvedený příklad by to vypadalo takto:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Tento kód obnoví **\_pomalou\_** konfiguraci protokolu dotazu na výchozí hodnotu **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Vyplnění tabulek časových pásem

Tabulky časových pásem na serveru mohou `mysql.az_load_timezone` být naplněny voláním uložené procedury z nástroje, jako je příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte `mysql.az_load_timezone` příkaz z MySQL Workbench, možná budete muset nejprve vypnout režim nouzové aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`aplikace .

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

Časové pásmo globální úrovně lze nastavit pomocí příkazu [az mysql server configuration set.](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set)

Následující příkaz aktualizuje parametr konfigurace serveru **časového\_pásma** serveru **mydemoserver.mysql.database.azure.com** v rámci skupiny prostředků **myresourcegroup** na **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace lze `SET time_zone` nastavit spuštěním příkazu z nástroje, jako je příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na **us/tichomořské** časové pásmo.  

```sql
SET time_zone = 'US/Pacific';
```

Funkce [data a času](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)naleznete v dokumentaci mySQL .


## <a name="next-steps"></a>Další kroky

- Konfigurace [parametrů serveru na webu Azure Portal](howto-server-parameters.md)