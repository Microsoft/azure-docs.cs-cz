---
title: Poradce při potížích s připojením
description: Řešení potíží s připojením v SQL Analytics.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 689a2e549c2627c607b6549f164e55a73318f63e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350040"
---
# <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

V tomto článku jsou uvedeny běžné techniky řešení potíží týkající se připojení k databázi SQL Analytics.
- [Zkontrolujte dostupnost služby](sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Zkontrolujte pozastavené operace nebo operace škálování](sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Zkontrolujte nastavení brány firewall](sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Zkontrolujte nastavení virtuální sítě / koncového bodu služby](sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Vyhledejte nejnovější ovladače](sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Zkontrolujte připojovací řetězec](sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problémy s přerušovaným připojením](sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Běžné chybové zprávy](sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Zkontrolujte dostupnost služby

Zkontrolujte, zda je služba k dispozici. Na webu Azure Portal přejděte do databáze SQL Analytics, kterou se pokoušíte připojit. V levém panelu TOC klikněte na **Diagnostika a řešení problémů**.

![Vybrat stav zdroje](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Zde se zobrazí stav služby SQL Analytics. Pokud se služba nezobrazuje jako **Dostupná**, zkontrolujte další kroky.

![Služba k dispozici](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Pokud váš stav prostředků ukazuje, že vaše instance SQL Analytics je pozastavena nebo škálování, postupujte podle pokynů k obnovení instance.

![Pozastavená](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) služba Další informace o stavu prostředků naleznete zde.

## <a name="check-for-paused-or-scaling-operation"></a>Zkontrolujte pozastavené operace nebo operace škálování

Zkontrolujte portál a zjistěte, jestli je vaše instance SQL Analytics pozastavená nebo škálování.

![Služba pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Pokud zjistíte, že vaše služba je pozastavena nebo škálování, zkontrolujte, zda není během plánu údržby. Na portálu pro *přehled*sql analytics uvidíte zvolený plán údržby.

![Přehled plánu údržby](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

V opačném případě se obraťte na správce IT a ověřte, zda tato údržba není naplánovanou událostí. Chcete-li obnovit instanci SQL Analytics, postupujte podle [pokynů uvedených zde](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Zkontrolujte nastavení brány firewall

Databáze SQL Analytics komunikuje přes port 1433.Pokud se pokoušíte připojit z podnikové sítě, vaše brána firewall možná nepovoluje odchozí přenosy přes port 1433. V takovém případě se nebudete moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433. Další informace o konfiguracích brány firewall naleznete [zde](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Zkontrolujte nastavení virtuální sítě / koncového bodu služby

Pokud se zobrazují chyby 40914 a 40615, přečtěte [si popis chyby a řešení zde](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vyhledejte nejnovější ovladače

### <a name="software"></a>Software

Zkontrolujte, jestli používáte nejnovější nástroje pro připojení k databázi SQL Analytics:

* SSMS
* Azure Data Studio
* Datové nástroje serveru SQL Server (Visual Studio)

### <a name="drivers"></a>Ovladače

Zkontrolujte, zda používáte nejnovější verze ovladačů.Použití starší verze ovladačů může mít za následek neočekávané chování, protože starší ovladače nemusí podporovat nové funkce.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Zkontrolujte připojovací řetězec

Zkontrolujte, jestli jsou správně nastavené připojovací řetězce.  Níže jsou uvedeny některé vzorky.  Další informace o [připojovacích řetězcích najdete tady](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Připojovací řetězec pro ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Připojovací řetězec ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Připojovací řetězec PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Připojovací řetězec JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problémy s přerušovaným připojením

Zkontrolujte, jestli je server hodně zatížený a existuje na něm vysoký počet požadavků ve frontě. Možná budete muset vertikálně navýšit kapacitu instanci SQL Analytics pro další prostředky.

## <a name="common-error-messages"></a>Běžné chybové zprávy

Chyby 40914 a 40615 naleznete v [popisu chyby a řešení zde](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Stále máte problémy s připojením?
Vytvořte [lístek podpory,](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) aby vás technický tým mohl podpořit.
