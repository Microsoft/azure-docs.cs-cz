---
title: Přesun dat z Teradata pomocí Azure Data Factory
description: Informace o konektoru Teradata pro službu Data Factory, která umožňuje přesun dat z databáze Teradata
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ef992ed907bc070643f290e7fd536de05ebf9242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387202"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Přesun dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-onprem-teradata-connector.md)
> * [Verze 2 (aktuální verze)](../connector-teradata.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [Teradata Connector ve verzi v2](../connector-teradata.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní databáze Teradata. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat Teradata můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesouvání dat z úložiště dat Teradata do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Teradata.

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k místním zdrojům Teradata prostřednictvím brány Správa dat. Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku.

Brána je vyžadována i v případě, že se Teradata hostuje na virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač s IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Aby se Správa dat brána připojovala k databázi Teradata, musíte nainstalovat [rozhraní .net zprostředkovatel dat pro Teradata](https://go.microsoft.com/fwlink/?LinkId=278886) verze 14 nebo vyšší ve stejném systému jako brána Správa dat. Je podporována Teradata verze 12 a vyšší.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního úložiště dat Teradata, najdete v části [JSON example: kopírování dat z Teradata do Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro úložiště dat Teradata:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu Teradata.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesTeradata** . |Yes |
| server |Název serveru Teradata. |Yes |
| authenticationType |Typ ověřování, který se používá pro připojení k databázi Teradata. Možné hodnoty jsou: anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování nebo ověřování systému Windows, zadejte uživatelské jméno. |No |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |No |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní databázi Teradata. |Yes |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. V současné době nejsou podporovány žádné vlastnosti typu pro datovou sadu Teradata.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

V takovém případě se vlastnosti dostupné v části typeProperties v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdrojem typ **RelationalSource** (který zahrnuje Teradata), jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: SELECT * FROM MyTable. |Yes |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Příklad JSON: kopírování dat z Teradata do Azure Blob
Následující příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data z Teradata do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [OnPremisesTeradata](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z výsledků dotazu v databázi Teradata do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba Teradata:**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Propojená služba úložiště objektů BLOB v Azure:**

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

**Vstupní datová sada Teradata:**

Ukázka předpokládá, že jste ve službě Teradata vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "časové razítko" pro data časových řad.

Nastavení "externí": hodnota true informuje službu Data Factory, že je tabulka externí pro objekt pro vytváření dat, a není vytvořená aktivitou v datové továrně.

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
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

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**Kanál s aktivitou kopírování:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění na každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Mapování typů pro Teradata
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat do Teradata se z typu Teradata do typu .NET použijí následující mapování.

| Typ databáze Teradata | Typ rozhraní .NET Framework |
| --- | --- |
| Char |Řetězec |
| Datový typ CLOB |Řetězec |
| Objekty |Řetězec |
| VarChar |Řetězec |
| VarGraphic |Řetězec |
| Objekt blob |Byte [] |
| Byte |Byte [] |
| VarByte |Byte [] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| dvojité |dvojité |
| Integer |Int32 |
| Číslo |dvojité |
| SmallInt |Int16 |
| Date (Datum) |Datum a čas |
| Čas |TimeSpan |
| Čas s časovým pásmem |Řetězec |
| Timestamp |DateTime |
| Časové razítko s časovým pásmem |DateTimeOffset |
| Den intervalu |TimeSpan |
| Každý den v intervalu hodiny |TimeSpan |
| Interval mezi dny a minutou |TimeSpan |
| Druhý den intervalu |TimeSpan |
| Hodina intervalu |TimeSpan |
| Interval – hodina až minuta |TimeSpan |
| Hodina intervalu sekund |TimeSpan |
| Minuta intervalu |TimeSpan |
| Interval – minuta sekunda |TimeSpan |
| Interval sekund |TimeSpan |
| Interval – rok |Řetězec |
| Interval od roku do měsíce |Řetězec |
| Měsíc intervalu |Řetězec |
| Tečka (datum) |Řetězec |
| Tečka (čas) |Řetězec |
| Období (čas s časovým pásmem) |Řetězec |
| Tečka (časové razítko) |Řetězec |
| Tečka (časové razítko s časovým pásmem) |Řetězec |
| XML |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
