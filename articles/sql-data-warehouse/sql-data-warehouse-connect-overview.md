---
title: Připojení k Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Připojení k Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: b2aff8379667191017830bf441ee101e773d2dfc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596166"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Připojení k Azure SQL Data Warehouse
Připojení k Azure SQL Data Warehouse.

## <a name="find-your-server-name"></a>Vyhledání názvu serveru
Název serveru v následujícím příkladu je samplesvr.database.windows.net. Plně kvalifikovaný název serveru zjistíte následujícím způsobem:

1. Přejděte na web [Azure Portal][Azure portal].
2. Klikněte na **Datové sklady SQL**.
3. Klikněte na datový sklad, ke kterému se chcete připojit.
4. Vyhledejte úplný název serveru.
   
    ![Úplný název serveru][1]

## <a name="supported-drivers-and-connection-strings"></a>Podporované ovladače a připojovací řetězce
Azure SQL Data Warehouse podporuje [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] a [JDBC][JDBC]. Najít na nejnovější verzi a dokumentace, klikněte na jednu z předchozí ovladače. Chcete-li automaticky generovat připojovací řetězec pro ovladač, který používáte z portálu Azure, klikněte na **zobrazit databázové připojovací řetězce** z předchozího příkladu. Následuje několik příkladů toho, jak připojovací řetězce vypadají pro jednotlivé ovladače.

> [!NOTE]
> Zvažte nastavení časového limitu připojení na 300 sekund, pokud chcete, aby vaše připojení přestálo krátká období nedostupnosti.
> 
> 

### <a name="adonet-connection-string-example"></a>Příklad připojovacího řetězce pro ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Příklad připojovacího řetězce pro ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Příklad připojovacího řetězce pro PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Příklad připojovacího řetězce pro JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Nastavení připojení
SQL Data Warehouse během připojování a vytváření objektů používá několik standardních nastavení. Tato nastavení nelze přepsat a zahrnují:

| Nastavení databáze | Hodnota |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ON |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ON |
| [DATEFORMAT][DATEFORMAT] |mdy |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Další postup
Informace o připojení a dotazování pomocí sady Visual Studio najdete v oddílu [Dotazování pomocí sady Visual Studio][Query with Visual Studio]. Další informace o možnostech ověřování najdete v oddílu [Ověřování do Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


