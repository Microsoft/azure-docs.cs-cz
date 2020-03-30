---
title: Zdroje dat podporované ve službě Azure Analysis Services | Dokumenty společnosti Microsoft
description: Popisuje zdroje dat a konektory podporované pro tabulkové 1200 a vyšší datové modely ve službě Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461653"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Podporované zdroje dat ve službě Azure Analysis Services

Zdroje dat a konektory zobrazené v Průvodci získáním dat nebo importem tabulky v sadě Visual Studio s projekty Analysis Services se zobrazují pro služby Azure Analysis Services i SQL Server Analysis Services. Ve službě Azure Analysis Services však nejsou podporovány všechny zobrazené zdroje dat a konektory. Typy zdrojů dat, ke kterému se můžete připojit, závisí na mnoha faktorech, jako je úroveň kompatibility modelu, dostupné datové konektory, typ ověřování a podpora místních datových bran. Následující tabulky popisují podporované zdroje dat pro Službu Azure Analysis Services.

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |Poznámky |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ano      |    Ano      |<sup>[2,](#azprovider)</sup> <sup> [3.](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (DATOVÝ SKLAD SQL)      |   Ano      |   Ano       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Ano       |    Ne      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Ano       |    Ne      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ano        |  Ne        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ano       |    Ne      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ano       |    Ne      |<sup>[1,](#tab1400a)</sup> <sup> [5.](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ano     |   Ne       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ano       |   Ne       |<sup>[1,](#tab1400a)</sup> <sup> [4.](#databricks)</sup>|
||||

**Poznámky:**   
<a name="tab1400a">1</a> - Pouze tabulkové modely 1400 a vyšší.  
<a name="azprovider">2</a> – Pokud je zadán jako zdroj dat *zprostředkovatele* v tabulkových modelech 1200 a vyšších, modely v paměti i DirectQuery vyžadují ovladač Microsoft OLE DB driver pro SQL Server MSOLEDBSQL (doporučeno), nativníklient SQL Server 11.0 nebo zprostředkovatel dat rozhraní .NET Framework pro SQL Server.    
<a name="azsqlmanaged">3</a> – Podporovaná je podporovaná instance Azure SQL Database Managed Instance. Vzhledem k tomu, že spravovaná instance běží v rámci virtuální sítě Azure s privátní IP adresou, musí být veřejný koncový bod povolen na instanci. Pokud není povolena, je vyžadována [místní brána dat.](analysis-services-gateway.md)    
<a name="databricks">4</a> – Azure Databricks pomocí konektoru Spark není momentálně podporována.   
<a name="gen2">5</a> - Konektor ADLS Gen2 není aktuálně podporován, ale konektor azure blob storage lze použít se zdrojem dat ADLS Gen2.   

## <a name="other-data-sources"></a>Other data sources

|Zdroj dat | V paměti | DirectQuery |Poznámky   |
|  --- | --- | --- | --- |
|Databáze aplikace Access     |  Ano | Ne |  |
|Active Directory     |  Ano | Ne | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Ano | Ne |  |
|Systém analytických platforem     |  Ano | Ne |  |
|Soubor CSV  |Ano | Ne |  |
|Dynamics 365     |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Excelový sešit     |  Ano | Ne |  |
|Výměna      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Složka      |Ano | Ne | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ano | Ne |  |
|Dokument JSON      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Řádky z binárního      | Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Databáze MySQL     | Ano | Ne |  |
|Datový kanál OData      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Dotaz ODBC     | Ano | Ne |  |
|OLE DB     |   Ano | Ne |  |
|Oracle  | Ano  |Ano  | <sup>[9](#oracle)</sup> |
|Databáze PostgreSQL   | Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Objekty Salesforce|  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Sestavy Salesforce |Ano | Ne | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Ano | Ne |  |
|SAP Business Warehouse    |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Sharepointový seznam      |   Ano | Ne | <sup>[6](#tab1400b)</sup>, <sup> [11.](#filesSP)</sup> |
|SQL Server |Ano   | Ano  | <sup>[7](#sqlim)</sup>, <sup> [8.](#instgw)</sup> | 
|Datový sklad serveru SQL Server |Ano   | Ano  | <sup>[7](#sqlim)</sup>, <sup> [8.](#instgw)</sup> |
|Databáze Sybase     |  Ano | Ne |  |
|Teradata | Ano  | Ano  | <sup>[10](#teradata)</sup> |
|Soubor TXT  |Ano | Ne |  |
|Tabulka XML    |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
| | | |

**Poznámky:**   
<a name="tab1400b">6</a> - Pouze tabulkové modely 1400 a vyšší.  
<a name="sqlim">7</a> - Pokud je zadán jako zdroj dat *zprostředkovatele* v tabulkových modelech 1200 a vyšších, zadejte ovladač Microsoft OLE DB driver pro SQL Server MSOLEDBSQL (doporučeno), NAtivní klient sql server 11.0 nebo zprostředkovatel dat rozhraní .NET Framework pro SQL Server.  
<a name="instgw">8</a> – Pokud zadejte MSOLEDBSQL jako zprostředkovatele dat, může být nutné stáhnout a nainstalovat [ovladač Microsoft OLE DB pro SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) do stejného počítače jako místní brána dat.  
<a name="oracle">9</a> – Pro tabulkové modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkových modelech 1400+ zadejte zprostředkovatele dat Oracle pro rozhraní .NET.  
<a name="teradata">10</a> - Pro tabulkové modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkových modelech 1400+ zadejte Teradata Data Provider pro .NET.   
<a name="filesSP">11</a> – Soubory v místním SharePointu nejsou podporované.

Připojení k místním zdrojům dat ze serveru Azure Analysis Services vyžaduje [místní bránu](analysis-services-gateway.md). Při použití brány jsou vyžadováni 64bitové zprostředkovatele. 

## <a name="understanding-providers"></a>Principy poskytovatelů

Při vytváření tabulkových 1400 a vyšší ch odvětvových projektů v sadě Visual Studio ve výchozím nastavení nezadáte zprostředkovatele dat při připojování ke zdroji dat pomocí **funkce Získat data**. Tabulkové modely 1400 a vyšší používají konektory [Power Query](/power-query/power-query-what-is-power-query) ke správě připojení, datových dotazů a mashupů mezi zdrojem dat a službou Analysis Services. Ty jsou někdy *označovány jako připojení strukturovaného* zdroje dat v nastavení vlastností připojení jsou nastaveny pro vás. Můžete však povolit starší zdroje dat. Pokud je tato možnost povolena, můžete se pomocí **Průvodce importem tabulky** připojit k určitým zdrojům dat, které jsou tradičně podporovány v tabulkových modelech 1200 a nižších modelech jako *starší verze*nebo zdroje dat *zprostředkovatele.* Pokud je zadán jako zdroj dat zprostředkovatele, můžete zadat konkrétního zprostředkovatele dat a další rozšířené vlastnosti připojení. Můžete se například připojit k místnímu datovému skladu SQL Serveru nebo dokonce k azure sql databázi jako starší zdroj dat. Potom můžete vybrat ovladač OLE DB pro zprostředkovatele dat SQL Server MSOLEDBSQL. V takovém případě může výběr zprostředkovatele dat technologie OLE DB poskytnout lepší výkon přes konektor Power Query. 

Při použití Průvodce importem tabulky v sadě Visual Studio vyžadují připojení k libovolnému zdroji dat zprostředkovatele dat. Je pro vás vybrán výchozí poskytovatel dat. V případě potřeby můžete změnit zprostředkovatele dat. Typ poskytovatele, který zvolíte, může záviset na výkonu, bez ohledu na to, zda model používá úložiště v paměti nebo DirectQuery a na kterou platformu Analysis Services nasadíte model.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Určení zdrojů dat zprostředkovatele v tabulkových projektech 1400 a vyšších modelech

Chcete-li povolit zdroje dat zprostředkovatele, klikněte v sadě Visual Studio na **položku Tools** > **Options** > **Analysis Services Tabulkový** > **import dat**, vyberte **Povolit starší zdroje dat**.

![Povolení starších zdrojů dat](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

S povolenými staršími zdroji dat klikněte v **Průzkumníku tabulkových modelů**pravým tlačítkem myši na **položku** > Import zdrojů dat**ze zdroje dat (starší verze).**

![Starší zdroje dat v Průzkumníku tabulkových modelů](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Stejně jako u tabulkových projektů modelu 1200 se pomocí **Průvodce importem tabulky** připojte ke zdroji dat. Na stránce připojení klepněte na tlačítko **Upřesnit**. Zadejte zprostředkovatele dat a další nastavení připojení v části **Nastavit upřesňující vlastnosti**.

![Starší zdroje dat Rozšířené vlastnosti](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat jiný účet zosobnění. Účet zosobnění lze zadat v sadě Visual Studio nebo SSMS.

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, zosobnění by měl být účet služby.
* Pokud používáte ověřování systému Windows, nastavte uživatelské nebo heslo systému Windows. Pro SQL Server je ověřování systému Windows s určitým účtem zosobnění podporováno pouze pro datové modely v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, zosobnění by měl být účet služby.

## <a name="oauth-credentials"></a>Pověření OAuth

Pro tabulkové modely na úrovni kompatibility 1400 a vyšší pomocí režimu v paměti podporují Azure SQL Database, Azure Synapse Analytics (SQL Data Warehouse), Dynamics 365 a SharePoint List přihlašovací údaje OAuth. Azure Analysis Services spravuje aktualizace tokenů pro zdroje dat OAuth, aby se zabránilo časovým časům pro dlouhotrvající operace aktualizace. Chcete-li generovat platné tokeny, nastavte pověření pomocí SSMS.

Režim přímého dotazu není podporován přihlašovacími údaji OAuth.

## <a name="next-steps"></a>Další kroky
[Místní brána](analysis-services-gateway.md)   
[Správa serveru](analysis-services-manage.md)   

