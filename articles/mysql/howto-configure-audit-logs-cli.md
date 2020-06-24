---
title: Přístup k protokolům auditu – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/18/2020
ms.openlocfilehash: a27bd98b723e6e827ba7d9e155586d336ababc49
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85100765"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu v rozhraní příkazového řádku Azure

[Protokoly auditu Azure Database for MySQL](concepts-audit-logs.md) můžete nakonfigurovat z rozhraní příkazového řádku Azure CLI.

> [!IMPORTANT]
> Funkce protokolu auditu je momentálně ve verzi Preview.

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:

- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje použití Azure CLI verze 2,0 nebo novější. Verzi ověříte tak, že v příkazovém řádku Azure CLI zadáte `az --version` . Informace o instalaci nebo upgradu najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!NOTE]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu pomocí následujících kroků:

1. Zapněte protokoly auditu nastavením parametru **audit_logs_enabled** na zapnuto. 
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. Vyberte [typy událostí](concepts-audit-logs.md#configure-audit-logging) , které se mají protokolovat, pomocí aktualizace parametru **audit_log_events** .
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. Pomocí aktualizace parametru **audit_log_exclude_users** přidejte všechny uživatele MySQL, které chcete vyloučit z protokolování. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. Aktualizujte parametr **audit_log_include_users** tím, že přidáte všechny konkrétní uživatele MySQL, které chcete zahrnout do protokolování. Zadejte uživatele pomocí svého uživatelského jména MySQL.
    ```azurecli-interactive
    az mysql server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech auditu](concepts-audit-logs.md) v Azure Database for MySQL
- Naučte se konfigurovat protokoly auditu v [Azure Portal](howto-configure-audit-logs-portal.md)