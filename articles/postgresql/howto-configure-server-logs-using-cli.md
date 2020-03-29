---
title: Správa protokolů – Azure CLI – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům serveru (.soubory protokolu) v Azure Database for PostgreSQL – single server pomocí rozhraní příkazového příkazu k webu Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: be679be91d49516bd2f6c672eb53640cfad2ae2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74763568"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům serveru pomocí rozhraní příkazového příkazu Azure
Protokoly chyb serveru PostgreSQL můžete stáhnout pomocí rozhraní příkazového řádku (Azure CLI). Přístup k protokolům transakcí však není podporován. 

## <a name="prerequisites"></a>Požadavky
Chcete-li projít tento návod, co potřebujete:
- [Databáze Azure pro postgreSQL server](quickstart-create-server-database-azure-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurace protokolování
Server můžete nakonfigurovat pro přístup k protokolům dotazů a protokolům chyb. Protokoly chyb mohou mít informace o automatickém vakuu, připojení a kontrolním bodu.
1. Zapněte protokolování.
2. Chcete-li povolit protokolování dotazů, aktualizujte **\_příkaz protokolu** a příkaz doby trvání **\_\_\_min protokolu**.
3. Aktualizovat dobu uchování.

Další informace naleznete v [tématu Přizpůsobení parametrů konfigurace serveru](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Protokoly seznamů
Chcete-li zobrazit seznam dostupných souborů protokolu pro váš server, spusťte příkaz [az postgres server-logs.](/cli/azure/postgres/server-logs)

Soubory protokolu pro server **mydemoserver.postgres.database.azure.com** můžete uvést ve skupině prostředků **myresourcegroup**. Potom nasměrujte seznam souborů protokolu do textového souboru nazvaného **list.txt\_souborů\_protokolu**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Stáhnout protokoly místně ze serveru
Pomocí příkazu [stahování az postgres server-logs](/cli/azure/postgres/server-logs) můžete stáhnout jednotlivé soubory protokolu pro váš server. 

Následující příklad slouží ke stažení konkrétního souboru protokolu pro server **mydemoserver.postgres.database.azure.com** v rámci skupiny prostředků **myresourcegroup** do místního prostředí.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Další kroky
- Další informace o protokolech serveru najdete [v tématu Protokoly serveru v Azure Database for PostgreSQL](concepts-server-logs.md).
- Další informace o parametrech serveru naleznete v [tématu Přizpůsobení parametrů konfigurace serveru pomocí rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).
