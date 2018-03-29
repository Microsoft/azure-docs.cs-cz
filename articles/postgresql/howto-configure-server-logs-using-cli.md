---
title: Konfigurace a přístup k protokolům serveru pro PostgreSQL pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje postup konfigurace a přístup v protokolech serveru v Azure databázi PostgreSQL pomocí příkazového řádku Azure CLI.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 951dcca562c08698b4ce4528d005fc91152ea337
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům serveru pomocí rozhraní příkazového řádku Azure
Protokoly chyb serveru PostgreSQL si můžete stáhnout pomocí rozhraní příkazového řádku (Azure CLI). Přístup k protokoly transakcí však není podporován. 

## <a name="prerequisites"></a>Předpoklady
Chcete-li krok tímto průvodcem postupy, je třeba:
- [Azure databázi PostgreSQL serveru.](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) nástroj příkazového řádku nebo prostředí cloudu Azure v prohlížeči

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Konfigurace protokolování pro databázi Azure pro PostgreSQL
Můžete nastavit server pro přístup k dotazu protokoly a protokoly chyb. Protokoly chyb může mít informace o automatické vakuu, připojení a kontrolního bodu.
1. Zapněte protokolování.
2. Chcete-li povolit protokolování dotazu, aktualizujte **protokolu\_příkaz** a **protokolu\_min\_doba trvání\_příkaz**.
3. Aktualizujte dobu uchování.

Další informace najdete v tématu [přizpůsobení parametry konfigurace serveru](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Seznam protokolů pro databázi Azure pro PostgreSQL server
Chcete-li zobrazit seznam souborů k dispozici protokolu pro váš server, spusťte [az postgres protokoly serveru seznamu](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) příkaz.

Můžete seznam souborů protokolu pro server **mydemoserver.postgres.database.azure.com** ve skupině prostředků **myresourcegroup**. Potom směrovat seznam souborů protokolů a do textového souboru s názvem **protokolu\_soubory\_seznam.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Stažení protokolů místně ze serveru
Pomocí [stáhnout az postgres – protokoly serveru](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) příkaz, si můžete stáhnout jednotlivých protokolových souborů pro váš server. 

Pomocí následujícího příkladu ke stažení konkrétním souboru protokolu pro server **mydemoserver.postgres.database.azure.com** ve skupině prostředků **myresourcegroup** na vašem místním prostředí.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Další postup
- Další informace o protokolech serveru najdete v tématu [protokolech serveru v Azure databázi PostgreSQL](concepts-server-logs.md).
- Další informace o parametrech serveru najdete v tématu [přizpůsobit parametry konfigurace serveru pomocí rozhraní příkazového řádku Azure](howto-configure-server-parameters-using-cli.md).
