---
title: Přístup k protokolům pomalým dotazům – Azure CLI-Azure Database for MySQL
description: Tento článek popisuje, jak získat přístup k protokolům pomalým dotazům v Azure Database for MySQL pomocí rozhraní příkazového řádku Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 945a67f81010a61adf814f6f6f422eba5001b48d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998545"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům pomalým dotazům pomocí Azure CLI
Protokoly pomalých dotazů Azure Database for MySQL můžete stáhnout pomocí Azure CLI, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurovat protokolování
Server můžete nakonfigurovat tak, aby se přihlásil k protokolu pomalým dotazů MySQL, a to provedením následujících kroků:
1. Zapněte pomalé protokolování dotazů nastavením parametru **pomalého \_ \_ protokolu dotazů** na zapnuto.
2. Vyberte, kde se mají protokoly výstupovat, a použijte přitom **\_ výstup protokolu**. Pokud chcete odesílat protokoly do místního úložiště i Azure Monitor diagnostické protokoly, vyberte **soubor**. Pokud chcete odesílat protokoly jenom do protokolů Azure Monitor, vyberte **žádné** .
3. Úprava dalších parametrů, jako jsou **například \_ dlouhé \_ časy dotazů** a **\_ \_ \_ příkazy správce protokolu s pomalým protokolem**.

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure CLI, najdete v tématu [Postup konfigurace parametrů serveru](howto-configure-server-parameters-using-cli.md).

Například následující příkaz CLI zapíná protokol pomalých dotazů, nastavuje dlouhou dobu dotazování na 10 sekund a pak vypne protokolování pomalého příkazu správce. Nakonec zobrazí seznam možností konfigurace pro vaši kontrolu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Vypsat protokoly pro Azure Database for MySQL server
Pokud je **log_output** nakonfigurovaná na soubor, můžete získat přístup k protokolům přímo z místního úložiště serveru. Chcete-li zobrazit seznam dostupných souborů protokolu pomalých dotazů pro váš server, spusťte příkaz [AZ MySQL server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Soubory protokolu pro server **mydemoserver.MySQL.Database.Azure.com** můžete zobrazit v části Skupina prostředků **myresourcegroup**. Pak nasměrujte seznam souborů protokolu do textového souboru s názvem **\_ soubory protokolu \_list.txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
Pokud je **log_output** nakonfigurovaná na "soubor", můžete jednotlivé soubory protokolu stáhnout ze serveru pomocí příkazu [AZ MySQL server-logs Download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) .

Použijte následující příklad ke stažení konkrétního souboru protokolu pro server **mydemoserver.MySQL.Database.Azure.com** ve skupině prostředků **myresourcegroup** do svého místního prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [protokolech pomalých dotazů v Azure Database for MySQL](concepts-server-logs.md).
