---
title: Transformace dat pomocí aktivity uložených procedur ve službě Azure Data Factory | Dokumentace Microsoftu
description: Vysvětluje, jak používat aktivitu uložená procedura aplikace SQL Server k vyvolání uložené procedury ve službě Azure SQL Database/Data Warehouse z kanálu Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: douglasl
ms.openlocfilehash: 54d0ce39ea511958824acb753bcf7102d33a6c90
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444024"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity uložená procedura SQL serveru ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuální verze](transform-data-using-stored-procedure.md)

Použití aktivity transformace dat ve službě Data Factory [kanálu](concepts-pipelines-activities.md) k transformaci a zpracování nezpracovaných dat do předpovědi a přehledy. Aktivita uložená procedura je jedním z aktivity transformace, které služba Data Factory podporuje. Tento článek vychází [transformovat data](transform-data.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace ve službě Data Factory.

> [!NOTE]
> Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](introduction.md) a udělat kurz: [kurz: transformace dat](tutorial-transform-data-spark-powershell.md) před čtením tohoto článku. 

Aktivity uložené procedury můžete použít k vyvolání uložené procedury v jedné z následujících úložiště dat ve vaší organizaci nebo na virtuálním počítači Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze SQL serveru.  Pokud používáte systém SQL Server, nainstalovat místní prostředí integration runtime ve stejném počítači, který je hostitelem databáze nebo na samostatném počítači, který má přístup k databázi. Místní prostředí integration runtime je součást, která se připojí data zdroje v místním prostředí nebo na virtuálním počítači Azure s cloudovými službami zabezpečení a správě způsobem. Zobrazit [modulu runtime integrace v místním prostředí](create-self-hosted-integration-runtime.md) , kde najdete podrobnosti.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování, která se má vyvolat uloženou proceduru s použitím **sqlWriterStoredProcedureName** vlastnost. Podrobné informace o vlastnosti, viz následující články konektor: [Azure SQL Database](connector-azure-sql-database.md), [systému SQL Server](connector-sql-server.md). Volání uložené procedury při kopírování dat do služby Azure SQL Data Warehouse s využitím aktivity kopírování se nepodporuje. Ale můžete použít aktivitu uložené procedury k vyvolání uložené procedury v SQL Data Warehouse. 
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování, která se má vyvolat uloženou proceduru pro čtení dat ze zdrojové databáze s použitím  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektor: [Azure SQL Database](connector-azure-sql-database.md), [systému SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Podrobnosti o syntaxi
Tady je formát JSON pro definici aktivity uložené procedury:

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
        "storedProcedureName": "sp_sample",
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
| description               | Text popisující, k čemu aktivita slouží | Ne       |
| type                      | Pro aktivitu uložená procedura je typ aktivity **SqlServerStoredProcedure** | Ano      |
| linkedServiceName         | Odkaz **Azure SQL Database** nebo **Azure SQL Data Warehouse** nebo **systému SQL Server** zaregistrovaný jako propojenou službu ve službě Data Factory. Další informace o tuto propojenou službu, najdete v článku [propojené služby Compute](compute-linked-services.md) článku. | Ano      |
| storedProcedureName       | Zadejte název uložené procedury, která se má vyvolat. | Ano      |
| storedProcedureParameters | Zadejte hodnoty parametrů uložené procedury. Použití `"param1": { "value": "param1Value","type":"param1Type" }` předat hodnoty parametrů a jejich typ podporovaný zdroj dat. Pokud potřebujete předat pro parametr hodnotu null, použijte `"param1": { "value": null }` (malými písmeny). | Ne       |

## <a name="error-info"></a>Informace o chybě

Když uloženou proceduru selže a vrátí podrobnosti o chybě, nelze zachytit informace o chybě přímo v výstup aktivity. Data Factory však čerpadla všechny jeho spuštění události do Azure monitoru aktivity. Mezi události, Data Factory čerpadla do Azure monitoru toho odesílá podrobnosti o chybě došlo. Můžete například nastavit e-mailová upozornění z těchto událostí. Další informace najdete v tématu [výstrahy a monitorování datové továrny pomocí Azure monitoru](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Další postup
Viz následující články, které vysvětlují, jak transformovat data dalšími způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita hivu](transform-data-using-hadoop-hive.md)
* [Aktivita pig](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Streamované aktivitě Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Spark](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení Bach Machine Learning](transform-data-using-machine-learning.md)
* [Aktivita uložená procedura](transform-data-using-stored-procedure.md)
