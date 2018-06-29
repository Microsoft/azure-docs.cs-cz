---
title: Transformace dat pomocí aktivity uložené procedury v Azure Data Factory | Microsoft Docs
description: Vysvětluje, jak vyvolat uloženou proceduru v databázi nebo datový sklad SQL Azure z objektu pro vytváření dat kanál pomocí aktivity uložené procedury aplikace SQL Server.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: e8e0f8352404892ea8af6a0fa176c336dd2c1659
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054020"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity uložené procedury serveru SQL v Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-stored-proc-activity.md)
> * [Aktuální verze](transform-data-using-stored-procedure.md)

Pomocí aktivit transformace dat v datové továrně [kanálu](concepts-pipelines-activities.md) k transformaci a zpracování nezpracovaných dat do předpovědi a statistiky. Aktivita uložené procedury je jedním z aktivit transformace, které podporuje služby Data Factory. Tento článek vychází [transformovat data](transform-data.md) článek, který představuje Obecné přehled o transformaci dat a aktivity podporované transformace v datové továrně.

> [!NOTE]
> Pokud jste nový do Azure Data Factory, pročtěte [Úvod do Azure Data Factory](introduction.md) a proveďte kurz: [kurz: transformovat data](tutorial-transform-data-spark-powershell.md) před přečtení tohoto článku. 

Aktivita uložené procedury můžete vyvolat uloženou proceduru v jednom z následujících úložiště dat ve vašem podniku nebo na virtuální počítač Azure (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Databáze systému SQL Server.  Pokud používáte systém SQL Server, nainstalujte modul runtime vlastním hostováním integrace ve stejném počítači, který je hostitelem databáze nebo na samostatný počítač, který má přístup k databázi. Integrace s vlastním hostováním runtime je, že komponenty, která připojí datového zdroje na místní nebo na virtuální počítač Azure s cloudovými službami v zabezpečený a spravovaný. V tématu [hostovanou na vlastním integrace runtime](create-self-hosted-integration-runtime.md) článku.

> [!IMPORTANT]
> Při kopírování dat do Azure SQL Database nebo SQL Server, můžete nakonfigurovat **SqlSink** v aktivitě kopírování k vyvolání uložené procedury pomocí **sqlWriterStoredProcedureName** vlastnost. Podrobnosti o vlastnosti, viz následující články konektor: [Azure SQL Database](connector-azure-sql-database.md), [systému SQL Server](connector-sql-server.md). Volání uložené procedury při kopírování dat do Azure SQL Data Warehouse pomocí aktivity kopírování není podporována. Ale aktivity uložené procedury můžete vyvolat uloženou proceduru v SQL Data Warehouse. 
>
> Při kopírování dat z Azure SQL Database nebo SQL Server nebo Azure SQL Data Warehouse, můžete nakonfigurovat **SqlSource** v aktivitě kopírování vyvolat uloženou proceduru čtení dat ze zdrojové databáze pomocí  **sqlReaderStoredProcedureName** vlastnost. Další informace naleznete v následujících článcích konektor: [Azure SQL Database](connector-azure-sql-database.md), [systému SQL Server](connector-sql-server.md), [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Syntaxe podrobnosti
Tady je formátu JSON pro definování aktivity uložené procedury:

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
| description               | Text popisující, co se používá aktivitu pro | Ne       |
| type                      | U aktivity uložené procedury je typ aktivity **SqlServerStoredProcedure** | Ano      |
| linkedServiceName         | Odkaz na **Azure SQL Database** nebo **Azure SQL Data Warehouse** nebo **systému SQL Server** registrován jako propojené služby ve službě Data Factory. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku. | Ano      |
| storedProcedureName       | Zadejte název uložené procedury, která bude vyvolána. | Ano      |
| storedProcedureParameters | Zadejte hodnoty pro parametry uložené procedury. Použití `"param1": { "value": "param1Value","type":"param1Type" }` předat hodnoty parametrů a jejich typu zdroje dat nepodporuje. Pokud potřebujete předat hodnotu null pro parametr, použijte `"param1": { "value": null }` (všechny malá písmena). | Ne       |

## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Streamované aktivitě Hadoop](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Vlastní aktivita .NET](transform-data-using-dotnet-custom-activity.md)
* [Aktivita provedení Bach Machine Learning](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
