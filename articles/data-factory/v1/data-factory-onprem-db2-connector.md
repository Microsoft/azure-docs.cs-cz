---
title: Přesun dat z DB2 pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z místní databáze DB2 pomocí aktivity kopírování Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bcd1e6f9f9be1765e76399707ae36776a9f1987d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387355"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Přesunutí dat z DB2 pomocí aktivity kopírování Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-db2-connector.md)
> * [Verze 2 (aktuální verze)](../connector-db2.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si část [konektor DB2 ve verzi v2](../connector-db2.md).


Tento článek popisuje, jak můžete použít aktivitu kopírování v nástroji Azure Data Factory ke kopírování dat z místní databáze DB2 do úložiště dat. Data můžete kopírovat do libovolného úložiště, které je uvedené jako podporovaná jímka v článku [Data Factory aktivity přesunu dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Toto téma se sestavuje na Data Factory článku, který představuje přehled přesunu dat pomocí aktivity kopírování a seznam podporovaných kombinací úložišť dat. 

Data Factory aktuálně podporuje pouze přesun dat z databáze DB2 do [podporovaného úložiště dat jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných úložišť dat do databáze DB2 není podporován.

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k místní databázi DB2 pomocí [brány pro správu dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení kanálu dat brány pro přesun dat najdete v článku věnovaném [přesunutí dat z místního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Brána je povinná i v případě, že je DB2 hostovaný na virtuálním počítači Azure s IaaS. Bránu můžete nainstalovat na stejný virtuální počítač s IaaS jako úložiště dat. Pokud se brána může připojit k databázi, můžete bránu nainstalovat na jiný virtuální počítač.

Brána pro správu dat poskytuje integrovaný ovladač DB2, takže nemusíte ručně instalovat ovladač pro kopírování dat z DB2.

> [!NOTE]
> Tipy pro řešení potíží s připojením a bránou najdete v článku řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .


## <a name="supported-versions"></a>Podporované verze
Konektor Data Factory DB2 podporuje následující platformy a verze IBM DB2 s architekturou Distributed relačních databází (DRDA) SQL Access Manager verze 9, 10 a 11:

* IBM DB2 pro z/OS verze 11,1
* IBM DB2 pro z/OS verze 10,1
* IBM DB2 pro i (AS400) verze 7,2
* IBM DB2 pro i (AS400) verze 7,1
* IBM DB2 pro Linux, UNIX a Windows (LUW) verze 11
* IBM DB2 pro LUW verze 10,5
* IBM DB2 pro LUW verze 10,1

> [!TIP]
> Pokud se zobrazí chybová zpráva "balíček odpovídající žádosti o spuštění příkazu SQL nebyl nalezen. SQLSTATE = 51002 SQLCODE =-805, "Důvodem je, že pro normálního uživatele v operačním systému není vytvořen potřebný balíček. Pokud chcete tento problém vyřešit, postupujte podle těchto pokynů pro typ serveru DB2:
> - DB2 pro i (AS400): umožní uživateli, aby před spuštěním aktivity kopírování vytvořil kolekci pro normálního uživatele. Chcete-li vytvořit kolekci, použijte příkaz: `create collection <username>`
> - DB2 pro z/OS nebo LUW: použijte účet s vysokými oprávněními – uživatel nebo správce, který má k dispozici balíčky a BIND, BINDADD, UDĚLÍ oprávnění k VEŘEJNÉmu spouštění kopie. Potřebný balíček se vytvoří automaticky během kopírování. Potom můžete přejít zpět na normálního uživatele pro následné spuštění kopírování.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování k přesunu dat z místního úložiště dat DB2 pomocí různých nástrojů a rozhraní API: 

- Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním Azure Data Factory. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním najdete v tomto [kurzu: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md). 
- Můžete také použít nástroje k vytvoření kanálu, včetně sady Visual Studio, Azure PowerShell, šablony Azure Resource Manager, rozhraní .NET API a REST API. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte propojené služby, které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové sady, které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte kanál s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce kopírováním se pro vás automaticky vytvoří definice JSON pro Data Factory propojené služby, datové sady a entity kanálu. Pokud používáte nástroje nebo rozhraní API (s výjimkou rozhraní .NET API), definujete Data Factory entit pomocí formátu JSON. Příklad JSON: kopírování dat z DB2 do úložiště objektů BLOB v Azure zobrazuje definice JSON pro Data Factory entity, které se používají ke kopírování dat z místního úložiště dat DB2.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit, které jsou specifické pro úložiště dat DB2.

## <a name="db2-linked-service-properties"></a>Vlastnosti propojené služby DB2
V následující tabulce jsou uvedeny vlastnosti JSON, které jsou specifické pro propojenou službu DB2.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **textový** |Tato vlastnost musí být nastavená na **OnPremisesDb2**. |Yes |
| **WebServer** |Název serveru DB2. |Yes |
| **databáze** |Název databáze DB2. |Yes |
| **XSD** |Název schématu v databázi DB2. Tato vlastnost rozlišuje velká a malá písmena. |No |
| **authenticationType** |Typ ověřování, který se používá pro připojení k databázi DB2. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| **jmen** |Název uživatelského účtu, pokud použijete základní ověřování nebo ověřování systému Windows. |No |
| **heslo** |Heslo pro uživatelský účet. |No |
| **gatewayName** |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi DB2. |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, například **Struktura**, **dostupnost** a **zásady** pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob Storage, Azure Table Storage atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu **relačních** typů, která zahrnuje datovou sadu DB2, má následující vlastnost:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| **tableName** |Název tabulky instance databáze DB2, na kterou odkazuje propojená služba. Tato vlastnost rozlišuje velká a malá písmena. |Ne (Pokud je zadaná vlastnost **dotazu** aktivity kopírování typu **RelationalSource** ) |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit kopírování, najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Vlastnosti aktivity kopírování, jako je **název**, **Popis**, **vstupy** , tabulka **výstupů** a **zásady**, jsou dostupné pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v části **typeProperties** v aktivitě, se liší pro každý typ aktivity. V případě aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů dat a jímky.

V případě aktivity kopírování, pokud je zdrojem typu **RelationalSource** (který zahrnuje DB2), jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| **zadávání** |K načtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: `"query": "select * from "MySchema"."MyTable""` |Ne (Pokud je určena vlastnost **TableName** objektu DataSet) |

> [!NOTE]
> V názvech schémat a tabulek se rozlišují velká a malá písmena. V příkazu dotazu uzavřete názvy vlastností pomocí "" (dvojité uvozovky).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z DB2 do Azure Blob Storage
Tento příklad poskytuje ukázkové definice JSON, které lze použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). V příkladu se dozvíte, jak kopírovat data z databáze DB2 do úložiště objektů BLOB. Data ale můžete zkopírovat do [libovolného podporovaného typu jímky úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování Azure Data Factory.

Ukázka má následující Data Factory entit:

- Propojená služba DB2 typu [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Propojená služba úložiště objektů BLOB v Azure typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Vstupní [datová sada](data-factory-create-datasets.md) [relačních](data-factory-onprem-db2-connector.md#dataset-properties) objektů typu
- Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties)
- [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá vlastnosti [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Ukázka kopíruje data z výsledku dotazu v databázi DB2 do objektu blob Azure za hodinu. Vlastnosti JSON, které jsou používány v ukázce, jsou popsány v částech, které následují za definicemi entit.

Jako první krok Nainstalujte a nakonfigurujte bránu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

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

**Propojená služba Azure Blob Storage**

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

Ukázka předpokládá, že jste vytvořili tabulku v DB2 s názvem "MyTable", která má sloupec s označením "časové razítko" pro data časové řady.

Vlastnost **External** je nastavena na hodnotu "true". Toto nastavení informuje službu Data Factory o tom, že tato datová sada je externí pro datovou továrnu a není vytvořená aktivitou v datové továrně. Všimněte si, že vlastnost **Type** je nastavená na **relační**.


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

Data se zapisují do nového objektu BLOB každou hodinu nastavením vlastnosti **frekvence** na "hour" (hodina) a vlastností **interval** na 1. Vlastnost **FolderPath** objektu BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá část roku, měsíce, dne a hodiny počátečního času.

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

**Kanál aktivity kopírování**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala zadané vstupní a výstupní datové sady a je naplánované spuštění každou hodinu. V definici JSON pro kanál je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data z tabulky Orders.

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
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojového typu na typ jímky pomocí následujících dvou kroků přístupu:

1. Převod z nativního zdrojového typu na typ .NET
2. Převod z typu .NET na nativní typ jímky

Následující mapování se používají, když aktivita kopírování převede data z typu DB2 na typ .NET:

| Typ databáze DB2 | Typ rozhraní .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Skutečné |Jednoduché |
| dvojité |dvojité |
| Float |dvojité |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Číselný |Decimal |
| Date (Datum) |Datum a čas |
| Čas |TimeSpan |
| Timestamp |DateTime |
| XML |Byte [] |
| Char |Řetězec |
| VarChar |Řetězec |
| LongVarChar |Řetězec |
| DB2DynArray |Řetězec |
| Binární |Byte [] |
| VarBinary |Byte [] |
| LongVarBinary |Byte [] |
| Objekty |Řetězec |
| VarGraphic |Řetězec |
| LongVarGraphic |Řetězec |
| Datový typ CLOB |Řetězec |
| Objekt blob |Byte [] |
| DbClob |Řetězec |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Skutečné |Jednoduché |
| dvojité |dvojité |
| Float |dvojité |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Číselný |Decimal |
| Date (Datum) |Datum a čas |
| Čas |TimeSpan |
| Timestamp |DateTime |
| XML |Byte [] |
| Char |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Informace o tom, jak namapovat sloupce ve zdrojové datové sadě ke sloupcům v datové sadě jímky, najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakující se čtení z relačních zdrojů
Při kopírování dat z relačního úložiště dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat vlastnost **zásady** opakování pro datovou sadu pro opětovné spuštění řezu, když dojde k selhání. Ujistěte se, že se stejná data čtou bez ohledu na to, kolikrát je řez znovu spuštěný, a bez ohledu na to, jak řez znovu spustíte. Další informace najdete v tématu [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Přečtěte si o klíčových faktorech, které mají vliv na výkon aktivity kopírování a způsoby optimalizace výkonu v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md).
