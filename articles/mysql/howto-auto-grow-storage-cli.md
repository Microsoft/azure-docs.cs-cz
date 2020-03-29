---
title: Automatické úložiště růstu – Azure CLI – databáze Azure pro MySQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšit úložiště pomocí Azure CLI v Azure Database for MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062638"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Automatické prodnosí azure databáze pro úložiště MySQL pomocí azure CLI
Tento článek popisuje, jak můžete nakonfigurovat Azure Database pro úložiště serveru MySQL růst bez dopadu na zatížení.

Server, [který dosáhne limitu úložiště](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit), je nastaven na jen pro čtení. Pokud úložiště auto růst je povolena pak pro servery s méně než 100 GB zřízeného úložiště, zřízené úložiště se zvýší o 5 GB, jakmile je volné úložiště je nižší než 1 GB nebo 10 % zřízeného úložiště. U serverů s více než 100 GB zřízeného úložiště se zřízené úložiště zvýší o 5 %, když je volný úložný prostor nižší než 5 % zřízené velikosti úložiště. Platí maximální limity úložiště, jak [je uvedeno zde.](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru MySQL

Povolte úložiště s automatickým rozrůstatím serveru na existujícím serveru pomocí následujícího příkazu:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Povolte úložiště s automatickým rozrůstatím serveru při vytváření nového serveru pomocí následujícího příkazu:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Další kroky

Přečtěte [si, jak vytvářet upozornění na metriky](howto-alert-on-metric.md).