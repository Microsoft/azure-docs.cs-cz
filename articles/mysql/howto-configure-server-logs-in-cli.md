---
title: Přístup k protokolům pomalých dotazů – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak získat přístup k protokolům pomalých dotazů v Azure Database for MySQL pomocí azure cli.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: 87db1a2af0bfdc854c909ef4221a3d97f9bf10d5
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270668"
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
2. Vyberte, kde se mají protokoly výstupovat pomocí **výstupu protokolu\_**. Pokud chcete odeslat protokoly do místního úložiště i do diagnostických protokolů monitorování Azure, vyberte **Soubor**. Chcete-li odeslat protokoly jenom do protokolů sledování Azure, vyberte **možnost Žádné.**
3. Upravte další parametry, například **\_dlouhou dobu dotazu\_** a **protokolujte\_pomalé\_příkazy správce\_**.

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure, najdete v [tématu Konfigurace parametrů serveru](howto-configure-server-parameters-using-cli.md).

Například následující příkaz rozhraní příkazu příkazu PŘÍKAZ CLI zapne protokol pomalého dotazu, nastaví dlouhou dobu dotazu na 10 sekund a potom vypne protokolování příkazu pomalé správy. Nakonec obsahuje seznam možností konfigurace pro vaši recenzi.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Protokoly seznamů pro Azure Database pro server MySQL
Pokud **je log_output** nakonfigurován na "Soubor", můžete přistupovat k protokolům přímo z místního úložiště serveru. Chcete-li zobrazit seznam dostupných pomalých souborů protokolu dotazů pro váš server, spusťte příkaz [seznamu protokolů az mysql server.](/cli/azure/mysql/server-logs#az-mysql-server-logs-list)

Soubory protokolu pro server **mydemoserver.mysql.database.azure.com** můžete uvést v rámci skupiny prostředků **myresourcegroup**. Potom nasměrujte seznam souborů protokolu do textového souboru nazvaného **list.txt\_souborů\_protokolu**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stažení protokolů ze serveru
Pokud **log_output** je nakonfigurován na "Soubor", můžete stáhnout jednotlivé soubory protokolu ze serveru pomocí [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) příkaz.

Následující příklad slouží ke stažení konkrétního souboru protokolu pro server **mydemoserver.mysql.database.azure.com** v rámci skupiny prostředků **myresourcegroup** do místního prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech pomalých dotazů v Azure Database for MySQL](concepts-server-logs.md).
