---
title: Přesun dat z DB2 pomocí Azure Data Factory | Microsoft Docs
description: Další informace o přesunutí dat z databáze DB2 místně pomocí Azure Data Factory kopie aktivity
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fc4ce0a2ae33e99ecede371d9f17fb9a63851f64
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622019"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Přesun dat z DB2 pomocí Azure Data Factory kopie aktivity
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-onprem-db2-connector.md)
> * [Verze 2 – Preview](../connector-db2.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [DB2 konektoru V2](../connector-db2.md).


Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z databáze DB2 místně do úložiště dat. Můžete zkopírovat data do jakékoli úložiště, která je uvedena jako podporovaných jímku v [aktivity přesunu dat pro vytváření dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) článku. Toto téma je založený na článku objekt pro vytváření dat, která zobrazí přehled o přesun dat pomocí aktivity kopírování a jsou uvedeny kombinace podporované datové úložiště. 

Aktuálně podporuje pouze přesunutí dat z databáze DB2 k objektu pro vytváření dat [úložiště dat podporovaných podřízený](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Přesun dat z jiných dat ukládá do DB2 databáze není podporována.

## <a name="prerequisites"></a>Požadavky
Objekt pro vytváření dat podporuje připojení k místní databázi DB2 pomocí [Brána pro správu dat](data-factory-data-management-gateway.md). Podrobné pokyny k nastavení brány datovém kanálu pro přesun dat najdete v tématu [přesun dat z lokálního prostředí do cloudu](data-factory-move-data-between-onprem-and-cloud.md) článku.

Vyžaduje se brána, i když DB2 je hostitelem virtuálního počítače Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat. Pokud brána se může připojit k databázi, můžete bránu nainstalovat na jiný virtuální počítač.

Brána pro správu dat poskytuje integrované DB2 ovladač, takže není nutné ručně nainstalovat ovladač ke zkopírování dat z databáze DB2.

> [!NOTE]
> Tipy pro řešení problémů připojení a problémy brány najdete v tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) článku.


## <a name="supported-versions"></a>Podporované verze
Konektor služby Data Factory DB2 podporuje tyto platformy IBM DB2 a verze správce přístupu k SQL distribuované relační databáze architektura (DRDA) verze 9, 10 a 11:

* IBM DB2 pro z verze 11.1 operačního systému
* IBM DB2 pro z verze 10.1 operačního systému
* IBM DB2 verze i (AS400) 7.2
* IBM DB2 i (AS400) verze 7.1
* IBM DB2 pro Windows (LUW) verze 11, Linux a UNIX
* IBM DB2 pro LUW verze 10.5
* IBM DB2 pro LUW verze 10.1

> [!TIP]
> Pokud se zobrazí chybová zpráva "nebyl nalezen balíček odpovídající žádost o provedení příkazu SQL. SQLSTATE = 51002 = SQLCODE-805, "z důvodu je nutné balíček není vytvořen normální uživatele na operačního systému. Chcete-li vyřešit tento problém, postupujte podle těchto pokynů pro váš typ serveru DB2:
> - DB2 pro i (AS400): může uživatel power vytvoření kolekce pro běžné uživatele před spuštěním aktivity kopírování. Chcete-li vytvořit kolekci, použijte příkaz: `create collection <username>`
> - DB2 pro z/OS nebo LUW: použití účtu s vysokou úrovní oprávnění – power users nebo správce, který má balíček autority a vazby, BINDADD, UDĚLTE EXECUTE veřejná oprávnění – pro kopírování jednou. Potřebný balíček je vytvořeno automaticky při vytvoření kopie. Potom můžete přepnout zpět na normální uživatelské pro vaše další kopie spustí.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování pro přesun dat z úložiště dat DB2 místně pomocí různých nástrojů a rozhraní API: 

- Nejjednodušší způsob, jak vytvořit kanál je pomocí Průvodce kopírováním Azure Data Factory. Rychlé návod k vytvoření kanálu pomocí Průvodce kopírováním najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md). 
- Nástroje můžete taky vytvořit kanál, včetně portálu Azure, Visual Studio, prostředí Azure PowerShell, šablonu Azure Resource Manager, .NET API a rozhraní REST API. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [aktivity kopírování kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření propojených služeb propojíte vstup a výstup úložiště dat do data factory.
2. Vytvořte datové sady, které představují vstupní a výstupní data pro kopírování. 
3. Vytvoření kanálu s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Pokud použijete Průvodce kopírováním, JSON definice služby Data Factory propojené služeb, datových sad a kanálu entity jsou automaticky vytvoří za vás. Pokud používáte rozhraní API (s výjimkou .NET API) nebo nástroje, definujete ve formátu JSON entit služby Data Factory. [JSON příklad: kopírování dat z DB2 do úložiště objektů Azure Blob](#json-example-copy-data-from-db2-to-azure-blob) ukazuje definice JSON entit služby Data Factory, které se používají ke zkopírování dat z místního úložiště dat DB2.

Následující části obsahují podrobnosti o vlastnostech JSON, které slouží k určení entit služby Data Factory, které jsou specifické pro úložiště dat DB2.

## <a name="db2-linked-service-properties"></a>Vlastnosti DB2 propojené služby
Následující tabulka uvádí vlastnosti JSON, které jsou specifické pro DB2 propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **type** |Tato vlastnost musí být nastavená na **OnPremisesDb2**. |Ano |
| **Server** |Název serveru DB2. |Ano |
| **database** |Název databáze DB2. |Ano |
| **schema** |Název schématu v databázi DB2. Tato vlastnost je malá a velká písmena. |Ne |
| **authenticationType.** |Typ ověřování, který se používá k připojení k databázi DB2. Možné hodnoty jsou: anonymní, základní a systému Windows. |Ano |
| **Uživatelské jméno** |Název pro uživatelský účet, pokud používáte ověřování Basic nebo Windows. |Ne |
| **Heslo** |Heslo pro uživatelský účet. |Ne |
| **gatewayName** |Název brány, kterou služba Data Factory měla použít pro připojení k místní databázi DB2. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Částech, jako například **struktura**, **dostupnosti**a **zásad** pro datovou sadu JSON, jsou podobné pro všechny typy datovou sadu (Azure SQL, úložiště objektů Blob v Azure, Azure Table storage a tak dále).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. **Rámci typeProperties** části datové sady typu **RelationalTable**, což zahrnuje datová sada DB2, má následující vlastnost:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| **tableName** |Název tabulky instance databáze DB2, na kterou odkazuje propojená služba. Tato vlastnost je malá a velká písmena. |Ne (Pokud **dotazu** vlastnost aktivity kopírování typu **RelationalSource** je zadána) |

## <a name="copy-activity-properties"></a>Zkopírovat vlastnosti aktivit
Seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity kopírování najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. Kopírovat vlastnosti aktivity, jako například **název**, **popis**, **vstupy** tabulky, **výstupy** tabulky, a **zásad**, jsou k dispozici pro všechny typy aktivit. Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity se liší pro jednotlivé typy aktivit. Pro aktivitu kopírování vlastnosti lišit v závislosti na typech zdrojů dat a jímky.

Pro aktivitu kopírování, pokud je zdroj typu **RelationalSource** (která zahrnuje DB2), jsou k dispozici v následujících vlastností **rámci typeProperties** části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| **Dotaz** |Pomocí vlastního dotazu přečíst data. |Řetězec dotazu SQL. Příklad: `"query": "select * from "MySchema"."MyTable""` |Ne (Pokud **tableName** je zadána vlastnost datové sady) |

> [!NOTE]
> Schéma a tabulku názvy rozlišují malá a velká písmena. V příkazu dotazu, uzavřete názvy vlastností pomocí "" (dvojité uvozovky).

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>Příklad JSON: kopírování dat z DB2 do úložiště objektů Blob v Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tento příklad ukazuje, jak zkopírovat data z databáze DB2 do úložiště objektů Blob. Data však můžete zkopírovat do [všechna podporovaná data ukládat typ jímky](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování objektu pro vytváření dat Azure.

Ukázka má následující entity služby Data Factory:

- DB2 propojené služby typu [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties)
- Azure Blob storage, propojené služby typu [azurestorage.](data-factory-azure-blob-connector.md#linked-service-properties)
- Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) vlastnosti

Ukázka zkopíruje data z výsledku dotazu v databázi DB2 do objektu blob Azure každou hodinu. Vlastnosti JSON, které se používají v ukázce jsou popsané v následujících definice entity.

Jako první krok nainstalujte a nakonfigurujte bránu data gateway. Pokyny naleznete v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**DB2 propojené služby**

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

**Objekt Blob propojená služba Azure storage**

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

**Vstupní datové sady DB2**

Příkladu se předpokládá, že jste vytvořili tabulku v databázích DB2 s názvem "MyTable", která má sloupec s názvem "časové razítko" data časové řady.

**Externí** je nastavena na hodnotu "true". Toto nastavení služba Data Factory informuje, že tato datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně. Všimněte si, že **typ** je nastavena na **RelationalTable**.


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

Data se zapisují do nového objektu blob každou hodinu nastavením **frekvence** vlastnost "Hodinu" a **interval** vlastnost na hodnotu 1. **FolderPath** vlastností pro objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a hodina části čas spuštění.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro používání zadaný vstup a výstupní datové sady a který je naplánováno spuštění každou hodinu. V definici JSON pro kanál **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data z tabulky "Objednávky".

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

## <a name="type-mapping-for-db2"></a>Mapování typu pro DB2
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí převody typů automatické z typ zdroje pro typ jímky pomocí následující postup ve dvou krocích:

1. Převést z typu nativní zdroj na typ formátu .NET
2. Převést na typ nativní jímky typ formátu .NET

Aktivita kopírování převádí data z typu DB2 na typ .NET jsou použity následující mapování:

| Typ databáze DB2 | Typ rozhraní .NET framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Jednoduchá |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Čísla |Decimal |
| Datum |DateTime |
| Čas |Časový interval |
| Časové razítko |DateTime |
| XML |Byte] |
| Char |Řetězec |
| VarChar |Řetězec |
| LongVarChar |Řetězec |
| DB2DynArray |Řetězec |
| Binární hodnota |Byte] |
| VarBinary |Byte] |
| LongVarBinary |Byte] |
| Obrázek |Řetězec |
| VarGraphic |Řetězec |
| LongVarGraphic |Řetězec |
| Datový typ CLOB |Řetězec |
| Objekt blob |Byte] |
| DbClob |Řetězec |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Jednoduchá |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Čísla |Decimal |
| Datum |DateTime |
| Čas |Časový interval |
| Časové razítko |DateTime |
| XML |Byte] |
| Char |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další postupy k mapování sloupců v datové sadě zdroje na sloupců v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Opakovatelných čtení z relační zdrojů
Při kopírování dat z relační datové úložiště, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Můžete také nakonfigurovat opakovaném **zásad** vlastnost pro datovou sadu řez znovu spustit, když dojde k chybě. Ujistěte se, že stejná data je pro čtení bez ohledu na to, kolikrát se znovu spustí řez, a to bez ohledu na to, jak spustit řez znovu. Další informace najdete v tématu [Repeatable čte z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
Další informace o klíčových faktorů, které ovlivňují výkon aktivity kopírování a způsoby, jak optimalizovat výkon v [výkonu kopie aktivity a ladění průvodce](data-factory-copy-activity-performance.md).
