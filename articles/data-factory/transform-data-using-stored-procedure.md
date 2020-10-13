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
ms.openlocfilehash: bdab4f33852be6bfc2621e2cbecff76778567b1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89484727"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformuje data pomocí aktivity SQL Server uložených procedur v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuální verze](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivity transformace dat v [kanálu](concepts-pipelines-activities.md) Data Factory slouží k transformaci a zpracování nezpracovaných dat do předpovědi a přehledů. Aktivita uložená procedura je jednou z aktivit transformace, které Data Factory podporuje. Tento článek sestaví na článku s [transformačními daty](transform-data.md) , který prezentuje obecný přehled transformace dat a podporovaných transformačních aktivit v Data Factory.

> [!NOTE]
> Pokud Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](introduction.md) a udělejte si kurz: [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

Aktivitu uložené procedury můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat v podniku nebo na virtuálním počítači Azure (VM): 

- Azure SQL Database
- Azure Synapse Analytics (dříve SQL Data Warehouse)
- SQL Server databázi.  Pokud používáte SQL Server, nainstalujte modul runtime integrace v místním prostředí do stejného počítače, který je hostitelem databáze, nebo na samostatném počítači, který má přístup k databázi. Self-Hosted Integration runtime je komponenta, která připojuje zdroje dat místně nebo na virtuálním počítači Azure s Cloud Services zabezpečeným a spravovaným způsobem. Podrobnosti najdete v článku věnovaném místnímu [prostředí Integration runtime](create-self-hosted-integration-runtime.md) .

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server můžete nakonfigurovat **SqlSink** v aktivitě kopírování a vyvolat uloženou proceduru pomocí vlastnosti **sqlWriterStoredProcedureName** . Podrobnosti o této vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Volání uložené procedury při kopírování dat do služby Azure synapse Analytics pomocí aktivity kopírování se nepodporuje. Můžete ale použít aktivitu uložená procedura k vyvolání uložené procedury ve službě Azure synapse Analytics. 
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure synapse Analytics můžete nakonfigurovat **SqlSource** v aktivitě kopírování, aby se vyvolala uložená procedura pro čtení dat ze zdrojové databáze pomocí vlastnosti **sqlReaderStoredProcedureName** . Další informace najdete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md)a [Azure synapse Analytics](connector-azure-sql-data-warehouse.md) .          

 

## <a name="syntax-details"></a>Podrobnosti syntaxe
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

| Vlastnost                  | Popis                              | Povinné |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Název aktivity                     | Yes      |
| Popis               | Text popisující, k čemu se aktivita používá | No       |
| typ                      | Pro aktivitu uložená procedura je typ aktivity **SqlServerStoredProcedure** | Yes      |
| linkedServiceName         | Odkaz na **Azure SQL Database** nebo **analýzy Azure synapse** nebo **SQL Server** v Data Factory zaregistrován jako propojená služba. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) . | Yes      |
| storedProcedureName       | Zadejte název uložené procedury, která se má vyvolat. | Yes      |
| storedProcedureParameters | Zadejte hodnoty parametrů uložených procedur. Slouží `"param1": { "value": "param1Value","type":"param1Type" }` k předání hodnot parametrů a jejich typu, které jsou podporovány zdrojem dat. Pokud pro parametr potřebujete předat hodnotu null, použijte `"param1": { "value": null }` (všechna malá písmena). | No       |

## <a name="parameter-data-type-mapping"></a>Mapování datových typů parametrů
Datový typ, který zadáte pro parametr, je typ Azure Data Factory, který se mapuje na datový typ ve zdroji dat, který používáte. Mapování datových typů pro zdroj dat můžete najít v oblasti konektory. Některé příklady jsou

| Zdroj dat          | Mapování datových typů |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
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
