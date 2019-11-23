---
title: Konfigurace a přístup k protokolům serveru pro PostgreSQL-Single server pomocí Azure CLI
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům serveru v Azure Database for PostgreSQL-Single server pomocí příkazového řádku Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bb33debaa23ad8625b6ddc1cc63738b13bcd19e1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023624"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Konfigurace a přístup k protokolům serveru pomocí Azure CLI
Protokoly chyb serveru PostgreSQL můžete stáhnout pomocí rozhraní příkazového řádku (Azure CLI). Přístup k protokolům transakcí ale není podporovaný. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete projít tento průvodce, budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Nástroj příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo Azure Cloud Shell v prohlížeči

## <a name="configure-logging"></a>Konfigurovat protokolování
Server můžete nakonfigurovat tak, aby měl přístup k protokolům dotazů a protokolům chyb. Protokoly chyb můžou mít informace o automatickém vaku, připojení a kontrolním bodu.
1. Zapněte protokolování.
2. Pokud chcete povolit protokolování dotazů, aktualizujte **příkaz log\_** a **log\_min\_Duration\_** .
3. Aktualizovat dobu uchování.

Další informace najdete v tématu [přizpůsobení parametrů konfigurace serveru](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Výpis protokolů
Chcete-li zobrazit seznam dostupných souborů protokolu pro váš server, spusťte příkaz [AZ Postgres Server-logs list](/cli/azure/postgres/server-logs) .

Soubory protokolu pro server **mydemoserver.Postgres.Database.Azure.com** můžete zobrazit v části Skupina prostředků **myresourcegroup**. Pak nasměrujte seznam souborů protokolu do textového souboru s názvem **log\_soubory\_list. txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Stahovat protokoly místně ze serveru
Pomocí příkazu [AZ Postgres Server-logs Download](/cli/azure/postgres/server-logs) si můžete stáhnout jednotlivé soubory protokolu pro váš server. 

Použijte následující příklad ke stažení konkrétního souboru protokolu pro server **mydemoserver.Postgres.Database.Azure.com** ve skupině prostředků **myresourcegroup** do svého místního prostředí.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Další kroky
- Další informace o protokolech serveru najdete [v tématu protokoly serveru v Azure Database for PostgreSQL](concepts-server-logs.md).
- Další informace o parametrech serveru najdete v tématu [přizpůsobení parametrů konfigurace serveru pomocí Azure CLI](howto-configure-server-parameters-using-cli.md).
