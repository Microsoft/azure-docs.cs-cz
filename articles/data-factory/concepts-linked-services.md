---
title: Propojené služby v Azure Data Factory | Dokumentace Microsoftu
description: Další informace o ve službě Data Factory propojené služby. Propojené služby propojují úložiště výpočetní/dat do služby data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870129"
---
# <a name="linked-services-in-azure-data-factory"></a>Ve službě Azure Data Factory propojené služby
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-create-datasets.md)
> * [Aktuální verze](concepts-datasets-linked-services.md)

Tento článek popisuje, jaké propojené služby jsou, jak jsou definované ve formátu JSON, a jak se používají v kanály Azure Data Factory.

Pokud do služby Data Factory začínáte, přečtěte si téma [Úvod do služby Azure Data Factory](introduction.md) Přehled.

## <a name="overview"></a>Přehled
Objekt pro vytváření dat může mít jeden nebo víc kanálů. A **kanálu** je logické seskupení **aktivity** , které dohromady provádějí určitou úlohu. Aktivity v kanálu definují akce, které se mají provést s vašimi daty. Například můžete použít aktivitu kopírování ke kopírování dat z místního SQL serveru do úložiště objektů Blob v Azure. Potom můžete použít aktivitu Hivu, která spouští skript Hive v clusteru Azure HDInsight ke zpracování dat z úložiště objektů Blob a generuje výstupní data. Nakonec můžete použít druhou aktivitu kopírování ke kopírování dat výstup do Azure SQL Data Warehouse, na které business intelligence (BI), vytváření sestav jsou integrované řešení. Další informace o kanálech a aktivitách najdete v tématu [kanály a aktivity](concepts-pipelines-activities.md) ve službě Azure Data Factory.

Nyní **datovou sadu** je pojmenované zobrazení dat, která jednoduše body nebo odkazuje na data, kterou chcete použít v vaše **aktivity** jako vstupy a výstupy.

Než vytvoříte datovou sadu, je třeba vytvořit **propojená služba** k propojení vašeho úložiště dat do služby data factory. Propojené služby jsou velmi podobné připojovacím řetězcům, které definují informace o připojení, které služba Data Factory potřebuje pro připojení k externím prostředkům. Můžete ho chápat takto; Datová sada reprezentuje strukturu těchto dat v rámci propojených úložištích dat a propojená služba definuje připojení ke zdroji dat. Například Azure Storage propojená služba propojuje účet úložiště do služby data factory. Datová sada služby Azure Blob představuje kontejner objektů blob a složku v rámci tohoto účtu úložiště Azure obsahující vstupní objekty BLOB ke zpracování.

Tady je ukázkový scénář. Ke zkopírování dat z úložiště objektů Blob do služby SQL database, vytvoříte dvě propojené služby: Azure Storage a Azure SQL Database. Vytvořte dvě datové sady: Azure Blob datovou sadu (odkazuje propojenou službu Azure Storage) a datová sada tabulky SQL Azure (což odkazuje na službu Azure SQL Database, která je propojená). Azure Storage a Azure SQL Database propojené služby obsahují připojovací řetězce, které služby Data Factory používá za běhu pro připojení k Azure Storage a Azure SQL Database, v uvedeném pořadí. Datová sada Azure Blob Určuje kontejner objektů blob a složka objektů blob obsahující vstupní objekty BLOB v úložišti objektů Blob. Datová sada tabulky SQL Azure Určuje tabulku SQL ve službě SQL database, ke které se mají zkopírovat data.

Následující diagram znázorňuje vztahy mezi kanálu, aktivit, datové sady a propojené služby ve službě Data Factory:

![Vztah mezi kanálu, aktivita, datové sady, propojené služby](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON propojené služby
Ve službě Data Factory propojené služby je definovaná ve formátu JSON následujícím způsobem:

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

Následující tabulka popisuje vlastnosti v výše uvedený text JSON:

Vlastnost | Popis | Požaduje se |
-------- | ----------- | -------- |
jméno | Název propojené služby. Zobrazit [Azure Data Factory – pravidla pojmenování](naming-rules.md). |  Ano |
type | Typ propojené služby. Příklad: AzureStorage (úložiště dat) nebo AzureBatch (compute). Zobrazit popis typeProperties. | Ano |
typeProperties | Vlastnosti typu se liší pro jednotlivé obchody dat nebo výpočetní. <br/><br/> Podporované datové úložiště typy a jejich vlastnosti typu, najdete v článku [typ datové sady](concepts-datasets-linked-services.md#dataset-type) tabulky v tomto článku. Přejděte na článek konektor úložiště dat. Další informace o typu vlastnosti specifické pro úložiště dat. <br/><br/> Typy podporovaných výpočetních a jejich vlastnosti typu naleznete v tématu [propojené služby Compute](compute-linked-services.md). | Ano |
connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne

## <a name="linked-service-example"></a>Například propojená služba
Tato propojená služba je propojenou službu Azure Storage. Všimněte si, že typ je nastavený na AzureStorage. Typ vlastnosti propojenou službu Azure Storage zahrnují připojovací řetězec. Služba Data Factory používá tento připojovací řetězec pro připojení k úložišti dat za běhu.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Vytvoření propojených služeb
Propojené služby můžete vytvořit pomocí některého z těchto nástrojů nebo sad SDK: [rozhraní .NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [rozhraní REST API](quickstart-create-data-factory-rest-api.md), šablony Azure Resource Manageru a webu Azure portal

## <a name="data-store-linked-services"></a>Propojené služby úložiště dat
Připojení k úložišti dat najdete v našich [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats). Odkaz na seznam pro konkrétní připojení vlastnosti potřebné pro různých obchodech.

## <a name="compute-linked-services"></a>Propojené služby Compute
Referenční dokumentace [výpočetní prostředí podporované](compute-linked-services.md) pro podrobnosti o různých výpočetní prostředí můžete připojit k z vaší datové továrně, jakož i další možnosti konfigurace.

## <a name="next-steps"></a>Další postup
Projděte si následující kurz podrobné pokyny pro vytváření kanálů a datových sad pomocí jedné z těchto nástrojů nebo sad SDK.

- [Rychlý start: Vytvoření datové továrny pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md)
- [Rychlý start: vytvoření datové továrny pomocí prostředí PowerShell](quickstart-create-data-factory-powershell.md)
- [Rychlý start: vytvoření datové továrny pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md)
- [Rychlý start: vytvoření datové továrny pomocí webu Azure portal](quickstart-create-data-factory-portal.md)
