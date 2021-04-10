---
title: Automatické zvětšování úložiště – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak můžete povolit automatické zvětšování úložiště pomocí rozhraní příkazového řádku Azure v Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3126adbae6cb719bf19dc549e83dfc55af56f7d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110011"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Automatické zvětšování Azure Database for MySQLho úložiště pomocí Azure CLI
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for MySQL pro růst, aniž by to ovlivnilo zatížení.

Server, který [dosáhl limitu úložiště](./concepts-pricing-tiers.md#reaching-the-storage-limit), je nastaven na hodnotu jen pro čtení. Pokud je pro servery s méně než 100 GB zřízené úložiště povolené automatické zvětšování úložiště, velikost zřízeného úložiště se zvýší o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](./concepts-pricing-tiers.md#storage) , platí.

## <a name="prerequisites"></a>Požadavky

Postup dokončení této příručky:

- Potřebujete [server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="enable-mysql-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru MySQL

Pomocí následujícího příkazu Povolte automatické zvětšení úložiště na stávajícím serveru:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Při vytváření nového serveru pomocí následujícího příkazu Povolte automatické zvětšování úložiště serveru:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).