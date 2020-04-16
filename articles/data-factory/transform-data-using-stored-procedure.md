---
title: Transformace dat pomocí aktivity Uložená procedura
description: Vysvětluje, jak použít aktivitu uložené procedury serveru SQL Server k vyvolání uložené procedury v databázi Azure SQL/datovém skladu z kanálu datové továrny.
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
ms.openlocfilehash: 57bf653aa3f421ae8897c4be661ceef589fcdc06
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418809"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity uložené procedury serveru SQL Server v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuální verze](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivity transformace dat v [kanálu](concepts-pipelines-activities.md) data factory transformovat a zpracovávat nezpracovaná data do předpovědi a přehledy. Aktivita uložené procedury je jednou z transformačních aktivit, které podporuje data factory. Tento článek vychází z článku [dat transformace,](transform-data.md) který představuje obecný přehled transformace dat a podporované transformační aktivity v Datové továrně.

> [!NOTE]
> Pokud jste s Azure Data Factory tečeme na [úvodním úvodu do Azure Data Factory](introduction.md) a proveďte kurz: Transformace [dat](tutorial-transform-data-spark-powershell.md) před přečtením tohoto článku. 

Aktivita uložené procedury můžete použít k vyvolání uložené procedury v jednom z následujících úložišť dat v podniku nebo na virtuálním počítači Azure(VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze serveru SQL Server.  Pokud používáte SQL Server, nainstalujte modul runtime integrace s vlastním hostitelem na stejném počítači, který je hostitelem databáze, nebo na samostatném počítači, který má přístup k databázi. Runtime integrace s vlastním hostitelem je komponenta, která propojuje zdroje dat místně/na virtuálním počítači Azure s cloudovými službami bezpečným a spravovaným způsobem. Podrobnosti najdete v článku [runtime integrace s vlastním hostitelem.](create-self-hosted-integration-runtime.md)

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování vyvolat uloženou proceduru pomocí **sqlWriterStoredProcedureName** vlastnost. Podrobnosti o vlastnosti naleznete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md). Vyvolání uložené procedury při kopírování dat do datového skladu Azure SQL pomocí aktivity kopírování není podporováno. Ale můžete použít uloženou aktivitu procedury k vyvolání uložené procedury v datovém skladu SQL. 
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze pomocí **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektoru: [Azure SQL Database](connector-azure-sql-database.md), SQL [Server](connector-sql-server.md), Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Podrobnosti syntaxe
Zde je formát JSON pro definování aktivity uložené procedury:

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

Následující tabulka popisuje tyto vlastnosti JSON:

| Vlastnost                  | Popis                              | Požaduje se |
| ------------------------- | ---------------------------------------- | -------- |
| jméno                      | Název aktivity                     | Ano      |
| description               | Text popisující, k čemu se aktivita používá | Ne       |
| type                      | Pro aktivitu uložené procedury je typem aktivity **SqlServerStoredProcedure** | Ano      |
| linkedServiceName         | Odkaz na **Azure SQL Database** nebo Azure SQL Data **Warehouse** nebo SQL **Server** registrovaný jako propojená služba v Data Factory. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md) | Ano      |
| storedProcedureName       | Zadejte název uložené procedury, kterou chcete vyvolat. | Ano      |
| storedProcedureParameters | Zadejte hodnoty pro parametry uložené procedury. Slouží `"param1": { "value": "param1Value","type":"param1Type" }` k předání hodnot parametrů a jejich typu podporovaných zdrojem dat. Pokud potřebujete předat hodnotu null `"param1": { "value": null }` pro parametr, použijte (všechna malá písmena). | Ne       |

## <a name="parameter-data-type-mapping"></a>Mapování datového typu parametru
Datový typ, který zadáte pro parametr, je typ Azure Data Factory, který se mapuje na datový typ ve zdroji dat, který používáte. Mapování datových typů pro zdroj dat najdete v oblasti konektorů. Některé příklady jsou

| Zdroj dat          | Mapování datových typů |
| ---------------------|-------------------|
| Azure SQL Data Warehouse | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Database   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Informace o chybě

Pokud uložená procedura selže a vrátí podrobnosti o chybě, nelze zachytit informace o chybě přímo ve výstupu aktivity. Data Factory však čerpadla všechny své aktivity spustit události azure monitoru. Mezi události, které Data Factory čerpadla na Azure Monitor, tlačí podrobnosti o chybě tam. Můžete například nastavit e-mailová upozornění z těchto událostí. Další informace najdete v [tématu Alert and Monitor data factories using Azure Monitor](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provádění strojového učení Bach](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
