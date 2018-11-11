---
title: Nastavit parametry služby ve službě Azure Database pro MariaDB
description: Tento článek popisuje, jak nastavit parametry služby ve službě Azure Database pro MariaDB pomocí nástroje příkazového řádku Azure CLI.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 4e391ce56b31f35da67b3d975bbc1290c17cb139
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516144"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>Přizpůsobení parametrů konfigurace serveru pomocí příkazového řádku Azure
Seznam, zobrazit a aktualizovat parametry konfigurace pro službu Azure Database pro MariaDB server pomocí rozhraní příkazového řádku Azure, nástroje příkazového řádku Azure. Podmnožina stroj konfigurace je zveřejněný na úrovni serveru a je možné upravit.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database pro MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo pomocí Azure Cloud Shell v prohlížeči.

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Seznam parametrů konfigurace serveru pro službu Azure Database pro MariaDB serveru
Seznam všech parametrů upravitelná v serveru a jejich hodnoty, spusťte [seznamu konfigurací serveru mariadb az](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) příkazu.

Můžete vytvořit seznam parametrů konfigurace serveru pro server **mydemoserver.mariadb.database.azure.com** ve skupině prostředků **myresourcegroup**.
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

Definice všech uvedených parametrů naleznete v části MariaDB odkaz na [serveru systémové proměnné](https://mariadb.com/kb/en/library/server-system-variables/).

## <a name="show-server-configuration-parameter-details"></a>Detaily parametr konfigurace serveru
Chcete-li zobrazit podrobnosti o konkrétní konfiguraci parametru pro server, spusťte [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) příkazu.

Tento příklad ukazuje podrobnosti o **pomalé\_dotazu\_protokolu** parametr konfigurace serveru pro server **mydemoserver.mariadb.database.azure.com** v rámci skupiny prostředků **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Změnit hodnotu parametru konfigurace serveru
Můžete také změnit hodnotu parametru určité server konfigurace, která aktualizuje hodnotu základní konfigurace pro MariaDB server engine. Chcete-li aktualizovat konfiguraci, použijte [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) příkazu. 

Aktualizovat **pomalé\_dotazu\_protokolu** server konfigurační parametr serveru **mydemoserver.mariadb.database.azure.com** ve skupině prostředků  **myresourcegroup.**
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

Pokud chcete resetovat hodnotu konfigurační parametr vynechat, nechte nepovinný `--value` parametr a službou použije výchozí hodnotu. Pro výše uvedeném příkladu to vypadat nějak takto:
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

Tento kód nastaví **pomalé\_dotazu\_protokolu** konfigurace na výchozí hodnotu **OFF**. 

## <a name="working-with-the-time-zone-parameter"></a>Práce s parametrem časového pásma

### <a name="populating-the-time-zone-tables"></a>Naplnění tabulek časové pásmo

Časové pásmo tabulky na vašem serveru je možné naplnit s voláním `az_load_timezone` uloženou proceduru z nástroje, jako je MariaDB příkazového řádku nebo MariaDB Workbench.

> [!NOTE]
> Pokud používáte `az_load_timezone` příkaz z aplikace MariaDB Workbench, budete muset nejprve vypnout režim bezpečného aktualizace pomocí `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

Chcete-li zobrazit hodnoty dostupné časové pásmo, spusťte následující příkaz:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Nastavení na globální úrovni časové pásmo

Na globální úrovni časové pásmo lze nastavit pomocí [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) příkazu.

Aktualizace zadáním následujícího příkazu **čas\_zóny** server konfigurační parametr serveru **mydemoserver.mariadb.database.azure.com** ve skupině prostředků  **myresourcegroup** k **US / Tichomoří**.

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Nastavení časového pásma úrovni relace

Relace spuštěním můžete nastavit úroveň časového pásma `SET time_zone` z nástroje, jako je MariaDB příkazového řádku nebo MariaDB Workbench. Následující příklad nastaví časové pásmo **US / Tichomoří** časové pásmo.  

```sql
SET time_zone = 'US/Pacific';
```

MariaDB dokumentaci pro [funkce data a času](https://mariadb.com/kb/en/library/date-time-functions/).

## <a name="next-steps"></a>Další postup

- Jak nakonfigurovat [parametrů serveru na webu Azure portal](howto-server-parameters.md)