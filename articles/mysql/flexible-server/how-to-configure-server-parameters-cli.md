---
title: Konfigurace parametrů serveru – Azure CLI – Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat parametry služby v Azure Database for MySQL flexibilním serveru pomocí nástroje příkazového řádku Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 21f9b3dcb94be105c8b7279e6ac447540da65447
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110096"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Konfigurace parametrů serveru v Azure Database for MySQL flexibilním serveru pomocí Azure CLI

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Pomocí Azure CLI, nástroje příkazového řádku Azure můžete vypsat, zobrazit a aktualizovat parametry Azure Database for MySQL flexibilního serveru. Při vytváření serveru jsou parametry serveru nastaveny s výchozí a doporučenou hodnotou.  

Tento článek popisuje, jak pomocí Azure CLI vypsat, zobrazit a aktualizovat parametry serveru.

>[!Note]
> Parametry serveru se dají aktualizovat globálně na úrovni serveru, pomocí rozhraní příkazového [řádku Azure](./how-to-configure-server-parameters-cli.md) nebo [Azure Portal](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Azure Database for MySQL flexibilní Server](quickstart-create-server-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte Azure Cloud Shell v prohlížeči.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Výpis parametrů serveru pro Azure Database for MySQL flexibilní Server
Chcete-li zobrazit seznam všech parametrů na serveru a jejich hodnotách, spusťte příkaz [AZ MySQL Flexible-Server Parameter list](/cli/azure/mysql/flexible-server/parameter) .

Můžete zobrazit seznam parametrů serveru pro server **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
Definice všech uvedených parametrů naleznete v části referenční dokumentace MySQL na [systémových proměnných serveru](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Zobrazit podrobnosti parametru serveru
Chcete-li zobrazit podrobnosti o konkrétním parametru serveru, spusťte příkaz [AZ MySQL flexibilní-Server Parameter show](/cli/azure/mysql/flexible-server/parameter) .

Tento příklad ukazuje podrobnosti parametru serveru s **pomalým \_ dotazem \_** pro server **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Úprava hodnoty parametru serveru
Můžete také změnit hodnotu určitého parametru serveru, která aktualizuje základní konfigurační hodnotu pro modul serveru MySQL. Chcete-li aktualizovat parametr serveru, použijte příkaz [AZ MySQL flexibilní-Server Parameter set](/cli/azure/mysql/flexible-server/parameter) . 

Pokud chcete aktualizovat parametr serveru s **pomalým \_ dotazem \_** na serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Pokud chcete resetovat hodnotu parametru, vynechejte volitelný `--value` parametr a služba použije výchozí hodnotu. V příkladu výše by vypadalo takto:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Tento kód resetuje **\_ \_ protokol pomalých dotazů** na výchozí hodnotu **vypnuto**. 

## <a name="setting-non-modifiable-server-parameters"></a>Nastavení neupravitelných parametrů serveru

Pokud parametr serveru, který chcete aktualizovat, není upravitelný, můžete volitelně nastavit parametr na úrovni připojení pomocí `init_connect` . Tím se nastaví parametry serveru pro každého klienta, který se připojuje k serveru. 

Aktualizujte parametr **init \_ Connect** serveru serveru **mydemoserver.MySQL.Database.Azure.com** v části Skupina prostředků **myresourcegroup** pro nastavení hodnot, jako je například znaková sada.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> Pomocí příkazu `init_connect` je možné měnit parametry, které nevyžadují oprávnění SUPER na úrovni relace. Pokud chcete ověřit, jestli můžete nastavit parametr pomocí příkazu `init_connect`, spusťte příkaz `set session parameter_name=YOUR_DESIRED_VALUE;` – pokud selže s chybou **Přístup byl odepřen, potřebujete oprávnění SUPER**, znamená to, že tento parametr nemůžete nastavit pomocí příkazu init_connect.

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časových pásem

Tabulky časových pásem na vašem serveru se dají naplnit voláním `mysql.az_load_timezone` uložené procedury z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud spouštíte `mysql.az_load_timezone` příkaz z aplikace MySQL Workbench, možná bude nutné nejprve vypnout režim bezpečné aktualizace pomocí nástroje `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Je nutné restartovat server, aby bylo zajištěno, že tabulky časových pásem budou správně vyplněny.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Chcete-li zobrazit dostupné hodnoty časového pásma, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení časového pásma globální úrovně

Časové pásmo globální úrovně lze nastavit pomocí příkazu [AZ MySQL flexibilní-Server Parameter set](/cli/azure/mysql/flexible-server/parameter) .

Následující příkaz aktualizuje parametr serveru **časového \_ pásma** **mydemoserver.MySQL.Database.Azure.com** ve skupině prostředků **myresourcegroup** na **USA/Tichomoří**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovně relace

Časové pásmo úrovně relace můžete nastavit spuštěním `SET time_zone` příkazu z nástroje, jako je třeba příkazový řádek MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo na časové pásmo **USA/Tichomoří** .  

```sql
SET time_zone = 'US/Pacific';
```

Informace o [funkcích pro datum a čas](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)najdete v dokumentaci k MySQL.


## <a name="next-steps"></a>Další kroky

- Postup konfigurace [parametrů serveru v Azure Portal](./how-to-configure-server-parameters-portal.md)
