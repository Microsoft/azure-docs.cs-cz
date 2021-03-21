---
title: Přístup k protokolům auditu – Azure CLI – Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MariaDB z Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1d446d8ee2863077ad84c361876758336f5a3cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665170"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu Azure Database for Marie DB v Azure CLI

[Protokoly auditu Azure Database for MariaDB](concepts-audit-logs.md) můžete nakonfigurovat z rozhraní příkazového řádku Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

Postup dokončení této příručky:

- Potřebujete [server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu pomocí následujících kroků: 

1. Zapněte protokoly auditu nastavením parametru **audit_logs_enabled** na zapnuto. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Pomocí aktualizace parametru **audit_log_exclude_users** přidejte všechny uživatele MariaDB, kteří budou vyloučení z protokolování. Určete uživatele zadáním svého uživatelského jména MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Aktualizujte parametr **audit_log_include_users** tak, že přidáte všechny konkrétní uživatele MariaDB, kteří budou zahrnuti do protokolování. Určete uživatele zadáním svého uživatelského jména MariaDB.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MariaDB
- Naučte se konfigurovat protokoly auditu v [Azure Portal](howto-configure-audit-logs-portal.md)
