---
title: Přístup k protokolům pomalým dotazům – Azure CLI-Azure Database for MariaDB
description: Tento článek popisuje, jak získat přístup k pomalým protokolům v Azure Database for MariaDB pomocí nástroje příkazového řádku Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539868"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům Azure Database for Marie DB pomalým dotazům pomocí Azure CLI

Protokoly pomalých dotazů Azure Database for MariaDB můžete stáhnout pomocí Azure CLI, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurovat protokolování
Server můžete nakonfigurovat tak, aby se přihlásil k protokolu pomalým dotazů MySQL, a to provedením následujících kroků:
1. Zapněte pomalé protokolování dotazů nastavením parametru **pomalého \_ \_ protokolu dotazů** na zapnuto.
2. Vyberte, kde se mají protokoly výstupovat, a použijte přitom **\_ výstup protokolu**. Pokud chcete odesílat protokoly do místního úložiště i Azure Monitor diagnostické protokoly, vyberte **soubor**. Pokud chcete odesílat protokoly jenom do protokolů Azure Monitor, vyberte **žádné** .
3. Úprava dalších parametrů, jako jsou **například \_ dlouhé \_ časy dotazů** a **\_ \_ \_ příkazy správce protokolu s pomalým protokolem**.

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure CLI, najdete v tématu [Postup konfigurace parametrů serveru](howto-configure-server-parameters-cli.md).

Například následující příkaz CLI zapíná protokol pomalých dotazů, nastavuje dlouhou dobu dotazování na 10 sekund a pak vypne protokolování pomalého příkazu správce. Nakonec zobrazí seznam možností konfigurace pro vaši kontrolu.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Vypsat protokoly pro Azure Database for MariaDB Server
Pokud je **log_output** nakonfigurovaná na soubor, můžete získat přístup k protokolům přímo z místního úložiště serveru. Chcete-li zobrazit seznam dostupných souborů protokolu pomalých dotazů pro váš server, spusťte příkaz [AZ MariaDB Server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Soubory protokolu pro server **mydemoserver.MariaDB.Database.Azure.com** můžete zobrazit v části Skupina prostředků **myresourcegroup**. Pak nasměrujte seznam souborů protokolu do textového souboru s názvem **\_ soubory protokolu \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
Pokud je **log_output** nakonfigurovaná na "soubor", můžete jednotlivé soubory protokolu stáhnout ze serveru pomocí příkazu [AZ MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Použijte následující příklad ke stažení konkrétního souboru protokolu pro server **mydemoserver.MariaDB.Database.Azure.com** ve skupině prostředků **myresourcegroup** do svého místního prostředí.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [protokolech pomalých dotazů v Azure Database for MariaDB](concepts-server-logs.md).
