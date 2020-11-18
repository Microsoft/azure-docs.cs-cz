---
title: Připojení k synapse SQL
description: Připojte se k synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 0897ca35dd5ae1b7209a36aee4bd5335d4ea4d25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681527"
---
# <a name="connect-to-synapse-sql"></a>Připojení k synapse SQL
Připojte se k funkci synapse SQL ve službě Azure synapse Analytics.

## <a name="supported-tools-for-serverless-sql-pool-preview"></a>Podporované nástroje pro fond SQL bez serveru (Preview)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) se plně podporuje od verze 1.18.0. SSMS je částečně podporovaná od verze 18,5, můžete ji použít jenom pro připojení a dotazování.

> [!NOTE]
> Pokud má přihlášení AAD otevřené připojení po dobu delší než 1 hodinu, dotaz, který se spoléhá na AAD, selže. To zahrnuje dotazování na úložiště pomocí předávacího a příkazového příkazu AAD, které komunikují s AAD (třeba vytvoření externího poskytovatele). To má vliv na všechny nástroje, které udržují připojení otevřené, jako v editoru dotazů v SSMS a REKLAMách. Nástroje, které otevírají nová připojení k provedení dotazu, jako je synapse Studio, to neovlivní.

> Pokud chcete tento problém zmírnit, můžete restartovat SSMS nebo připojení a odpojení v REKLAMách. 

## <a name="find-your-server-name"></a>Vyhledání názvu serveru

Název serveru pro vyhrazený fond SQL v následujícím příkladu: showdemoweu.sql.azuresynapse.net.
Název serveru pro fond SQL bez serveru v následujícím příkladu: showdemoweu-ondemand.sql.azuresynapse.net.

Plně kvalifikovaný název serveru zjistíte následujícím způsobem:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte v **pracovních prostorech synapse**.
3. Vyberte pracovní prostor, ke kterému se chcete připojit.
4. Přejít na přehled
5. Vyhledejte úplný název serveru.

## <a name="sql-pool"></a>**Fond SQL**

![Úplný název serveru](./media/connect-overview/server-connect-example.png)

## <a name="serverless-sql-pool"></a>**Bezserverový fond SQL**

![Úplný fond SQL bez názvového serveru](./media/connect-overview/server-connect-example-sqlod.png)

## <a name="supported-drivers-and-connection-strings"></a>Podporované ovladače a připojovací řetězce
Synapse SQL podporuje [ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [php](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396)a [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Pokud chcete najít nejnovější verzi a dokumentaci, vyberte jeden z předchozích ovladačů. Chcete-li automaticky vygenerovat připojovací řetězec pro ovladač, který používáte z Azure Portal, vyberte v předchozím příkladu **Zobrazit databázové připojovací řetězce** . Následuje několik příkladů toho, jak připojovací řetězce vypadají pro jednotlivé ovladače.

> [!NOTE]
> Zvažte nastavení časového limitu připojení na 300 sekund, pokud chcete, aby vaše připojení přestálo krátká období nedostupnosti.

### <a name="adonet-connection-string-example"></a>Příklad připojovacího řetězce pro ADO.NET

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Příklad připojovacího řetězce pro ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Příklad připojovacího řetězce pro PHP

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Příklad připojovacího řetězce pro JDBC

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Nastavení připojení
Synapse SQL v rámci připojení a vytváření objektů standardizace některá nastavení. Tato nastavení nelze přepsat a zahrnout:

| Nastavení databáze | Hodnota |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |ON |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |7 |

## <a name="recommendations"></a>Doporučení

Pro spouštění dotazů na **fond SQL bez serveru** jsou doporučené nástroje [Azure Data Studio](get-started-azure-data-studio.md) a Azure synapse Studio.

## <a name="next-steps"></a>Další kroky
Informace o připojení a dotazování pomocí sady Visual Studio najdete v oddílu [Dotazování pomocí sady Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Další informace o možnostech ověřování najdete v tématu [ověřování pro synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).