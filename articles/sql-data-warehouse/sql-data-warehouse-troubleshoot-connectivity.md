---
title: Řešení potíží s Azure SQL Data Warehouse | Microsoft Docs
description: Řešení potíží s Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ebdeaf21253e89a9a14e3a56ca7be0f6e8adceb0
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70859226"
---
# <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

V tomto článku jsou uvedené běžné techniky řešení potíží, které se připojují k vašemu SQL Data Warehouse.
- [Ověřit dostupnost služby](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Kontrolovat pozastavené operace nebo škálování](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Kontrolovat nastavení brány firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Ověřte nastavení služby VNet/Service Endpoint.](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Vyhledat nejnovější ovladače](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Ověřte připojovací řetězec](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Přerušované problémy s připojením](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Běžné chybové zprávy](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Ověřit dostupnost služby

Zkontrolujte, zda je služba k dispozici. V Azure Portal přejdete do SQL Data Warehouse, ke kterému se pokoušíte připojit. Na levém panelu obsahu klikněte na diagnostikovat **a řešte problémy**.

![Vybrat stav prostředku](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Tady se zobrazí stav SQL Data Warehouse. Pokud služba není zobrazená jako **dostupná**, Projděte si další kroky.

![Služba k dispozici](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Pokud se ve stavu prostředku ukáže, že je váš datový sklad pozastaven nebo se mění jeho velikost, postupujte podle pokynů pro obnovení datového skladu.

![Služba pozastavila](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Další informace o Resource Health najdete tady.

## <a name="check-for-paused-or-scaling-operation"></a>Kontrolovat pozastavené operace nebo škálování

Podívejte se na portál a zjistěte, jestli je vaše SQL Data Warehouse pozastavené nebo škálovatelné.

![Služba byla pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Pokud vidíte, že vaše služba je pozastavená nebo je škálovatelná, zkontrolujte, že se během plánu údržby nezobrazuje. Na portálu pro SQL Data Warehouse *Přehled*uvidíte vybraný plán údržby.

![Přehled plánu údržby](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

V opačném případě obraťte se na správce IT a ověřte, že tato údržba není naplánovaná událost. Pokud chcete SQL Data Warehouse obnovit, postupujte podle kroků uvedených [tady](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Kontrolovat nastavení brány firewall

SQL Data Warehouse komunikuje přes port 1433.   Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. V takovém případě se k serveru Azure SQL Database nemůžete připojit, dokud vaše IT oddělení neotevře port 1433. Další informace o konfiguracích brány firewall najdete [tady](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>Ověřte nastavení služby VNet/Service Endpoint.

Pokud dochází k chybám 40914 a 40615, přečtěte si [Popis chyby a rozlišení tady](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vyhledat nejnovější ovladače

### <a name="software"></a>Software

Ujistěte se, že používáte nejnovější nástroje pro připojení k SQL Data Warehouse:

* SSMS
* Azure Data Studio
* Nástroje pro SQL Server data (Visual Studio)

### <a name="drivers"></a>Ovladače

Ujistěte se, že používáte nejnovější verze ovladačů.  Při použití starší verze ovladačů by mohlo dojít k neočekávanému chování, protože starší ovladače nemusí podporovat nové funkce.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Ověřte připojovací řetězec

Zkontrolujte, jestli jsou správně nastavené připojovací řetězce.  Níže jsou uvedeny některé ukázky.  Další informace o připojovacích řetězcích můžete najít [tady](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

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

## <a name="intermittent-connection-issues"></a>Přerušované problémy s připojením

Zkontrolujte, jestli u serveru dochází k velkým zátěži s vysokým počtem požadavků zařazených do fronty. Možná budete muset škálovat datový sklad pro další prostředky.

## <a name="common-error-messages"></a>Běžné chybové zprávy

Chyby 40914 a 40615, viz [Popis chyby a řešení zde](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Pořád máte problémy s připojením?
Vytvořte [lístek podpory](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) , aby vám technický tým mohl podpořit.
