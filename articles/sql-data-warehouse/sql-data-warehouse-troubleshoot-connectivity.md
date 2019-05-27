---
title: Řešení potíží s Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Řešení potíží s Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: ddae39b7a2c76b5bb3f7caff1c0d5a8165c9d93a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873832"
---
# <a name="troubleshooting-connectivity-issues"></a>Řešení potíží s připojením

Tento článek uvádí běžné postupy řešení potíží po připojení ke službě SQL Data Warehouse.
- [Kontrola dostupnosti služby](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Zkontrolujte operaci pozastaven nebo škálování](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Zkontrolujte nastavení brány firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Zkontrolujte nastavení virtuální sítě nebo služby koncového bodu](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Vyhledat nejnovější ovladače](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Zkontrolujte připojovací řetězec](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problémy s přerušovaným připojením](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Běžné chybové zprávy](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Kontrola dostupnosti služby

Zkontrolujte, jestli je služba k dispozici. Na webu Azure Portal přejděte do SQL data warehouse, kterou se pokoušíte připojit. Na levém panelu obsahu, klikněte na **diagnostikovat a řešit problémy**.

![Vyberte Resource health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Tady se zobrazí stav služby SQL data warehouse. Pokud služba není zobrazen jako **dostupné**, zkontrolujte další kroky.

![Služba k dispozici](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Pokud vaše služba Resource health ukazuje, že je váš datový sklad pozastavený nebo škálování, postupujte podle pokynů, které chcete obnovit váš datový sklad.

![Služba byla pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Další informace o službě Resource Health najdete tady.

## <a name="check-for-paused-or-scaling-operation"></a>Zkontrolujte operaci pozastaven nebo škálování

Zkontrolujte portálu, abyste zjistili, zda služby SQL data warehouse je pozastaven nebo škálování.

![Služba byla pozastavena](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Pokud uvidíte, že vaše služba je pozastavena nebo škálování, zkontrolujte, že se že nejedná během vašeho plánu údržby. Na portálu pro služby SQL data warehouse *přehled*, zobrazí se vám plán údržby. vybrané.

![Přehled plánu údržby](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Jinak obraťte se na správce IT chcete ověřit, že tato Údržba není naplánované události. Pokud chcete obnovit na SQL data warehouse, postupujte podle kroků uvedených [tady](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Zkontrolujte nastavení brány firewall

SQL Data Warehouse komunikuje přes port 1433.   Pokud se pokoušíte připojit z podnikové sítě, nemusí odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. V takovém případě nemůžete připojit, k vašemu serveru Azure SQL Database dokud vaše IT oddělení neotevře port 1433. Další informace o konfiguraci brány firewall najdete [tady](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Zkontrolujte nastavení virtuální sítě nebo služby koncového bodu

Pokud se zobrazuje 40914 chyby a 40615, přečtěte si téma [popis chyby a řešení tady](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Vyhledat nejnovější ovladače

### <a name="software"></a>Software

Zkontrolujte, že používáte nejnovější nástroje potřebné pro připojení k SQL data warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Ovladače

Zkontrolujte, že používáte nejnovější verze ovladače.  Ve starší verzi ovladače by mohlo způsobit neočekávané chování, protože starší ovladače nemusí podporovat nové funkce.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Zkontrolujte připojovací řetězec

Zkontrolujte, abyste měli jistotu, že připojovací řetězce jsou nastavené správně.  Níže jsou uvedeny některé ukázky.  Můžete najít další informace o celém [připojovací řetězce zde](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Připojovací řetězec pro ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Řetězec připojení ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP připojovací řetězec

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Připojovací řetězec JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problémy s přerušovaným připojením

Zaškrtněte, pokud chcete zobrazit, pokud se zobrazuje velké zatížení na serveru vysoký počet požadavků zařazených do fronty. Budete muset vertikálně navýšit kapacitu datového skladu pro další zdroje informací.

## <a name="common-error-messages"></a>Běžné chybové zprávy

Chyby 40914 a 40615, najdete v článku [popis chyby a řešení tady](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>Stále máte problémy s připojením?
Vytvoření [lístek podpory](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) tak technický tým je může podporovat.
