---
title: Konfigurace protokolů auditu a protokolů pomalých dotazů pomocí Azure CLI-Azure Database for MySQL-flexibilního serveru
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům pomalým dotazům v Azure Database for MySQL flexibilním serveru z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: b42aba84dcbff795ee4ca1f8d622527e8039f27a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509058"
---
# <a name="configure-slow-query-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurace pomalých protokolů dotazů pro Azure Database for MySQL-flexibilní Server pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak nakonfigurovat [pomalé protokoly dotazů](concepts-slow-query-logs.md) pro flexibilní Server MySQL pomocí Azure CLI. 

## <a name="prerequisites"></a>Požadavky
- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Nainstalujte nebo upgradujte Azure CLI na nejnovější verzi. Viz Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).
-  Přihlaste se k účtu Azure pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) . Poznamenejte si vlastnost **ID** , která se vztahuje k **ID předplatného** pro váš účet Azure.

    ```azurecli-interactive
    az login
    ````

- Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém chcete vytvořit server pomocí ```az account set``` příkazu.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Vytvořte flexibilní Server MySQL, pokud jste ho ještě nevytvořili pomocí ```az mysql flexible-server create``` příkazu.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="configure-slow-query-logs"></a>Konfigurace pomalých protokolů dotazů

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokoly pomalých dotazů pro váš server.

```azurecli
# Turn on statement level log
az mysql flexible-server parameter set \
--name log_statement \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value all


# Set log_min_duration_statement time to 10 sec
# This setting will log all queries executing for more than 10 sec. Please adjust this threshold based on your definition for slow queries
az mysql server configuration set \
--name log_min_duration_statement \
--resource-group myresourcegroup \
--server mydemoserver \
--value 10000

# Enable Slow query logs
az mysql flexible-server parameter set \
--name slow_query_log \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON

```

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech pomalých dotazů](concepts-slow-query-logs.md)
