---
title: Generování připojovacího řetězce pomocí PowerShellu Azure Database for MariaDB
description: Tento článek poskytuje příklad Azure PowerShell pro vytvoření připojovacího řetězce pro připojení k Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 79109a7f39debfa14c8a11d9014685382151ab3b
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663719"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Jak vygenerovat připojovací řetězec Azure Database for MariaDB pomocí PowerShellu

Tento článek ukazuje, jak vygenerovat připojovací řetězec pro server Azure Database for MariaDB. Připojovací řetězec můžete použít pro připojení k Azure Database for MariaDB z mnoha různých aplikací.

## <a name="requirements"></a>Požadavky

Tento článek používá jako výchozí bod prostředky vytvořené v následující příručce:

* [Rychlý Start: vytvoření serveru Azure Database for MariaDB pomocí prostředí PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce

`Get-AzMariaDbConnectionString`Rutina slouží k vygenerování připojovacího řetězce pro připojení aplikací k Azure Database for MariaDB. Následující příklad vrátí připojovací řetězec pro klienta PHP z **mydemoserver**.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Přizpůsobení parametrů Azure Database for MariaDB serveru pomocí PowerShellu](howto-configure-server-parameters-using-powershell.md)
