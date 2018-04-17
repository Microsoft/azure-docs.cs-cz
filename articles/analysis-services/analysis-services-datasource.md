---
title: Zdroje dat podporované ve službě Azure Analysis Services | Microsoft Docs
description: Popisuje zdroje dat, které jsou podporovány pro modely dat v Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3b60a5b96d7b8a0c48aacc916b1ba933dcd83705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Zdroje dat podporované ve službě Azure Analysis Services

Zdroje dat a konektory zobrazí v načíst Data nebo Průvodce importem v sadě Visual Studio se zobrazí pro Azure Analysis Services a SQL Server Analysis Services. Ale ne všechny zdroje dat a konektory, zobrazí se podporují v Azure Analysis Services. Typy zdrojů dat, se můžete připojit k závisí na mnoha faktorech, jako model úroveň kompatibility, konektory dostupných dat, typ ověřování, zprostředkovatele a podpory brány místní data. 

## <a name="azure-data-sources"></a>Zdroje dat Azure

|Zdroj dat  |V paměti  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Ano      |    Ano      |
|Azure SQL Data Warehouse     |   Ano      |   Ano       |
|Úložiště objektů Blob v Azure *     |   Ano       |    Ne      |
|Azure Table Storage *    |   Ano       |    Ne      |
|Azure Cosmos DB *     |  Ano        |  Ne        |
|Azure Data Lake Store *     |   Ano       |    Ne      |
|Azure HDInsight HDFS *     |     Ano     |   Ne       |
|Azure HDInsight Spark *     |   Ano       |   Ne       |
||||

\* Tabulkové 1400 modely jenom.

**Zprostředkovatel**   
V paměti a modelech DirectQuery připojení ke zdrojům dat Azure pomocí zprostředkovatele dat .NET Framework pro SQL Server.

## <a name="on-premises-data-sources"></a>Místní zdroje dat

Připojení k místní, že zdroje dat z a server Azure AS vyžadují místní bránu. Při použití brány, jsou požadovány 64bitová verze zprostředkovatele.

### <a name="in-memory-and-directquery"></a>V paměti a DirectQuery

|Zdroj dat | Zprostředkovatel v paměti | DirectQuery zprostředkovatele |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 zprostředkovatel Microsoft OLE DB pro SQL Server, zprostředkovatel dat .NET Framework pro SQL Server | Zprostředkovatel dat .NET framework pro SQL Server |
| SQL Server datového skladu |SQL Server Native Client 11.0 zprostředkovatel Microsoft OLE DB pro SQL Server, zprostředkovatel dat .NET Framework pro SQL Server | Zprostředkovatel dat .NET framework pro SQL Server |
| Oracle |Zprostředkovatel Microsoft OLE DB pro Oracle, poskytovatele dat Oracle pro .NET |Zprostředkovatel dat Oracle pro .NET | |
| Teradata |Zprostředkovatel OLE DB pro Teradata, Teradata Data Provider pro rozhraní .NET |Zprostředkovatel dat Teradata pro rozhraní .NET | |
| | | |

### <a name="in-memory-only"></a>V paměti pouze

|Zdroj dat  |  
|---------|---------|
|Databáze Access     |  
|Active Directory *     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Sešit aplikace Excel     |  
|Exchange*     |  
|Složky *     | 
|Dokument JSON *     |  
|Řádky z binárního souboru *     | 
|Databáze MySQL     | 
|Informační kanál OData *     |  
|Dotaz rozhraní ODBC     | 
|OLE DB     |   
|Databáze Postgre SQL *    | 
|SAP HANA *    |  
|SAP Business Warehouse *    |  
|SharePoint*     |   
|Databáze Sybase     |  
|XML tabulky *    |  
|||
 
\* Tabulkové 1400 modely jenom.

## <a name="specifying-a-different-provider"></a>Určení jiného zprostředkovatele

Modely dat v Azure Analysis Services může vyžadovat různé datové zprostředkovatele při připojování k určité zdroje dat. V některých případech může vrátit tabulkové modely služby připojení ke zdrojům dat pomocí nativního zprostředkovatele, jako je například SQL Server Native Client (SQLNCLI11) k chybě. Pokud používáte nativní zprostředkovatelé než SQLOLEDB, mohou se zobrazit chybová zpráva: **zprostředkovatele není registrovaný 'SQLNCLI11.1'**. Nebo, pokud je model DirectQuery připojení k místním zdrojům dat a používat nativní zprostředkovatele, mohou se zobrazit chybová zpráva: **Chyba při vytváření sady řádků OLE DB. Nesprávná syntaxe poblíž textu 'LIMIT'**.

Při migraci místní SQL Server Analysis Services tabulkový model Azure Analysis Services, může být nutné změnit zprostředkovatele.

**Pokud chcete zadat zprostředkovatele**

1. V sadě SSDT > **tabulkový Model Explorer** > **zdroje dat**, klikněte pravým tlačítkem na připojení ke zdroji dat a pak klikněte na tlačítko **upravit zdroj dat**.
2. V **upravit připojení**, klikněte na tlačítko **Upřesnit** a otevřete okno Vlastnosti zálohy.
3. V **nastavit rozšířené vlastnosti** > **zprostředkovatelé**, pak vyberte příslušného poskytovatele.

## <a name="impersonation"></a>Zosobnění
V některých případech může být nutné zadat účet jiné zosobnění. Účet zosobnění lze zadat v aplikaci Visual Studio (SSDT) nebo aplikace SSMS.

Pro místní zdroje dat:

* Pokud používáte ověřování SQL, musí být zosobnění účtu služby.
* Pokud používáte ověřování systému Windows, nastavte uživatele nebo heslo systému Windows. Ověřování systému Windows s konkrétní zosobnění účtu systému SQL Server, je podporováno pouze pro modely dat v paměti.

Pro cloudové zdroje dat:

* Pokud používáte ověřování SQL, musí být zosobnění účtu služby.

## <a name="next-steps"></a>Další postup
[Místní brány](analysis-services-gateway.md)   
[Správa serveru](analysis-services-manage.md)   

