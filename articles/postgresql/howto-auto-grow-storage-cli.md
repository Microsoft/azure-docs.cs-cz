---
title: Automatické zvětšování úložiště – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete nakonfigurovat automatické zvětšování úložiště pomocí rozhraní příkazového řádku Azure CLI na Azure Database for PostgreSQL jednom serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 38a15136bb7bee1d37486ee02d5342506ed3f7d8
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228108"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Automatické zvětšování Azure Database for PostgreSQL Storage – jeden server pomocí Azure CLI
Tento článek popisuje, jak můžete nakonfigurovat úložiště serveru Azure Database for PostgreSQL pro růst, aniž by to ovlivnilo zatížení.

Server, který [dosáhl limitu úložiště](./concepts-pricing-tiers.md#reaching-the-storage-limit), je nastaven na hodnotu jen pro čtení. Pokud je pro servery s méně než 100 GB zřízené úložiště povolené automatické zvětšování úložiště, velikost zřízeného úložiště se zvýší o 5 GB, jakmile bude volné úložiště nižší než 1 GB nebo 10% zřízené úložiště. U serverů s více než 100 GB zřízeného úložiště se velikost zřízeného úložiště zvyšuje o 5%, pokud je volný prostor úložiště pod 5% velikosti zřízeného úložiště. Maximální limity úložiště, jak je uvedeno [zde](./concepts-pricing-tiers.md#storage) , platí.

## <a name="prerequisites"></a>Požadavky

- Potřebujete [server Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Povolit automatické zvětšování úložiště serveru PostgreSQL

Pomocí následujícího příkazu Povolte automatické zvětšení úložiště na stávajícím serveru:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Při vytváření nového serveru pomocí následujícího příkazu Povolte automatické zvětšování úložiště serveru:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [tom, jak vytvářet výstrahy na metrikách](howto-alert-on-metric.md).