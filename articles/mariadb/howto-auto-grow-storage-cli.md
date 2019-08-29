---
title: Automatické zvětšování Azure Database for MariaDBho úložiště pomocí Azure CLI
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pomocí rozhraní příkazového řádku Azure v Azure Database for MariaDB.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: 1024d5e2d4be8e5bb7c1371ce049a4a9e94bd603
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143424"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Automatické zvětšování Azure Database for MariaDBho úložiště pomocí Azure CLI
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MariaDB pro růst, aniž by to ovlivnilo zatížení.

Server, který [dosáhl limitu úložiště](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit), je nastaven na hodnotu jen pro čtení. Pokud je pro servery s méně než 100 GB zřízené úložiště povolené automatické zvětšování úložiště, velikost zřízeného úložiště se zvýší o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](https://docs.microsoft.com/azure/mariadb/concepts-pricing-tiers#storage) , platí.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="enable-mariadb-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru MariaDB

Pomocí následujícího příkazu Povolte automatické zvětšení úložiště na stávajícím serveru:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Při vytváření nového serveru pomocí následujícího příkazu Povolte automatické zvětšování úložiště serveru:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 10.3
```

## <a name="next-steps"></a>Další postup

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-metric.md).