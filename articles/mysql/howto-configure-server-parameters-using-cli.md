---
title: Nastavit parametry služby ve službě Azure Database for MySQL
description: Tento článek popisuje, jak nastavit parametry služby ve službě Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: 61fee0771d6847a0ec56de656057409bbcdcba16
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433737"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pomocí příkazového řádku Azure
Seznam, zobrazit a aktualizovat parametry konfigurace pro serveru Azure Database for MySQL pomocí Azure CLI, nástroj příkazového řádku Azure. Podmnožina stroj konfigurace je zveřejněný na úrovni serveru a je možné upravit. 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí Azure Cloud Shell v prohlížeči.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Seznam parametrů konfigurace serveru pro službu Azure Database pro MySQL server
Seznam všech parametrů upravitelná v serveru a jejich hodnoty, spusťte [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) příkazu.

Můžete vytvořit seznam parametrů konfigurace serveru pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definice všech uvedených parametrů najdete v části odkaz MySQL na [serveru systémové proměnné](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Detaily parametr konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétní konfiguraci parametru pro server, spusťte [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) příkazu.

Tento příklad ukazuje podrobnosti o **pomalé\_dotazu\_protokolu** parametr konfigurace serveru pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Změnit hodnotu parametru konfigurace serveru
Můžete také změnit hodnotu parametru určité server konfigurace, která aktualizuje hodnotu základní konfigurace pro server stroje MySQL. Chcete-li aktualizovat konfiguraci, použijte [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) příkazu. 

Aktualizovat **pomalé\_dotazu\_protokolu** server konfigurační parametr serveru **mydemoserver.mysql.database.azure.com** ve skupině prostředků  **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Pokud chcete resetovat hodnotu konfigurační parametr vynechat, nechte nepovinný `--value` parametr a službou použije výchozí hodnotu. Pro výše uvedeném příkladu to vypadat nějak takto:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Tento kód nastaví **pomalé\_dotazu\_protokolu** konfigurace na výchozí hodnotu **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časové pásmo

Časové pásmo tabulky na vašem serveru je možné naplnit s voláním `az_load_timezone` uloženou proceduru z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench.

> [!NOTE]
> Pokud používáte `az_load_timezone` příkaz z aplikace MySQL Workbench, budete muset nejprve vypnout režim bezpečného aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Chcete-li zobrazit hodnoty dostupné časové pásmo, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení na globální úrovni časové pásmo

Na globální úrovni časové pásmo lze nastavit pomocí [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) příkazu.

Aktualizace zadáním následujícího příkazu **čas\_zóny** server konfigurační parametr serveru **mydemoserver.mysql.database.azure.com** ve skupině prostředků  **myresourcegroup** k **US / Tichomoří**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovni relace

Relace spuštěním můžete nastavit úroveň časového pásma `SET time_zone` z některého nástroje, například na příkazovém řádku MySQL nebo MySQL Workbench. Následující příklad nastaví časové pásmo **US / Tichomoří** časové pásmo.  

```sql
SET time_zone = 'US/Pacific';
```

Naleznete v dokumentaci MySQL pro [funkce data a času](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Další postup

- Jak nakonfigurovat [parametrů serveru na webu Azure portal](howto-server-parameters.md)