---
title: Konfigurace protokolů auditu pomocí Azure CLI-Azure Database for MySQL-flexibilního serveru
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k protokolům auditu v Azure Database for MySQL flexibilním serveru z Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 757d765f44f09eeb6f7a24644abeb1ce4577f664
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509046"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Konfigurace a přístup k protokolům auditu pro Azure Database for MySQL flexibilní Server pomocí Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto článku se dozvíte, jak nakonfigurovat [protokoly auditu](concepts-audit-logs.md) pro server MySQL Flexible pomocí Azure CLI.

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

## <a name="configure-audit-logging"></a>Konfigurace protokolování auditu

>[!IMPORTANT]
> Doporučujeme pouze protokolovat typy událostí a uživatele požadované pro vaše účely auditování, aby se zajistilo, že výkon serveru nebude velmi ovlivněn.

Povolte a nakonfigurujte protokolování auditu.

```azurecli
# Enable audit logs
az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>Další kroky
- Další informace o [protokolech auditu](concepts-audit-logs.md)
