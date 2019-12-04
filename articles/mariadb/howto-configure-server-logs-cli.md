---
title: Přístup k protokolům pomalým dotazům – Azure CLI-Azure Database for MariaDB
description: Tento článek popisuje, jak získat přístup k pomalým protokolům v Azure Database for MariaDB pomocí nástroje příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 32e73835732538813f90de5cb737429373c3762a
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767377"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům pomalým dotazům pomocí Azure CLI
Protokoly pomalých dotazů Azure Database for MariaDB můžete stáhnout pomocí Azure CLI, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Předpoklady
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Konfigurovat protokolování pro Azure Database for MariaDB
Server můžete nakonfigurovat tak, aby se přihlásil k protokolu MariaDB pomalých dotazů, a to provedením následujících kroků:
1. Zapněte protokolování nastavením parametru **pomalého\_dotaz\_protokolu** na zapnuto.
2. Upravte další parametry, třeba **long\_dotaz\_Time** a **log\_pomalé\_příkazy správce\_** .

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure CLI, najdete v tématu [Postup konfigurace parametrů serveru](howto-configure-server-parameters-cli.md).

Například následující příkaz CLI zapíná protokol pomalých dotazů, nastavuje dlouhou dobu dotazování na 10 sekund a pak vypne protokolování pomalého příkazu správce. Nakonec zobrazí seznam možností konfigurace pro vaši kontrolu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Vypsat protokoly pro Azure Database for MariaDB Server
Chcete-li zobrazit seznam dostupných souborů protokolu pomalých dotazů pro váš server, spusťte příkaz [AZ MariaDB Server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Soubory protokolu pro server **mydemoserver.MariaDB.Database.Azure.com** můžete zobrazit v části Skupina prostředků **myresourcegroup**. Pak nasměrujte seznam souborů protokolu do textového souboru s názvem **log\_soubory\_list. txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
Pomocí příkazu [AZ MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) si můžete stáhnout jednotlivé soubory protokolu pro váš server.

Použijte následující příklad ke stažení konkrétního souboru protokolu pro server **mydemoserver.MariaDB.Database.Azure.com** ve skupině prostředků **myresourcegroup** do svého místního prostředí.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [protokolech pomalých dotazů v Azure Database for MariaDB](concepts-server-logs.md).
