---
title: Řešení potíží s připojením
description: Řešení potíží s připojením v synapse fondu SQL.
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
ms.openlocfilehash: d69c8dd28b946df3fff500c31c7cdefa4767c0c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408136"
---
# <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

V tomto článku jsou uvedené běžné techniky řešení potíží při připojování k databázi SQL Analytics.

## <a name="check-service-availability"></a>Ověřit dostupnost služby

Zkontrolujte, zda je služba k dispozici. V Azure Portal přejdete do synapse fondu SQL, ke kterému se pokoušíte připojit. Na levém panelu obsahu klikněte na **diagnostikovat a řešte problémy**.

![Vybrat stav prostředku](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Tady se zobrazí stav vašeho synapse fondu SQL. Pokud služba není zobrazená jako **dostupná**, Projděte si další kroky.

![Služba k dispozici](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Pokud se ve stavu prostředku ukáže, že vaše instance fondu SQL synapse je pozastavená nebo má velikost, postupujte podle pokynů a obnovte svou instanci.

![Služba pozastavila](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Další informace o Resource Health najdete tady.

## <a name="check-for-paused-or-scaling-operation"></a>Zkontrolujte pozastavené operace nebo operace škálování

Podívejte se na portál a zjistěte, jestli je vaše instance synapse fondu SQL pozastavená nebo škálovatelná.

![Služba byla pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Pokud vidíte, že vaše služba je pozastavená nebo je škálovatelná, zkontrolujte, že se během plánu údržby nezobrazuje. Na portálu synapse *vašeho fondu SQL se zobrazí*vybraný plán údržby.

![Přehled plánu údržby](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

V opačném případě obraťte se na správce IT a ověřte, že tato údržba není naplánovaná událost. Chcete-li obnovit instanci SQL Analytics, postupujte podle [těchto kroků](pause-and-resume-compute-portal.md).

## <a name="check-your-firewall-settings"></a>Zkontrolujte nastavení brány firewall

Databáze SQL Analytics komunikuje přes port 1433.Pokud se pokoušíte připojit z podnikové sítě, vaše brána firewall možná nepovoluje odchozí přenosy přes port 1433. V takovém případě se nebudete moct připojit k serveru služby Azure SQL Database, dokud vaše IT oddělení neotevře port 1433. Další informace o konfiguracích brány firewall najdete [tady](../../sql-database/sql-database-firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Zkontrolujte nastavení virtuální sítě / koncového bodu služby

Pokud dochází k chybám 40914 a 40615, přečtěte si [Popis chyby a rozlišení tady](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vyhledejte nejnovější ovladače

### <a name="software"></a>Software

Ujistěte se, že používáte nejnovější nástroje pro připojení ke synapse fondu SQL:

- SSMS
- Azure Data Studio
- Nástroje pro SQL Server data (Visual Studio)

### <a name="drivers"></a>Ovladače

Ujistěte se, že používáte nejnovější verze ovladačů.Při použití starší verze ovladačů by mohlo dojít k neočekávanému chování, protože starší ovladače nemusí podporovat nové funkce.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>Zkontrolujte připojovací řetězec

Zkontrolujte, jestli jsou správně nastavené připojovací řetězce.  Níže jsou uvedeny některé ukázky.  Další informace o [připojovacích řetězcích najdete tady](sql-data-warehouse-connection-strings.md).

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

Zkontrolujte, jestli je server hodně zatížený a existuje na něm vysoký počet požadavků ve frontě. Možná budete muset škálovat synapse fond SQL pro další prostředky.

## <a name="common-error-messages"></a>Běžné chybové zprávy

Chyby 40914 a 40615, viz [Popis chyby a řešení zde](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Pořád máte problémy s připojením?

Vytvořte [lístek podpory](sql-data-warehouse-get-started-create-support-ticket.md) , aby vám technický tým mohl podpořit.
