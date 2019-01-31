---
title: Zdroje dat podporované ve službě Azure Analysis Services | Dokumentace Microsoftu
description: Popisuje zdroje dat podporované pro datové modely ve službě Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 92c2d59ffe8c144bea6e7f8676880c866e234885
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299040"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Zdroje dat podporované ve službě Azure Analysis Services

Zdroje dat a konektory zobrazují v získat Data nebo Průvodce importem v sadě Visual Studio se zobrazí pro Azure Analysis Services a SQL Server Analysis Services. Ale ne všechny zdroje dat a konektory, zobrazí se podporují ve službě Azure Analysis Services. Typy zdrojů dat, které se můžete připojit k závisí na mnoha faktorech, jako model úroveň kompatibility, k dispozici datové konektory, typ ověřování, zprostředkovatele a podpora On-premises data gateway. 

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database<sup>[2](#azsqlmanaged)</sup>     |   Ano      |    Ano      |
|Azure SQL Data Warehouse     |   Ano      |   Ano       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ano       |    Ne      |
|Azure Table Storage<sup>[1](#tab1400a)</sup>    |   Ano       |    Ne      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ano        |  Ne        |
|Azure Data Lake Store<sup>[1](#tab1400a)</sup>     |   Ano       |    Ne      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ano     |   Ne       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup> [3](#databricks)</sup>     |   Ano       |   Ne       |
||||

<a name="tab1400a">1</a> pouze vyšší modely a model tabulkový 1400.   
<a name="azsqlmanaged">2</a> azure SQL Database Managed Instance je podporována. Proto spravované instance běží v rámci virtuální sítě Azure s použitím privátní IP adresy, není nutná On-premises Data Gateway.   
<a name="databricks">3</a> azure Databricks pomocí konektoru Spark v tuto chvíli nepodporuje.


**Zprostředkovatel**   
V paměti a modelech DirectQuery připojení ke zdrojům dat Azure pomocí zprostředkovatele dat .NET Framework pro SQL Server.

## <a name="on-premises-data-sources"></a>Místní zdroje dat

Připojení k místní zdroje dat z a serveru Azure AS vyžadují místní brány. Při použití brány, 64-bit poskytovatelé jsou povinné.

### <a name="in-memory-and-directquery"></a>In-memory a DirectQuery

|Zdroj dat | Zprostředkovatel v paměti | Poskytovatel DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, zprostředkovatel Microsoft OLE DB pro SQL Server, zprostředkovatele dat .NET Framework pro SQL Server | Zprostředkovatel dat .NET framework pro SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, zprostředkovatel Microsoft OLE DB pro SQL Server, zprostředkovatele dat .NET Framework pro SQL Server | Zprostředkovatel dat .NET framework pro SQL Server |
| Oracle |Zprostředkovatel Microsoft OLE DB pro Oracle, poskytovatel dat Oracle pro .NET |Poskytovatel dat Oracle pro .NET | |
| Teradata |Zprostředkovatel OLE DB pro Teradata, Teradata Data Provider for .NET |Poskytovatel Teradata dat pro .NET | |
| | | |

### <a name="in-memory-only"></a>V paměti pouze

|Zdroj dat  |  
|---------|---------|
|Databáze aplikace Access     |  
|Služby Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Systém Analytics Platform System     |  
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excelový sešit     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Složka<sup>[1](#tab1400b)</sup>     |
|Databáze IBM Informix<sup>[1](#tab1400b) </sup> (beta verze) |
|Dokument JSON<sup>[1](#tab1400b)</sup>     |  
|Řádky z binárního souboru<sup>[1](#tab1400b)</sup>     | 
|Databáze MySQL     | 
|Datový kanál OData<sup>[1](#tab1400b)</sup>     |  
|Dotaz rozhraní ODBC     | 
|OLE DB     |   
|Databáze Postgre SQL<sup>[1](#tab1400b)</sup>    | 
|Objekty Salesforce<sup>[1](#tab1400b)</sup> |  
|Sestavy služby Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint<sup>[1](#tab1400b)</sup>     |   
|Databáze Sybase     |  
|Tabulky XML<sup>[1](#tab1400b)</sup>    |  
|||
 
<a name="tab1400b">1</a> pouze vyšší modely a model tabulkový 1400.

## <a name="specifying-a-different-provider"></a>Zadání jiného poskytovatele

Modely dat ve službě Azure Analysis Services může vyžadovat různé datové zprostředkovatele při připojování k určité zdroje dat. V některých případech může tabulkových modelů připojení ke zdrojům dat pomocí nativní poskytovatelů, jako je například SQL Server Native Client (SQLNCLI11) vrátí chybu. Pokud používáte nativní poskytovatelů než SQLOLEDB, zobrazí se chybová zpráva: **'SQLNCLI11.1' není registrovaný poskytovatel**. Nebo, pokud máte modelu DirectQuery připojení k místním zdrojům dat a používají nativní zprostředkovatele, může se zobrazit chybová zpráva: **Chyba při vytváření sady řádků OLE DB. Nesprávná syntaxe poblíž textu "LIMIT"**.

Při migraci tabulkový model místní SQL Server Analysis Services do služby Azure Analysis Services, může být nutné změnit zprostředkovatele.

**Pokud chcete zadat zprostředkovatele**

1. V sadě SSDT > **Průzkumníku tabelárních modelů** > **zdroje dat**, klikněte pravým tlačítkem na připojení ke zdroji dat a potom klikněte na tlačítko **upravit zdroj dat**.
2. V **upravit připojení**, klikněte na tlačítko **Upřesnit** otevřete okno Vlastnosti zálohy.
3. V **nastavte rozšířené vlastnosti** > **poskytovatelé**, vyberte odpovídající zprostředkovatele.

## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat účet jiné zosobnění. Účet zosobnění se dá nastavit v sadě Visual Studio (SSDT) nebo aplikaci SSMS.

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, by měl být zosobnění účtu služby.
* Pokud používáte ověřování Windows, nastavte Windows uživatele a hesla. Pro SQL Server Windows ověřování pomocí zosobnění konkrétní účet je podporována pouze pro modely dat v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, by měl být zosobnění účtu služby.

## <a name="next-steps"></a>Další postup
[Místní brána](analysis-services-gateway.md)   
[Správa serveru](analysis-services-manage.md)   

