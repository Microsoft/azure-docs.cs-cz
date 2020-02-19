---
title: Zdroje dat podporované v Azure Analysis Services | Microsoft Docs
description: Popisuje zdroje dat a konektory podporované tabulkami 1200 a vyššími datovými modely v Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f65d8fa2c2e522c718c637e32defc4c56fca8364
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461653"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Zdroje dat podporované v Azure Analysis Services

Zdroje dat a konektory zobrazené v průvodci získáním dat nebo importu tabulek v aplikaci Visual Studio s Analysis Services projekty jsou zobrazeny pro Azure Analysis Services i SQL Server Analysis Services. V Azure Analysis Services ale nejsou podporované všechny zdroje dat a konektory. Typy zdrojů dat, ke kterým se můžete připojit, závisí na mnoha faktorech, například na úrovni kompatibility modelů, dostupných datových konektorech, typu ověřování a místní podpoře datových bran. Následující tabulky popisují podporované zdroje dat pro Azure Analysis Services.

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |Poznámky: |
|---------|---------|---------|---------|
|Azure SQL Database      |   Ano      |    Ano      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure synapse Analytics (SQL Data Warehouse)      |   Ano      |   Ano       |<sup>[odst](#azprovider)</sup>|
|Azure Blob Storage      |   Ano       |    Ne      | <sup>[1](#tab1400a)</sup> |
|Úložiště tabulek v Azure     |   Ano       |    Ne      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ano        |  Ne        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ano       |    Ne      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ano       |    Ne      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|HDFS Azure HDInsight    |     Ano     |   Ne       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ano       |   Ne       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**Poznámky:**    
<a name="tab1400a">1</a> -tabelární 1400 a vyšší modely.  
<a name="azprovider">2</a> – Pokud jsou zadané jako zdroj dat *zprostředkovatele* v tabulkovém 1200 a vyšších modelech, v paměti i v modelech DIRECTQUERY OLE DB vyžaduje SQL Server MSOLEDBSQL (doporučeno), SQL Server Native Client 11,0 nebo .NET Framework Zprostředkovatel dat pro SQL Server.    
je podporována <a name="azsqlmanaged">3</a> -Azure SQL Database spravovaná instance. Vzhledem k tomu, že se spravovaná instance spouští v rámci virtuální sítě Azure s privátní IP adresou, musí být v instanci povolený veřejný koncový bod. Pokud není povolená, vyžaduje se [místní brána dat](analysis-services-gateway.md) .    
<a name="databricks">4</a> – Azure Databricks používání konektoru Sparku se v tuto chvíli nepodporuje.   
<a name="gen2">5</a> -adls Gen2 konektor se momentálně nepodporuje, ale konektor Azure Blob Storage můžete použít se zdrojem dat adls Gen2.   

## <a name="other-data-sources"></a>Další zdroje dat

|Zdroj dat | V paměti | DirectQuery |Poznámky:   |
|  --- | --- | --- | --- |
|Přístup k databázi     |  Ano | Ne |  |
|Active Directory     |  Ano | Ne | <sup>[6](#tab1400b)</sup>  |
|Služba Analysis Services     |  Ano | Ne |  |
|Systém Analytics Platform System     |  Ano | Ne |  |
|Soubor CSV  |Ano | Ne |  |
|Dynamics 365     |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Excelový sešit     |  Ano | Ne |  |
|Exchange      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Složka      |Ano | Ne | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ano | Ne |  |
|Dokument JSON      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Řádky z binárního souboru      | Ano | Ne | <sup>[6](#tab1400b)</sup> |
|MySQL Database     | Ano | Ne |  |
|Kanál OData      |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Dotaz ODBC     | Ano | Ne |  |
|OLE DB     |   Ano | Ne |  |
|Oracle  | Ano  |Ano  | <sup>[9](#oracle)</sup> |
|Databáze PostgreSQL   | Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Objekty Salesforce|  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|Sestavy Salesforce |Ano | Ne | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Ano | Ne |  |
|SAP Business Warehouse    |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
|SharePointový seznam      |   Ano | Ne | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|Server SQL |Ano   | Ano  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server Data Warehouse |Ano   | Ano  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Databáze Sybase     |  Ano | Ne |  |
|Teradata | Ano  | Ano  | <sup>[10pruhový](#teradata)</sup> |
|Soubor TXT  |Ano | Ne |  |
|Tabulka XML    |  Ano | Ne | <sup>[6](#tab1400b)</sup> |
| | | |

**Poznámky:**    
<a name="tab1400b">6</a> – tabulkové 1400 a vyšší modely.  
<a name="sqlim">7</a> – při zadání jako zdroje dat *zprostředkovatele* v tabulkových 1200 a vyšších modelech zadejte Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (doporučeno), SQL Server Native Client 11,0 nebo .NET Framework Zprostředkovatel dat pro SQL Server.  
<a name="instgw">8</a> – Pokud zadáte MSOLEDBSQL jako poskytovatele dat, může být nutné stáhnout a nainstalovat [ovladač Microsoft OLE DB pro SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) na stejném počítači jako místní brána dat.  
<a name="oracle">9</a> – pro tabelární modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkách 1400 s více modely určete Oracle zprostředkovatel dat pro .NET.  
<a name="teradata">10</a> – pro tabelární modely 1200 nebo jako zdroj dat *zprostředkovatele* v tabulkách 1400 s více modely určete zprostředkovatel dat Teradata pro .NET.   
<a name="filesSP">11</a> – soubory v místní službě SharePoint nejsou podporovány.

Připojení k místním zdrojům dat z Azure Analysis Services serveru vyžaduje místní [bránu](analysis-services-gateway.md). Při použití brány se vyžadují 64 zprostředkovatelé. 

## <a name="understanding-providers"></a>Principy zprostředkovatelů

Při vytváření tabelárních projektů 1400 a vyšších modelů v aplikaci Visual Studio ve výchozím nastavení neurčíte poskytovatele dat při připojování ke zdroji dat pomocí **získat data**. Tabelární 1400 a vyšší modely používají konektory [Power Query](/power-query/power-query-what-is-power-query) ke správě připojení, datových dotazů a hybridních webových aplikací mezi zdrojem dat a Analysis Services. Někdy se v nastaveních vlastností připojení označují jako *strukturovaná* připojení zdrojů dat. Můžete ale povolit starší zdroje dat. Pokud je tato možnost povolená, můžete použít **Průvodce importem tabulky** pro připojení k určitým zdrojům dat, které jsou tradičně podporované v tabulkovém 1200 a nižších modelech jako *starší verze*nebo zdroje dat *poskytovatele* . Pokud je zadaný jako zdroj dat zprostředkovatele, můžete zadat konkrétního poskytovatele dat a další rozšířené vlastnosti připojení. Můžete se například připojit k místnímu datovému skladu SQL Server nebo dokonce k Azure SQL Database jako k staršímu zdroji dat. Pak můžete vybrat ovladač OLE DB pro poskytovatele dat SQL Server MSOLEDBSQL. V takovém případě může poskytovatel dat OLE DB poskytovat lepší výkon prostřednictvím konektoru Power Query. 

Při použití Průvodce importem tabulky v aplikaci Visual Studio připojení k jakémukoli zdroji dat vyžaduje poskytovatele dat. Pro vás je vybraný výchozí zprostředkovatel dat. V případě potřeby můžete změnit zprostředkovatele dat. Typ poskytovatele, který zvolíte, může záviset na výkonu, bez ohledu na to, jestli model používá úložiště v paměti nebo DirectQuery a které Analysis Services platforma, do které model nasazujete.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Určení zdrojů dat zprostředkovatele v tabulkových projektech 1400 a vyšších

Chcete-li povolit zdroje dat poskytovatele, v aplikaci Visual Studio klikněte na **nástroje** > **Možnosti** > **Analysis Services tabelární** > **Import dat**, vyberte možnost **Povolit starší zdroje dat**.

![Povolit starší zdroje dat](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Pokud jsou povolené starší zdroje dat, v **Průzkumníkovi tabelárních modelů**klikněte pravým tlačítkem na **zdroje dat** > **Importovat ze zdroje dat (starší verze)** .

![Starší zdroje dat v Průzkumníkovi tabulkových modelů](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Stejně jako u tabulkových projektů tabelárních 1200 se pomocí **Průvodce importem tabulky** připojte ke zdroji dat. Na stránce připojit klikněte na **Upřesnit**. Zadejte poskytovatele dat a další nastavení připojení v **Nastavení Upřesnit vlastnosti**.

![Rozšířené vlastnosti zdrojů dat ve starší verzi](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat jiný účet zosobnění. Účet zosobnění lze zadat v aplikaci Visual Studio nebo SSMS.

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.
* Pokud používáte ověřování systému Windows, nastavte uživatele nebo heslo systému Windows. Pro SQL Server se ověřování Windows s konkrétním účtem zosobnění podporuje jenom pro datové modely v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.

## <a name="oauth-credentials"></a>Přihlašovací údaje OAuth

Pro tabelární modely na úrovni kompatibility 1400 a vyšší s použitím režimu v paměti, Azure SQL Database, Azure synapse Analytics (SQL Data Warehouse), Dynamics 365 a SharePointového seznamu podporují přihlašovací údaje OAuth. Azure Analysis Services spravuje aktualizace tokenu pro zdroje dat OAuth, aby nedocházelo k vypršení časových limitů pro dlouhotrvající operace aktualizace. Pokud chcete generovat platné tokeny, nastavte přihlašovací údaje pomocí SSMS.

Režim přímého dotazu není u přihlašovacích údajů OAuth podporován.

## <a name="next-steps"></a>Další kroky
[Místní brána](analysis-services-gateway.md)   
[Správa serveru](analysis-services-manage.md)   

