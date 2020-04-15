---
title: Protokoly auditování přístupu – Azure CLI – databáze Azure pro MariaDB
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MariaDB z rozhraní příkazového příkazu k Andazure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384191"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu v rozhraní příkazového příkazu Azure

Můžete nakonfigurovat [Azure Database pro protokoly auditu MariaDB](concepts-audit-logs.md) z rozhraní příkazového příkazového příkazu Konto Azure.

> [!IMPORTANT]
> Funkce protokolu auditu je nyní ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

Chcete-li projít tento návod, co potřebujete:

- [Databáze Azure pro server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolení a konfigurace protokolování auditu pomocí následujících kroků: 

1. Zapněte protokoly auditu nastavením **parametru audit_logs_enabled** na "ON". 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Vyberte [typy událostí,](concepts-audit-logs.md#configure-audit-logging) které mají být zaznamenány, aktualizací parametru **audit_log_egitvents.**
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Přidejte všechny uživatele MariaDB, kteří budou vyloučeni z protokolování aktualizací **parametru audit_log_exclude_users.** Zadejte uživatele zadáním jejich mariadb uživatelské jméno.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Přidejte všechny konkrétní uživatele MariaDB, které mají být zahrnuty pro protokolování aktualizací **parametru audit_log_include_users.** Zadejte uživatele zadáním jejich mariadb uživatelské jméno.
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MariaDB
- Přečtěte si, jak [nakonfigurovat](howto-configure-audit-logs-portal.md) protokoly auditu na webu Azure Portal