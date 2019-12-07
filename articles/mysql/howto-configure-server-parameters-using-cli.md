---
title: Konfigurace parametrů serveru – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 0250810d25b0abb5bf675d8c91f3c0678d895c37
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893157"
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

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časové pásmo

Časové pásmo tabulky na vašem serveru je možné naplnit s voláním `az_load_timezone` uloženou proceduru z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte `az_load_timezone` příkaz z aplikace MySQL Workbench, budete muset nejprve vypnout režim bezpečného aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny. K restartování serveru použijte [Azure Portal](howto-restart-server-portal.md) nebo [CLI](howto-restart-server-cli.md).

Chcete-li zobrazit hodnoty dostupné časové pásmo, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení na globální úrovni časové pásmo

Časové pásmo globální úrovně lze nastavit pomocí příkazu [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Následující příkaz aktualizuje parametr konfigurace serveru **\_zóny** serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup** na **USA/Tichomoří**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovni relace

Relace spuštěním můžete nastavit úroveň časového pásma `SET time_zone` z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo **US / Tichomoří** časové pásmo.  

```sql
SET time_zone = 'US/Pacific';
```

Naleznete v dokumentaci MySQL pro [funkce data a času](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure Portal](howto-server-parameters.md)