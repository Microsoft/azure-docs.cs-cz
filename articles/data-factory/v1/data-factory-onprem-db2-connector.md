---
title: Přesunutí dat z DB2 pomocí Azure Data Factory
description: Zjistěte, jak přesunout data z místní databáze DB2 pomocí aktivity kopírování Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931786"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Přesunutí dat z DB2 pomocí aktivity kopírování Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-db2-connector.md)
> * [Verze 2 (aktuální verze)](../connector-db2.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma DB2 connector ve verzi 2](../connector-db2.md).


Tento článek popisuje, jak můžete použít kopírovat aktivitu v Azure Data Factory ke kopírování dat z místní databáze DB2 do úložiště dat. Data můžete zkopírovat do libovolného úložiště, které je uvedeno jako podporovaná jímka v článku [aktivity přesunu dat datové továrny.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Toto téma vychází z článku Data Factory, který představuje přehled přesunu dat pomocí aktivity kopírování a uvádí podporované kombinace úložiště dat. 

Data Factory aktuálně podporuje pouze přesunutí dat z databáze DB2 do [úložiště dat podporované jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesouvání dat z jiných úložišť dat do databáze DB2 není podporováno.

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k místní databázi DB2 pomocí [brány pro správu dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení datového kanálu brány pro přesun dat najdete v článku [Přesun dat z místního do cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Brána je vyžadována i v případě, že db2 je hostovaný na Azure IaaS VM. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat. Pokud se brána může připojit k databázi, můžete ji nainstalovat na jiný virtuální počítač.

Brána pro správu dat poskytuje integrovaný ovladač DB2, takže není nutné ručně instalovat ovladač pro kopírování dat z DB2.

> [!NOTE]
> Tipy k řešení problémů s připojením a bránou najdete v článku [Poradce při potížích s bránou.](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)


## <a name="supported-versions"></a>Podporované verze
Konektor DB2 datové továrny podporuje následující platformy a verze IBM DB2 s distribuovanou relační databázovou architekturou (DRDA) SQL Access Manager verze 9, 10 a 11:

* IBM DB2 pro z/OS verze 11.1
* IBM DB2 pro z/OS verze 10.1
* IBM DB2 pro i (AS400) verze 7.2
* IBM DB2 pro i (AS400) verze 7.1
* IBM DB2 pro Linux, UNIX a Windows (LUW) verze 11
* IBM DB2 pro LUW verze 10.5
* IBM DB2 pro LUW verze 10.1

> [!TIP]
> Pokud se zobrazí chybová zpráva "Balíček odpovídající požadavku na spuštění příkazu SQL nebyl nalezen. SQLSTATE=51002 SQLCODE=-805," důvodem je nutný balíček není vytvořen pro běžného uživatele v os. Chcete-li tento problém vyřešit, postupujte podle následujících pokynů pro typ serveru DB2:
> - DB2 pro i (AS400): Nechte mocečitého uživatele vytvořit kolekci pro běžného uživatele před spuštěním aktivity kopírování. Chcete-li vytvořit kolekci, použijte příkaz:`create collection <username>`
> - DB2 pro z/OS nebo LUW: Ke spuštění kopie použijte účet s vysokými oprávněními – mocenského uživatele nebo správce, který má autority balíčků a BIND, BINDADD, GRANT EXECUTE TO PUBLIC permissions -- ke spuštění kopie jednou. Potřebný balíček se automaticky vytvoří během kopírování. Poté můžete přepnout zpět na normálního uživatele pro vaše následné spuštění kopie.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování pro přesun dat z místního úložiště dat DB2 pomocí různých nástrojů a api: 

- Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním dat Azure. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním naleznete v [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md). 
- Pomocí nástrojů můžete také vytvořit kanál, včetně Visual Studia, Azure PowerShellu, šablony Azure Resource Manager, rozhraní .NET API a rozhraní REST API. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování naleznete v [tématu Kopírovat aktivitu .](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte propojené služby pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte datové sady představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte kanál s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce kopírováním se automaticky vytvoří definice JSON pro propojené služby, datové sady a entity kanálu. Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete entity Factory dat pomocí formátu JSON. Příklad JSON: Kopírování dat z DB2 do úložiště objektů blob Azure zobrazuje definice JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat DB2.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Data Factory, které jsou specifické pro úložiště dat DB2.

## <a name="db2-linked-service-properties"></a>Vlastnosti propojené služby DB2
V následující tabulce jsou uvedeny vlastnosti JSON, které jsou specifické pro propojenou službu DB2.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Typ** |Tato vlastnost musí být nastavena **na OnPremisesDb2**. |Ano |
| **server** |Název serveru DB2. |Ano |
| **Databáze** |Název databáze DB2. |Ano |
| **Schématu** |Název schématu v databázi DB2. Tato vlastnost je malá a velká písmena. |Ne |
| **authenticationType** |Typ ověřování, který se používá pro připojení k databázi DB2. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| **Username** |Název uživatelského účtu, pokud používáte základní nebo windows ověřování. |Ne |
| **heslo** |Heslo pro uživatelský účet. |Ne |
| **název brány** |Název brány, kterou by měla služba Data Factory použít k připojení k místní databázi DB2. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je **struktura**, **dostupnost**a **zásady** pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, úložiště objektů blob Azure, úložiště Azure Table a tak dále).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **RelationalTable**, která obsahuje datovou sadu DB2, má následující vlastnost:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **Tablename** |Název tabulky v instanci databáze DB2, na kterou odkazuje propojená služba. Tato vlastnost je malá a velká písmena. |Ne (pokud je zadána vlastnost **dotazu** aktivity kopírování typu **RelationalSource)** |

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity
Seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit kopírování, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti aktivity kopírování, například **název**, **popis**, **tabulka vstupů,** tabulka **výstupů** a **zásady**, jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity se liší pro každý typ aktivity. U aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů dat a jímek.

V části CopyActivity, pokud je zdroj typu **RelationalSource** (který zahrnuje DB2), jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **query** |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: `"query": "select * from "MySchema"."MyTable""` |Ne (pokud je zadána vlastnost **tableName** datové sady) |

> [!NOTE]
> Názvy schémat a tabulek rozlišují malá a velká písmena. Do příkazu dotazu uzavřete názvy vlastností pomocí "" (dvojité uvozovky).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Příklad JSON: Kopírování dat z DB2 do úložiště objektů blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklad ukazuje, jak zkopírovat data z databáze DB2 do úložiště objektů Blob. Data však můžete zkopírovat do [libovolného typu úložiště dat pomocí aktivity](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopírování Azure Data Factory.

Ukázka má následující entity Data Factory:

- Propojená služba DB2 typu [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Propojená služba úložiště objektů blob Azure typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá vlastnosti [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka zkopíruje data z výsledku dotazu v databázi DB2 do objektu blob Azure každou hodinu. Vlastnosti JSON, které se používají v vzorku jsou popsány v částech, které následují definice entit.

Jako první krok nainstalujte a nakonfigurujte bránu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

**Propojená služba DB2**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Propojená služba úložiště objektů blob Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Vstupní datová sada DB2**

Ukázka předpokládá, že jste vytvořili tabulku v DB2 s názvem "MyTable", která má sloupec s názvem "časové razítko" pro data časové řady.

Externí **external** vlastnost je nastavena na hodnotu "true". Toto nastavení informuje službu Data Factory, že tato datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně. Všimněte si, že vlastnost **typu** je nastavena na **Relační tabulka**.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu nastavením **vlastnosti frekvence** na "Hodina" a vlastnost **interval** na 1. Vlastnost **folderPath** pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodinu.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kanál pro aktivitu kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití zadaných vstupních a výstupních datových sad a která je naplánována na každou hodinu. V definici JSON pro kanál je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data z tabulky Objednávky.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Mapování typů pro DB2
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) aktivita kopírování provádí převody automatických typů z typu zdroje na typ jímky pomocí následujícího dvoustupňového přístupu:

1. Převod z nativního typu zdroje na typ .NET
2. Převod z typu .NET na nativní typ jímky

Následující mapování se používají při kopírování aktivity převede data z typu DB2 na typ .NET:

| Typ databáze DB2 | Typ rozhraní .NET Framework |
| --- | --- |
| Smallint |Int16 |
| Integer |Int32 |
| Bigint |Int64 |
| Skutečné |Single |
| Double |Double |
| Plovoucí desetinná čárka |Double |
| Desetinné číslo |Desetinné číslo |
| DecimalFloat |Desetinné číslo |
| Numeric |Desetinné číslo |
| Datum |DateTime |
| Time |TimeSpan |
| Časové razítko |DateTime |
| XML |Bajt[] |
| Char |Řetězec |
| Varchar |Řetězec |
| LongVarChar (Vavč.) |Řetězec |
| Db2DynArray |Řetězec |
| binární |Bajt[] |
| Varbinary |Bajt[] |
| Binární soubor LongVar |Bajt[] |
| Grafické |Řetězec |
| VarGrafika |Řetězec |
| Funkce LongVarGraphic |Řetězec |
| Clob |Řetězec |
| Objekt blob |Bajt[] |
| DbClob |Řetězec |
| Smallint |Int16 |
| Integer |Int32 |
| Bigint |Int64 |
| Skutečné |Single |
| Double |Double |
| Plovoucí desetinná čárka |Double |
| Desetinné číslo |Desetinné číslo |
| DecimalFloat |Desetinné číslo |
| Numeric |Desetinné číslo |
| Datum |DateTime |
| Time |TimeSpan |
| Časové razítko |DateTime |
| XML |Bajt[] |
| Char |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z úložiště relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat vlastnost **zásad** opakování pro datovou sadu pro opětovné spuštění řezu, když dojde k chybě. Ujistěte se, že stejná data jsou čtena bez ohledu na to, kolikrát je řez znovu spuštěn, a bez ohledu na to, jak znovu spustíte řez. Další informace naleznete v tématu [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Seznamte se s klíčovými faktory, které ovlivňují výkon aktivity kopírování, a způsoby optimalizace výkonu v [průvodci výkonem a laděním aktivity kopírování](data-factory-copy-activity-performance.md).
