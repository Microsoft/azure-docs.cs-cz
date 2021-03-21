---
title: Přístup k protokolům auditu – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL z Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7ffabae785a589bacf349356079f6046039f9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541993"
---
# <a name="configure-and-access-audit-logs-in-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu v rozhraní příkazového řádku Azure

[Protokoly auditu Azure Database for MySQL](concepts-audit-logs.md) můžete nakonfigurovat z rozhraní příkazového řádku Azure CLI.

## <a name="prerequisites"></a>Předpoklady

Postup pro krokování této příručky:

- Potřebujete [server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
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
