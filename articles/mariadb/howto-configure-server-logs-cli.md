---
title: Přístup k protokolům pomalých dotazů – Azure CLI – databáze Azure pro MariaDB
description: Tento článek popisuje, jak získat přístup k pomalé protokoly v Azure Database pro MariaDB pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527652"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům pomalých dotazů pomocí rozhraní příkazového příkazu Azure
Azure Database for MariaDB pomalé protokoly dotazů můžete stáhnout pomocí Azure CLI, nástroj příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Databáze Azure pro server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Azure [CLI](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurace protokolování pro azure databázi pro MariaDB
Server můžete nakonfigurovat tak, aby přistupoval k pomalému protokolu dotazů MariaDB, a to provedením následujících kroků:
1. Zapněte protokolování nastavením parametru **protokolu\_pomalého dotazu\_** na ZAPNUTO.
2. Upravte další parametry, například **\_dlouhou dobu dotazu\_** a **protokolujte\_pomalé\_příkazy správce\_**.

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure, najdete v [tématu Konfigurace parametrů serveru](howto-configure-server-parameters-cli.md).

Například následující příkaz rozhraní příkazu příkazu PŘÍKAZ CLI zapne protokol pomalého dotazu, nastaví dlouhou dobu dotazu na 10 sekund a potom vypne protokolování příkazu pomalé správy. Nakonec obsahuje seznam možností konfigurace pro vaši recenzi.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Seznam protokolů pro azure databáze pro server MariaDB
Chcete-li zobrazit seznam dostupných pomalých souborů protokolu dotazů pro váš server, spusťte příkaz [seznamu protokolů serveru az mariadb.](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list)

Soubory protokolu pro server **mydemoserver.mariadb.database.azure.com** můžete uvést v části skupina prostředků **myresourcegroup**. Potom nasměrujte seznam souborů protokolu do textového souboru nazvaného **list.txt\_souborů\_protokolu**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stažení protokolů ze serveru
Pomocí příkazu [stahování serverových protokolů az mariadb](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) si můžete stáhnout jednotlivé soubory protokolu pro váš server.

Následující příklad slouží ke stažení konkrétního souboru protokolu pro server **mydemoserver.mariadb.database.azure.com** v rámci skupiny prostředků **myresourcegroup** do místního prostředí.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Další informace o [pomalé protokoly dotazů v Azure Database pro MariaDB](concepts-server-logs.md).
