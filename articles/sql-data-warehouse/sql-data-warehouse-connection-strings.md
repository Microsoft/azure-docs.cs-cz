---
title: Připojovací řetězce pro službu Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Připojovací řetězce pro službu SQL Data Warehouse
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 057e412857feb39069c55bb9905c83dcea7cbd4e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460365"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Připojovací řetězce pro službu Azure SQL Data Warehouse
Můžete připojit k SQL Data Warehouse pomocí několika různých aplikačních protokolů, jako [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP] [ PHP] a [JDBC][JDBC]. Níže je několik příkladů řetězců připojení pro každý protokol.  Na webu Azure portal můžete také použít k sestavení připojovacího řetězce.  Pokud chcete vytvořit váš připojovací řetězec pomocí webu Azure portal, přejděte do okna vaší databáze v části *Essentials* klikněte na *zobrazit databázové připojovací řetězce*.

## <a name="sample-adonet-connection-string"></a>Připojovací řetězec ADO.NET vzorku
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Ukázka rozhraní ODBC připojovací řetězec
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Ukázka PHP připojovací řetězec
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Připojovací řetězec JDBC vzorku
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Zvažte nastavení časového limitu připojení na 300 sekund, aby bylo možné povolit připojení přestálo krátká období nedostupnosti.
> 
> 

## <a name="next-steps"></a>Další postup
Pokud chcete začít dotazovat na váš datový sklad pomocí sady Visual Studio a dalších aplikací, přečtěte si téma [dotazování pomocí sady Visual Studio][Query with Visual Studio].

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
