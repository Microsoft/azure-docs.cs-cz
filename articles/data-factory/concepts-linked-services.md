---
title: Propojené služby v Azure Data Factory
description: Přečtěte si o propojených službách v Data Factory. Propojené služby propojují úložiště výpočetních a datových dat s totoovou továrnou na data.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 90e51e8b56bd3fb63d56c630d47770e97f439796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563528"
---
# <a name="linked-services-in-azure-data-factory"></a>Propojené služby v Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-linked-services.md)

Tento článek popisuje, co jsou propojené služby, jak jsou definovány ve formátu JSON a jak se používají v kanálech Azure Data Factory.

Pokud jste s Toutovou službou pro data tezi te, [přečtěte si přehled v tématu Úvod do Azure Data Factory.](introduction.md)

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. **Kanál** je logické seskupení **aktivit,** které společně provádějí úkol. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Můžete například použít aktivitu kopírování ke kopírování dat z místního SQL Serveru do úložiště objektů blob Azure. Potom můžete použít aktivitu Hive, která spouští skript Hive v clusteru Azure HDInsight ke zpracování dat z úložiště objektů Blob k vytvoření výstupních dat. Nakonec můžete použít druhou aktivitu kopírování ke kopírování výstupních dat do Azure SQL Data Warehouse, na příkladu, které jsou vytvořeny řešení pro vytváření sestav business intelligence (BI). Další informace o kanálech a aktivitách najdete v [tématu Kanály a aktivity](concepts-pipelines-activities.md) v Azure Data Factory.

**Datová sada** je nyní pojmenované zobrazení dat, které jednoduše odkazuje nebo odkazuje na data, která chcete použít ve svých **aktivitách** jako vstupy a výstupy.

Před vytvořením datové sady je nutné vytvořit **propojenou službu,** která propojí úložiště dat s továrnou na data. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Přemýšlejte o tom tímto způsobem; datová sada představuje strukturu dat v rámci propojených úložišť dat a propojená služba definuje připojení ke zdroji dat. Například propojené služby Azure Storage propojuje účet úložiště do datové továrny. Datová sada Azure Blob představuje kontejner objektů blob a složku v rámci tohoto účtu úložiště Azure, který obsahuje vstupní objekty BLOB, které mají být zpracovány.

Zde je ukázkový scénář. Chcete-li zkopírovat data z úložiště objektů Blob do databáze SQL, vytvořte dvě propojené služby: Azure Storage a Azure SQL Database. Potom vytvořte dvě datové sady: datovou sadu Azure Blob (která odkazuje na propojenou službu Azure Storage) a datovou sadu Azure SQL Table (která odkazuje na propojenou službu Azure SQL Database). Služby propojené s Azure Storage a Azure SQL Database obsahují připojovací řetězce, které Data Factory používá za běhu k připojení k úložišti Azure Storage a Azure SQL Database. Datová sada Objektů blob Azure určuje kontejner objektů blob a složku objektů blob, která obsahuje vstupní objekty BLOB v úložišti objektů Blob. Datová sada Tabulka Azure SQL určuje tabulku SQL v databázi SQL, do které se mají data kopírovat.

Následující diagram znázorňuje vztahy mezi kanálem, aktivitou, datovou sadou a propojenou službou v datové továrně:

![Vztah mezi kanálem, aktivitou, datovou sadou, propojenými službami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Propojená služba JSON
Propojená služba v Datové továrně je definována ve formátu JSON takto:

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

Následující tabulka popisuje vlastnosti ve výše uvedeném json:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název propojené služby. Viz [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ propojené služby. Například: AzureStorage (úložiště dat) nebo AzureBatch (výpočetní). Viz popis pro typeProperties. | Ano |
typeProperties | Vlastnosti typu se liší pro každé úložiště dat nebo výpočetní prostředky. <br/><br/> Podporované typy úložiště dat a jejich vlastnosti typu naleznete v tabulce [typů datové sady](concepts-datasets-linked-services.md#dataset-type) v tomto článku. Přejděte do článku konektor úložiště dat se dozvíte o vlastnostech typu specifické pro úložiště dat. <br/><br/> Podporované typy výpočetních prostředků a jejich vlastnosti typu najdete v [tématu Výpočetní propojené služby](compute-linked-services.md). | Ano |
connectVia | [Prostředí Integrace Runtime,](concepts-integration-runtime.md) které se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne

## <a name="linked-service-example"></a>Příklad propojené služby
Následující propojená služba je propojená služba Azure Storage. Všimněte si, že typ je nastavena na AzureStorage. Vlastnosti typu pro propojenou službu Azure Storage zahrnují připojovací řetězec. Služba Data Factory používá tento připojovací řetězec pro připojení k úložišti dat za běhu.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
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

Propojené služby můžete vytvořit pomocí jednoho z těchto nástrojů nebo sad SDK: [rozhraní .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [ROZHRANÍ REST API](quickstart-create-data-factory-rest-api.md), Šablona Správce prostředků Azure a Portál Azure.

## <a name="data-store-linked-services"></a>Propojené služby úložiště dat
Seznam úložišť dat podporovaných datafactory najdete v článku [přehled konektoru.](copy-activity-overview.md#supported-data-stores-and-formats) Kliknutím na úložiště dat zjistíte podporované vlastnosti připojení.

## <a name="compute-linked-services"></a>Propojené služby Compute
Referenční [výpočetní prostředí podporovaná](compute-linked-services.md) pro podrobnosti o různých výpočetních prostředích, ke kterému se můžete připojit z vaší továrny na data, a také o různých konfiguracích.

## <a name="next-steps"></a>Další kroky
V následujícím kurzu naleznete podrobné pokyny pro vytváření kanálů a datových sad pomocí jednoho z těchto nástrojů nebo sad SDK.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Úvodní příručka: vytvoření datové továrny pomocí PowerShellu](quickstart-create-data-factory-powershell.md)
- [Úvodní příručka: vytvoření datové továrny pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md)
- [Úvodní příručka: vytvoření datové továrny pomocí portálu Azure](quickstart-create-data-factory-portal.md)
