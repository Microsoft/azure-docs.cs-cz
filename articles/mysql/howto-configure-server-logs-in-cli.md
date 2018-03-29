---
title: Přístup v protokolech serveru ve službě Azure Database pro databázi MySQL pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak získat přístup v protokolech serveru v databázi Azure pro databázi MySQL pomocí nástroje příkazového řádku Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 037f631b3b23e66f1ed54106cc5be14a809b2e22
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům serveru pomocí rozhraní příkazového řádku Azure
Databáze Azure pro protokoly MySQL serveru si můžete stáhnout pomocí rozhraní příkazového řádku Azure, nástroj příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Chcete-li krok tímto průvodcem postupy, je třeba:
- [Databáze MySQL serveru Azure](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) nebo prostředí cloudu Azure v prohlížeči

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurace protokolování pro databázi Azure pro databázi MySQL
Můžete nastavit server pro přístup k protokolu pomalé dotazu MySQL provedením následujících kroků:
1. Zapnutí protokolování nastavením **pomalé\_dotazu\_protokolu** parametr na hodnotu ON.
2. Nastavit další parametry, jako například **dlouho\_dotazu\_čas** a **protokolu\_pomalé\_správce\_příkazy**.

Zjistěte, jak nastavit hodnotu z těchto parametrů prostřednictvím rozhraní příkazového řádku Azure, najdete v tématu [postup konfigurace serveru parametry](howto-configure-server-parameters-using-cli.md). 

Například následující příkaz rozhraní příkazového řádku zapne protokol pomalé dotazu, nastaví čas dlouhého dotazu na 10 sekund a pak vypne protokolování příkaz pomalé správce. Nakonec se zobrazí seznam možností konfigurace pro zkontrolovali.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Seznam protokolů pro databázi Azure pro server databáze MySQL
Chcete-li zobrazit seznam souborů k dispozici protokolu pro váš server, spusťte [seznamu protokolů serveru mysql az](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) příkaz.

Můžete seznam souborů protokolu pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup**. Potom směrovat seznam souborů protokolů a do textového souboru s názvem **protokolu\_soubory\_seznam.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stažení protokolů ze serveru
Pomocí [stáhnout az mysql – protokoly serveru](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) příkaz, si můžete stáhnout jednotlivých protokolových souborů pro váš server. 

Pomocí následujícího příkladu ke stažení konkrétním souboru protokolu pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup** na vašem místním prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další postup
- Další informace o [v protokolech serveru ve službě Azure Database pro databázi MySQL](concepts-server-logs.md).
