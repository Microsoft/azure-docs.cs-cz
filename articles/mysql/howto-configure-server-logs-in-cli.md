---
title: Přístup k protokolům pomalým dotazům v Azure Database for MySQL pomocí Azure CLI
description: Tento článek popisuje, jak získat přístup k protokolům pomalým dotazům v Azure Database for MySQL pomocí rozhraní příkazového řádku Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 0ab4162d11642ec7df53040bd744711002227497
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030631"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům pomalým dotazům pomocí Azure CLI
Protokoly pomalých dotazů Azure Database for MySQL můžete stáhnout pomocí Azure CLI, nástroje příkazového řádku Azure.

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurovat protokolování
Server můžete nakonfigurovat tak, aby se přihlásil k protokolu pomalým dotazů MySQL, a to provedením následujících kroků:
1. Zapněte pomalé protokolování dotazů nastavením parametru **pomalé\_dotaz\_protokol** na zapnuto.
2. Upravte další parametry, třeba **long\_dotaz\_Time** a **log\_pomalé\_příkazy správce\_** .

Informace o tom, jak nastavit hodnotu těchto parametrů prostřednictvím rozhraní příkazového řádku Azure CLI, najdete v tématu [Postup konfigurace parametrů serveru](howto-configure-server-parameters-using-cli.md).

Například následující příkaz CLI zapíná protokol pomalých dotazů, nastavuje dlouhou dobu dotazování na 10 sekund a pak vypne protokolování pomalého příkazu správce. Nakonec zobrazí seznam možností konfigurace pro vaši kontrolu.
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>Vypsat protokoly pro Azure Database for MySQL server
Chcete-li zobrazit seznam dostupných souborů protokolu pomalých dotazů pro váš server, spusťte příkaz [AZ MySQL server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) .

Soubory protokolu pro server **mydemoserver.MySQL.Database.Azure.com** můžete zobrazit v části Skupina prostředků **myresourcegroup**. Pak nasměrujte seznam souborů protokolu do textového souboru s názvem **log\_soubory\_list. txt**.
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Stáhnout protokoly ze serveru
Pomocí příkazu [AZ MySQL server-logs ke stažení](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) můžete stáhnout jednotlivé soubory protokolu pro váš server. 

Použijte následující příklad ke stažení konkrétního souboru protokolu pro server **mydemoserver.MySQL.Database.Azure.com** ve skupině prostředků **myresourcegroup** do svého místního prostředí.
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Další kroky
- Přečtěte si o [protokolech pomalých dotazů v Azure Database for MySQL](concepts-server-logs.md).
