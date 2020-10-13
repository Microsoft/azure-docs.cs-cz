---
title: Konfigurace parametrů serveru – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: dfb1d59335f2e35ce3d4661f043b6eed53d2a109
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627155"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Konfigurace parametrů serveru v Azure Database for MySQL pomocí rozhraní příkazového řádku Azure
Pomocí Azure CLI, nástroje příkazového řádku Azure můžete vypsat, zobrazit a aktualizovat parametry konfigurace Azure Database for MySQL serveru. Podmnožina konfigurací modulu se zveřejňuje na úrovni serveru a dá se upravit. 

>[!Note]
> Parametry serveru se dají aktualizovat globálně na úrovni serveru, pomocí rozhraní příkazového [řádku Azure CLI](./howto-configure-server-parameters-using-cli.md), [powershellu](./howto-configure-server-parameters-using-powershell.md)nebo [Azure Portal](./howto-server-parameters.md) .

## <a name="prerequisites"></a>Požadavky
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

Tento příklad ukazuje podrobnosti parametru konfigurace serveru **pomalého \_ dotazu \_ ** pro server **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Změna hodnoty parametru konfigurace serveru
Můžete také změnit hodnotu určitého parametru konfigurace serveru, který aktualizuje základní konfigurační hodnotu pro modul serveru MySQL. Pokud chcete aktualizovat konfiguraci, použijte příkaz [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Pokud chcete aktualizovat parametr konfigurace serveru s **pomalým \_ dotazem \_ ** na serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Pokud chcete resetovat hodnotu parametru konfigurace, vynechejte volitelný `--value` parametr a služba použije výchozí hodnotu. V příkladu výše by vypadalo takto:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Tento kód resetuje konfiguraci ** \_ \_ protokolu pomalých dotazů** na výchozí hodnotu **vypnuto**. 

## <a name="setting-parameters-not-listed"></a>Nastavení neuvedených parametrů
Pokud parametr serveru, který chcete aktualizovat, není uveden v Azure Portal, můžete volitelně nastavit parametr na úrovni připojení pomocí `init_connect` . Tím se nastaví parametry serveru pro každého klienta, který se připojuje k serveru. 

Aktualizujte parametr konfigurace serveru **init \_ connect** serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup** pro nastavení hodnot, jako je například znaková sada.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časových pásem

Tabulky časových pásem na vašem serveru se dají naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud spouštíte `mysql.az_load_timezone` příkaz z aplikace MySQL Workbench, možná bude nutné nejprve vypnout režim bezpečné aktualizace pomocí nástroje `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny. K restartování serveru použijte [Azure Portal](howto-restart-server-portal.md) nebo [CLI](howto-restart-server-cli.md).

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit pomocí příkazu [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Následující příkaz aktualizuje parametr konfigurace **serveru \_ časového pásma** serveru **mydemoserver.MySQL.Database.Azure.com** ve skupině prostředků **myresourcegroup** na **US/Tichomoří**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním `SET time_zone` příkazu z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .  

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.


## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure Portal](howto-server-parameters.md)
