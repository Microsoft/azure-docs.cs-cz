---
title: Propojené služby v Azure Data Factory
description: Přečtěte si o propojených službách v Data Factory. Propojené služby propojí výpočetní a datové úložiště s datovou továrnou.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 3d49422af01e38884b5d8ff871fbe84254938944
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434108"
---
# <a name="linked-services-in-azure-data-factory"></a>Propojené služby v Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, co jsou propojené služby, jak jsou definované ve formátu JSON a jak se používají v Azure Data Factorych kanálech.

Pokud s Data Factory začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md) pro přehled.

## <a name="overview"></a>Přehled

Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit** , které dohromady provádějí úlohu. Aktivity v kanálu definují akce, které se mají provádět s daty. Aktivitu kopírování můžete například použít ke kopírování dat z SQL Server do úložiště objektů BLOB v Azure. Pak můžete použít aktivitu podregistru, která spouští skript podregistru v clusteru Azure HDInsight ke zpracování dat z úložiště objektů blob za účelem vytvoření výstupních dat. Nakonec můžete použít druhou aktivitu kopírování ke zkopírování výstupních dat do služby Azure synapse Analytics (dříve SQL Data Warehouse), a to nad tím, která řešení pro vytváření sestav business intelligence (BI) jsou sestavená. Další informace o kanálech a aktivitách najdete v tématu [kanály a aktivity](concepts-pipelines-activities.md) v Azure Data Factory.

Nyní je **datovou sadou** pojmenované zobrazení dat, která jednoduše odkazují na data, která chcete ve svých **aktivitách** použít jako vstupy a výstupy.

Před vytvořením datové sady je nutné vytvořit **propojenou službu** , která propojí úložiště dat s datovou továrnou. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Zamyslete se nad tímto způsobem; datová sada představuje strukturu dat v propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například propojená služba Azure Storage propojuje účet úložiště s datovou továrnou. Datová sada objektů BLOB v Azure představuje kontejner objektů BLOB a složku v rámci tohoto Azure Storage účtu, která obsahuje vstupní objekty blob, které se mají zpracovat.

Tady je ukázkový scénář. Pokud chcete kopírovat data z úložiště objektů blob do SQL Database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Pak vytvořte dvě datové sady: datovou sadu Azure Blob (která odkazuje na propojenou službu Azure Storage) a datovou sadu tabulky Azure SQL (která odkazuje na propojenou službu Azure SQL Database). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které Data Factory používá za běhu, aby se připojily k vašemu Azure Storage a Azure SQL Database. Datová sada Azure Blob určuje kontejner objektů BLOB a složku objektů blob, které obsahují vstupní objekty BLOB v úložišti objektů BLOB. Datová sada tabulky SQL Azure Určuje tabulku SQL ve vašem SQL Database, do které se mají kopírovat data.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v Data Factory:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Služba JSON propojené služby

Propojená služba v Data Factory je definována ve formátu JSON následujícím způsobem:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Následující tabulka obsahuje popis vlastností ve výše uvedeném formátu JSON:

Vlastnost | Popis | Povinné |
-------- | ----------- | -------- |
name | Název propojené služby. Viz [pravidla pro Pojmenovávání Azure Data Factory](naming-rules.md). |  Yes |
typ | Typ propojené služby. Například: AzureBlobStorage (úložiště dat) nebo AzureBatch (COMPUTE). Podívejte se na popis pro typeProperties. | Yes |
typeProperties | Vlastnosti typu jsou pro každé úložiště dat nebo výpočetní prostředky odlišné. <br/><br/> Podporované typy úložiště dat a jejich vlastnosti typu najdete v článku [Přehled konektoru](copy-activity-overview.md#supported-data-stores-and-formats) . Přejděte do článku konektor úložiště dat, kde se dozvíte o vlastnostech typu specifických pro úložiště dat. <br/><br/> Podporované typy výpočtů a jejich vlastnosti typu najdete v tématu [propojené služby COMPUTE](compute-linked-services.md). | Yes |
connectVia | [Integration runtime](concepts-integration-runtime.md) , která se má použít pro připojení k úložišti dat Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No

## <a name="linked-service-example"></a>Příklad propojené služby

Následující propojená služba je propojená služba Azure Blob Storage. Všimněte si, že typ je nastavený na Azure Blob Storage. Mezi vlastnosti typu propojené služby Azure Blob Storage patří připojovací řetězec. Služba Data Factory používá tento připojovací řetězec pro připojení k úložišti dat za běhu.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Vytvoření propojených služeb

Propojené služby je možné vytvořit v prostředí Azure Data Factorye prostřednictvím [centra pro správu](author-management-hub.md) a všech aktivit, datových sad nebo toků dat, které na ně odkazují.

Propojené služby můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager Template a Azure Portal.


## <a name="data-store-linked-services"></a>Propojené služby úložiště dat

Seznam úložišť dat podporovaných nástrojem Data Factory najdete v článku [Přehled konektoru](copy-activity-overview.md#supported-data-stores-and-formats) . Kliknutím na úložiště dat získáte informace o podporovaných vlastnostech připojení.

## <a name="compute-linked-services"></a>Propojené služby Compute

Referenční [výpočetní prostředí jsou podporovaná](compute-linked-services.md) pro podrobnosti o různých výpočetních prostředích, ke kterým se můžete připojit z vaší datové továrny i s různými konfiguracemi.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny k vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK najdete v následujícím kurzu.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý Start: vytvoření datové továrny pomocí PowerShellu](quickstart-create-data-factory-powershell.md)
- [Rychlý Start: vytvoření datové továrny pomocí REST API](quickstart-create-data-factory-rest-api.md)
- [Rychlý Start: vytvoření datové továrny pomocí Azure Portal](quickstart-create-data-factory-portal.md)
