---
title: Přístup k protokolům auditu – Azure CLI – Azure Database for MariaDB
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MariaDB z Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: e9716f0fa8e0ae44d614bbb28ed6846105e683d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81384191"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu v rozhraní příkazového řádku Azure

[Protokoly auditu Azure Database for MariaDB](concepts-audit-logs.md) můžete nakonfigurovat z rozhraní příkazového řádku Azure CLI.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje použití Azure CLI verze 2,0 nebo novější. Verzi ověříte tak, že v příkazovém řádku Azure CLI zadáte `az --version`. Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

Povolte a nakonfigurujte protokolování auditu pomocí následujících kroků: 

1. Zapněte protokoly auditu nastavením parametru **audit_logs_enabled** na zapnuto. 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_egitvents** .
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