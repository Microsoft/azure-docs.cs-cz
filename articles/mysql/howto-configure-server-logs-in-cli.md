---
title: Přístup protokolů serveru ve službě Azure Database for MySQL pomocí Azure CLI
description: Tento článek popisuje, jak získat přístup k protokolům serveru ve službě Azure Database for MySQL pomocí nástroje příkazového řádku Azure CLI.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: e3940f07ab97eb2ad03c347c39bf2412a8497f5e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978450"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a získat přístup k protokolům serveru pomocí příkazového řádku Azure
Azure Database for MySQL – protokoly serveru si můžete stáhnout pomocí příkazového řádku Azure, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database for MySQL serveru](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging-for-azure-database-for-mysql"></a>Konfigurace protokolování pro službu Azure Database for MySQL
Můžete nakonfigurovat server pro přístup k protokolu pomalých dotazů MySQL pomocí následujících kroků:
1. Zapnutí protokolování tak, že nastavíte **pomalé\_dotazu\_protokolu** parametr ON.
2. Upravit další parametry, jako například **dlouhé\_dotazu\_čas** a **protokolu\_pomalé\_správce\_příkazy**.

Zjistěte, jak nastavit hodnoty těchto parametrů pomocí Azure CLI, najdete v článku [konfigurace parametrů serveru](howto-configure-server-parameters-using-cli.md). 

Například následující příkaz rozhraní příkazového řádku zapne protokolu pomalých dotazů, nastaví doba dlouhého dotazu na 10 sekund a pak ji vypne protokolování pomalých správce příkazu. Nakonec se zobrazí možnosti konfigurace pro kontrolu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Protokoly seznamu pro službu Azure Database for MySQL serveru
Chcete-li seznam souborů protokolu k dispozici pro váš server, spusťte [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) příkazu.

Můžete zobrazit seznam souborů protokolu pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup**. Potom směrovat seznam souborů protokolů do textového souboru s názvem **protokolu\_soubory\_seznam.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
S [az mysql server-logs Stáhnout](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) příkazu si můžete stáhnout jednotlivých protokolových souborů pro váš server. 

Použijte následující příklad ke stažení souboru protokolu specifické pro server **mydemoserver.mysql.database.azure.com** ve skupině prostředků **myresourcegroup** do vašeho místního prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další postup
- Další informace o [protokolů serveru ve službě Azure Database for MySQL](concepts-server-logs.md).
