---
title: Přístup k protokolům pomalých dotazů – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak získat přístup k protokolům pomalých dotazů v Azure Database for MySQL pomocí azure cli.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 93840af61a69599447588be01869a20290b2db94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062486"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům pomalých dotazů pomocí rozhraní příkazového příkazu Azure
Protokoly pomalých dotazů Azure Database for MySQL si můžete stáhnout pomocí azure cli, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Azure [CLI](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurace protokolování
Server můžete nakonfigurovat tak, aby přistupoval k pomalému protokolu dotazů MySQL, a to provedením následujících kroků:
1. Zapněte pomalé protokolování dotazů nastavením parametru **protokolu\_pomalého dotazu\_** na ZAPNUTO.
2. Upravte další parametry, například **\_dlouhou dobu dotazu\_** a **protokolujte\_pomalé\_příkazy správce\_**.

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure, najdete v [tématu Konfigurace parametrů serveru](howto-configure-server-parameters-using-cli.md).

Například následující příkaz rozhraní příkazu příkazu PŘÍKAZ CLI zapne protokol pomalého dotazu, nastaví dlouhou dobu dotazu na 10 sekund a potom vypne protokolování příkazu pomalé správy. Nakonec obsahuje seznam možností konfigurace pro vaši recenzi.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Protokoly seznamů pro Azure Database pro server MySQL
Chcete-li zobrazit seznam dostupných pomalých souborů protokolu dotazů pro váš server, spusťte příkaz [seznamu protokolů az mysql server.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Soubory protokolu pro server **mydemoserver.mysql.database.azure.com** můžete uvést v rámci skupiny prostředků **myresourcegroup**. Potom nasměrujte seznam souborů protokolu do textového souboru nazvaného **list.txt\_souborů\_protokolu**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stažení protokolů ze serveru
Pomocí příkazu [stahování az mysql server-logs](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) si můžete stáhnout jednotlivé soubory protokolu pro váš server. 

Následující příklad slouží ke stažení konkrétního souboru protokolu pro server **mydemoserver.mysql.database.azure.com** v rámci skupiny prostředků **myresourcegroup** do místního prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech pomalých dotazů v Azure Database for MySQL](concepts-server-logs.md).
