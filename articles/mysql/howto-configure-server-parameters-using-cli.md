---
title: Konfigurace parametrů serveru – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 2a53debb72cfd5da73c2bceb7993288eb828237a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770522"
---
# <a name="customize-server-parameters-by-using-azure-cli"></a>Přizpůsobení parametrů serveru pomocí Azure CLI
Pomocí Azure CLI, nástroje příkazového řádku Azure můžete vypsat, zobrazit a aktualizovat parametry konfigurace Azure Database for MySQL serveru. Podmnožina konfigurací modulu se zveřejňuje na úrovni serveru a dá se upravit. 

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Vypsat parametry konfigurace serveru pro Azure Database for MySQL server
Pokud chcete zobrazit seznam všech parametrů, které lze upravovat na serveru a jejich hodnotách, spusťte příkaz [AZ MySQL Server Configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) .

Můžete uvést parametry konfigurace serveru pro server **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definice všech uvedených parametrů naleznete v části referenční dokumentace MySQL na [systémových proměnných serveru](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Zobrazit podrobnosti parametru konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru konfigurace pro server, spusťte příkaz [AZ MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) .

V tomto příkladu se zobrazí podrobnosti o **pomalém\_** parametr konfigurace serveru protokolu\_pro server **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru
Můžete také změnit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní konfigurační hodnotu pro modul serveru MySQL. Pokud chcete aktualizovat konfiguraci, použijte příkaz [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Chcete-li aktualizovat parametr **pomalé\_dotaz\_serveru protokolu** serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Pokud chcete resetovat hodnotu parametru konfigurace, vynechejte parametr Optional `--value` a služba použije výchozí hodnotu. V příkladu výše by vypadalo takto:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Tento kód obnoví **pomalé\_dotaz\_konfiguraci protokolu** na výchozí hodnotu **vypnuto**. 

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časových pásem

Tabulky časových pásem na vašem serveru se dají naplnit voláním `az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte příkaz `az_load_timezone` z MySQL Workbench, možná budete muset nejdřív vypnout režim bezpečné aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit pomocí příkazu [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Následující příkaz aktualizuje parametr konfigurace serveru **\_zóny** serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup** na **USA/Tichomoří**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním příkazu `SET time_zone` z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .  

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.


## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure Portal](howto-server-parameters.md)