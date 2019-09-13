---
title: Zdroje dat podporované v Azure Analysis Services | Microsoft Docs
description: Popisuje zdroje dat podporované pro datové modely v Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 096f8b3aa6ae66e65bbbd9ea6e2204af619199dd
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899417"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Zdroje dat podporované v Azure Analysis Services

Zdroje dat a konektory zobrazené v průvodci získat data nebo importovat v aplikaci Visual Studio jsou zobrazeny pro Azure Analysis Services i SQL Server Analysis Services. V Azure Analysis Services ale nejsou podporované všechny zdroje dat a konektory. Typy zdrojů dat, ke kterým se můžete připojit, závisí na mnoha faktorech, například na úrovni kompatibility modelů, dostupných datových konektorech, typu ověřování, poskytovatelích a podpoře místních datových bran. 

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Ano      |    Ano      |
|Azure SQL Data Warehouse     |   Ano      |   Ano       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ano       |    Ne      |
|Table Storage Azure<sup>[1](#tab1400a)</sup>    |   Ano       |    Ne      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ano        |  Ne        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>; <sup>[4](#gen2)</sup>      |   Ano       |    Ne      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ano     |   Ne       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Ano       |   Ne       |
||||

<a name="tab1400a">1</a> -tabelární 1400 a vyšší modely.   
<a name="azsqlmanaged">2</a> Azure SQL Database spravovaná instance je podporovaná. Vzhledem k tomu, že se spravovaná instance spouští v rámci virtuální sítě Azure s privátní IP adresou, musí být v instanci povolený veřejný koncový bod. Pokud není povolená, vyžaduje se místní brána dat.    
<a name="databricks">3</a> – Azure Databricks používání konektoru Sparku se v tuto chvíli nepodporuje.   
<a name="gen2">4</a> adls Gen2 v tuto chvíli není podporovaná.


**Zprostředkovatele**   
Modely v paměti a DirectQuery, které se připojují ke zdrojům dat Azure, používají .NET Framework Zprostředkovatel dat SQL Server.

## <a name="on-premises-data-sources"></a>Místní zdroje dat

Připojení k místním zdrojům dat z a Azure jako server vyžaduje místní bránu. Při použití brány se vyžadují 64 zprostředkovatelé.

### <a name="in-memory-and-directquery"></a>V paměti a DirectQuery

|Zdroj dat | Zprostředkovatel v paměti | Zprostředkovatel DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, Microsoft OLE DB Provider for SQL Server .NET Framework Zprostředkovatel dat pro SQL Server | .NET Framework Zprostředkovatel dat SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11,0, Microsoft OLE DB Provider for SQL Server .NET Framework Zprostředkovatel dat pro SQL Server | .NET Framework Zprostředkovatel dat SQL Server |
| Oracle | Poskytovatel OLE DB pro Oracle, Oracle Zprostředkovatel dat pro .NET |Oracle Zprostředkovatel dat pro .NET |
| Teradata |Zprostředkovatel OLE DB pro Teradata, Teradata Zprostředkovatel dat pro .NET |Zprostředkovatel dat Teradata pro .NET |
| | | |

### <a name="in-memory-only"></a>Pouze v paměti

|Zdroj dat  |  
|---------|
|Přístup k databázi     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Systém Analytics Platform System     |  
|Soubor CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excelový sešit     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Složka<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (beta verze) |
|Dokument JSON<sup>[1](#tab1400b)</sup>     |  
|Řádky z binárního souboru<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|Kanál OData<sup>[1](#tab1400b)</sup>     |  
|Dotaz ODBC     | 
|OLE DB     |   
|PostgreSQL databázi<sup>[1](#tab1400b)</sup>    | 
|Objekty Salesforce<sup>[1](#tab1400b)</sup> |  
|Sestavy Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePointový seznam<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Databáze Sybase     |  
|Soubor TXT  |
|Tabulka XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> -tabelární 1400 a vyšší modely.   
<a name="filesSP">2</a> – soubory v místní službě SharePoint nejsou podporovány.

## <a name="specifying-a-different-provider"></a>Určení jiného zprostředkovatele

Datové modely v Azure Analysis Services mohou vyžadovat různé zprostředkovatele dat při připojování k určitým zdrojům dat. V některých případech mohou být v tabulkových modelech připojujících se ke zdrojům dat pomocí nativních zprostředkovatelů, jako je například SQL Server Native Client (SQLNCLI11), vrácena chyba. Pokud používáte jiné nativní zprostředkovatele než SQLOLEDB, může se zobrazit chybová zpráva: **Zprostředkovatel SQLNCLI 11.1 není zaregistrován**. Nebo, pokud máte model DirectQuery připojující se k místním zdrojům dat a používáte nativní zprostředkovatele, může se zobrazit chybová zpráva: **Při vytváření sady řádků OLE DB došlo k chybě. Nesprávná syntaxe u LIMITu**

Při migraci místního SQL Server Analysis Services tabulkového modelu do Azure Analysis Services může být nutné změnit poskytovatele.

**Určení poskytovatele**

1. V SSDT >**datové zdroje** **Průzkumníka** > tabulkových modelů klikněte pravým tlačítkem na připojení ke zdroji dat a pak klikněte na **Upravit zdroj dat**.
2. V části **Upravit připojení**kliknutím na **Upřesnit** otevřete okno Vlastnosti pro upřesnění.
3. V části **nastavit pokročilé** > **poskytovatele**vlastností vyberte příslušného poskytovatele.

## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat jiný účet zosobnění. Účet zosobnění lze zadat v aplikaci Visual Studio (SSDT) nebo v SSMS.

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.
* Pokud používáte ověřování systému Windows, nastavte uživatele nebo heslo systému Windows. Pro SQL Server se ověřování Windows s konkrétním účtem zosobnění podporuje jenom pro datové modely v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, měl by se jednat o zosobnění účtu služby.

## <a name="next-steps"></a>Další kroky
[Místní brána](analysis-services-gateway.md)   
[Správa serveru](analysis-services-manage.md)   

