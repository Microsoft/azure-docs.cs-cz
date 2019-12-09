---
title: Transformace dat pomocí aktivity uložené procedury
description: Vysvětluje, jak pomocí aktivity SQL Server uložených procedur vyvolat uloženou proceduru v Azure SQL Database/datovém skladu z kanálu Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: 4a0709b4eaa8742069eecb4c39712e384645304b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926662"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformuje data pomocí aktivity SQL Server uložených procedur v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuální verze](transform-data-using-stored-procedure.md)

Aktivity transformace dat v [kanálu](concepts-pipelines-activities.md) Data Factory slouží k transformaci a zpracování nezpracovaných dat do předpovědi a přehledů. Aktivita uložená procedura je jednou z aktivit transformace, které Data Factory podporuje. Tento článek sestaví na článku s [transformačními daty](transform-data.md) , který prezentuje obecný přehled transformace dat a podporovaných transformačních aktivit v Data Factory.

> [!NOTE]
> Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si kurz: [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

Aktivitu uložené procedury můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat v podniku nebo na virtuálním počítači Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server databázi.  Pokud používáte SQL Server, nainstalujte modul runtime integrace v místním prostředí do stejného počítače, který je hostitelem databáze, nebo na samostatném počítači, který má přístup k databázi. Místní prostředí Integration runtime je komponenta, která propojuje zdroje dat místně nebo na virtuálním počítači Azure s Cloud Services zabezpečeným a spravovaným způsobem. Podrobnosti najdete v článku věnovaném místnímu [prostředí Integration runtime](create-self-hosted-integration-runtime.md) .

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server můžete nakonfigurovat **SqlSink** v aktivitě kopírování a vyvolat uloženou proceduru pomocí vlastnosti **sqlWriterStoredProcedureName** . Podrobnosti o této vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Vyvolání uložené procedury při kopírování dat do Azure SQL Data Warehouse pomocí aktivity kopírování není podporováno. Můžete ale použít aktivitu uložená procedura k vyvolání uložené procedury v SQL Data Warehouse. 
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse můžete nakonfigurovat **SqlSource** v aktivitě kopírování a vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze pomocí vlastnosti **sqlReaderStoredProcedureName** . Další informace najdete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md) [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Podrobnosti o syntaxi
Tady je formát JSON pro definování aktivity uložené procedury:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

Tyto vlastnosti JSON jsou popsány v následující tabulce:

| Vlastnost                  | Popis                              | Požaduje se |
| ------------------------- | ---------------------------------------- | -------- |
| jméno                      | Název aktivity                     | Ano      |
| description               | Text popisující, k čemu se aktivita používá | Ne       |
| type                      | Pro aktivitu uložená procedura je typ aktivity **SqlServerStoredProcedure** | Ano      |
| linkedServiceName         | Odkaz na **Azure SQL Database** nebo **Azure SQL Data Warehouse** nebo **SQL Server** zaregistrován jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Ano      |
| storedProcedureName       | Zadejte název uložené procedury, která se má vyvolat. | Ano      |
| storedProcedureParameters | Zadejte hodnoty parametrů uložených procedur. Pomocí `"param1": { "value": "param1Value","type":"param1Type" }` předat hodnoty parametrů a jejich typ podporovaný zdrojem dat. Pokud pro parametr potřebujete předat hodnotu null, použijte `"param1": { "value": null }` (malá písmena). | Ne       |

## <a name="parameter-data-type-mapping"></a>Mapování datových typů parametrů
Datový typ, který zadáte pro parametr, je typ Azure Data Factory, který se mapuje na datový typ ve zdroji dat, který používáte. Mapování datových typů pro zdroj dat můžete najít v oblasti konektory. Některé příklady jsou

| Zdroj dat          | Mapování datových typů |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informace o chybě

Pokud uložená procedura selhává a vrátí podrobnosti o chybě, nemůžete zachytit informace o chybě přímo ve výstupu aktivity. Data Factory se však všechny události spouštějí do Azure Monitor. V rámci událostí, které Data Factory pumpy Azure Monitor, se tam zobrazí podrobnosti o chybě. Můžete například nastavit e-mailová upozornění z těchto událostí. Další informace najdete v tématu [Alert and Monitoring Data Factory pomocí Azure monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provádění Machine Learning Bach](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
