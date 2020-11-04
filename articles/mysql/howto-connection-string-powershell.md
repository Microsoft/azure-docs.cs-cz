---
title: Generování připojovacího řetězce pomocí PowerShellu Azure Database for MySQL
description: Tento článek poskytuje příklad Azure PowerShell pro vytvoření připojovacího řetězce pro připojení k Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: fcba366a0322c3c1b5c6dcdf0fc3571646053fad
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337179"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Jak vygenerovat připojovací řetězec Azure Database for MySQL pomocí PowerShellu

Tento článek ukazuje, jak vygenerovat připojovací řetězec pro server Azure Database for MySQL. Připojovací řetězec můžete použít pro připojení k Azure Database for MySQL z mnoha různých aplikací.

## <a name="requirements"></a>Požadavky

Tento článek používá jako výchozí bod prostředky vytvořené v následující příručce:

* [Rychlý Start: vytvoření serveru Azure Database for MySQL pomocí prostředí PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce

`Get-AzMySqlConnectionString`Rutina slouží k vygenerování připojovacího řetězce pro připojení aplikací k Azure Database for MySQL. Následující příklad vrátí připojovací řetězec pro klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Přizpůsobení parametrů Azure Database for MySQL serveru pomocí PowerShellu](howto-configure-server-parameters-using-powershell.md)
