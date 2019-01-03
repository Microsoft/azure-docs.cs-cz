---
title: Přístup protokolů serveru ve službě Azure Database pro MariaDB s použitím rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak získat přístup k protokolům serveru ve službě Azure Database pro MariaDB s použitím nástroje příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/10/2018
ms.openlocfilehash: 403f111882b1bd151b26af56fd614355e7ba88e3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539207"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a získat přístup k protokolům serveru pomocí příkazového řádku Azure
Azure Database pro MariaDB serverové protokoly můžete stáhnout pomocí příkazového řádku Azure, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v této příručce s postupy, musíte:
- [Azure Database pro MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Nakonfigurujte si protokolování pro službu Azure Database pro MariaDB
Můžete nakonfigurovat server pro přístup k protokolu pomalých dotazů MariaDB podle následujících kroků:
1. Zapnutí protokolování tak, že nastavíte **pomalé\_dotazu\_protokolu** parametr ON.
2. Upravit další parametry, jako například **dlouhé\_dotazu\_čas** a **protokolu\_pomalé\_správce\_příkazy**.

Zjistěte, jak nastavit hodnoty těchto parametrů pomocí Azure CLI, najdete v článku [konfigurace parametrů serveru](howto-configure-server-parameters-cli.md).

Například následující příkaz rozhraní příkazového řádku zapne protokolu pomalých dotazů, nastaví doba dlouhého dotazu na 10 sekund a pak ji vypne protokolování pomalých správce příkazu. Nakonec se zobrazí možnosti konfigurace pro kontrolu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Seznam protokolů pro službu Azure Database pro MariaDB server
Chcete-li seznam souborů protokolu k dispozici pro váš server, spusťte [az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) příkazu.

Můžete zobrazit seznam souborů protokolu pro server **mydemoserver.mariadb.database.azure.com** ve skupině prostředků **myresourcegroup**. Potom směrovat seznam souborů protokolů do textového souboru s názvem **protokolu\_soubory\_seznam.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
S [az mariadb server-logs Stáhnout](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) příkazu si můžete stáhnout jednotlivých protokolových souborů pro váš server.

Použijte následující příklad ke stažení souboru protokolu specifické pro server **mydemoserver.mariadb.database.azure.com** ve skupině prostředků **myresourcegroup** do vašeho místního prostředí.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další postup
- Další informace o [protokolů serveru ve službě Azure Database pro MariaDB](concepts-server-logs.md).
