---
title: Generování připojovacího řetězce pomocí PowerShellu Azure Database for PostgreSQL
description: Tento článek poskytuje příklad Azure PowerShell pro vytvoření připojovacího řetězce pro připojení k Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708165"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Jak vygenerovat připojovací řetězec Azure Database for PostgreSQL pomocí PowerShellu

Tento článek ukazuje, jak vygenerovat připojovací řetězec pro server Azure Database for PostgreSQL. Připojovací řetězec můžete použít pro připojení k Azure Database for PostgreSQL z mnoha různých aplikací.

## <a name="requirements"></a>Požadavky

Tento článek používá jako výchozí bod prostředky vytvořené v následující příručce:

* [Rychlý Start: vytvoření serveru Azure Database for PostgreSQL pomocí prostředí PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce

`Get-AzPostgreSqlConnectionString`Rutina slouží k vygenerování připojovacího řetězce pro připojení aplikací k Azure Database for PostgreSQL. Následující příklad vrátí připojovací řetězec pro klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Platné hodnoty pro `Client` parametr zahrnují:

* ROZHRANÍ ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přizpůsobení parametrů Azure Database for PostgreSQL serveru pomocí PowerShellu](howto-configure-server-parameters-using-powershell.md)
