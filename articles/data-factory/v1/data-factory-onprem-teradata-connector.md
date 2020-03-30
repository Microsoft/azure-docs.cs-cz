---
title: Přesunutí dat z Teradata pomocí Azure Data Factory
description: Informace o konektoru Teradata pro službu Data Factory, která umožňuje přesun dat z databáze Teradata
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ecde5784e759ef5259b8c67ed574cef6cae98f30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281194"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>Přesunutí dat z Teradata pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-onprem-teradata-connector.md)
> * [Verze 2 (aktuální verze)](../connector-teradata.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, viz [Konektor Teradata ve V2](../connector-teradata.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místní databáze Teradata. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Můžete zkopírovat data z místního úložiště dat Teradata do libovolného úložiště dat podporované jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory aktuálně podporuje pouze přesunutí dat z úložiště dat Teradata do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat Teradata.

## <a name="prerequisites"></a>Požadavky
Data Factory podporuje připojení k místním zdrojům Teradata prostřednictvím brány pro správu dat. Podívejte [se na pohybující se data mezi místními lokacemi a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem, kde najdete informace o bráně pro správu dat a podrobné pokyny k nastavení brány.

Brána je vyžadována i v případě, že Teradata je hostovaný ve virtuálním počítači Azure IaaS. Bránu můžete nainstalovat na stejný virtuální počítač IaaS jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.

> [!NOTE]
> Tipy týkající se řešení problémů s připojením nebo bránou najdete v [tématu Poradce při potížích](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) s bránou.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Aby se brána pro správu dat připojila k databázi Teradata, musíte nainstalovat [zprostředkovatele dat .NET pro teradata](https://go.microsoft.com/fwlink/?LinkId=278886) verze 14 nebo vyšší do stejného systému jako brána pro správu dat. Teradata verze 12 a vyšší je podporována.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Data Factory, které se používají ke kopírování dat z místního úložiště dat Teradata, najdete v [tématu JSON příklad: Kopírování dat z Teradata do Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro úložiště dat Teradata:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu Teradata.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena **na: OnPremisesTeradata** |Ano |
| server |Název serveru Teradata. |Ano |
| authenticationType |Typ ověřování používaný pro připojení k databázi Teradata. Možné hodnoty jsou: Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Zadejte uživatelské jméno, pokud používáte základní ověřování nebo ověřování systému Windows. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| název brány |Název brány, kterou by měla služba Data Factory použít k připojení k místní databázi Teradata. |Ano |

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. V současné době nejsou podporovány žádné vlastnosti typu pro datovou sadu Teradata.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v typeProperties části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud je zdroj typu **RelationalSource** (který zahrnuje Teradata), jsou v části **typeProperties** k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |Řetězec dotazu SQL. Příklad: vyberte * z MyTable. |Ano |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>Příklad JSON: Kopírování dat z Teradata do objektu Blob Azure
Následující příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data z Teradata do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující entity datové továrny:

1. Propojená služba typu [OnPremisesTeradata](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [Relační zdroj](#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v databázi Teradata do objektu blob každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

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

**Propojená služba úložiště objektů blob Azure:**

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

Ukázka předpokládá, že jste vytvořili tabulku "MyTable" v Teradata a obsahuje sloupec s názvem "časové razítko" pro data časových řad.

Nastavení "externí": true informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánováno na spouštění každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

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
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) aktivita Copy provádí automatické převody typů z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat do Teradata se používají následující mapování z typu Teradata na typ .NET.

| Typ databáze Teradata | Typ rozhraní .NET Framework |
| --- | --- |
| Char |Řetězec |
| Clob |Řetězec |
| Grafické |Řetězec |
| Varchar |Řetězec |
| VarGrafika |Řetězec |
| Objekt blob |Bajt[] |
| Byte |Bajt[] |
| VarByte |Bajt[] |
| Bigint |Int64 |
| Bajt |Int16 |
| Desetinné číslo |Desetinné číslo |
| Double |Double |
| Integer |Int32 |
| Číslo |Double |
| Smallint |Int16 |
| Datum |DateTime |
| Time |TimeSpan |
| Čas s časovým pásmem |Řetězec |
| Časové razítko |DateTime |
| Časové razítko s časovým pásmem |DateTimeOffset |
| Den intervalu |TimeSpan |
| Interval den od hodiny |TimeSpan |
| Interval den na minutu |TimeSpan |
| Interval den až druhý |TimeSpan |
| Intervalová hodina |TimeSpan |
| Interval od hodiny do minuty |TimeSpan |
| Interval hodinu až sekunda |TimeSpan |
| Intervalová minuta |TimeSpan |
| Interval minuta až sekunda |TimeSpan |
| Interval druhý |TimeSpan |
| Intervalový rok |Řetězec |
| Interval rok od měsíce |Řetězec |
| Interval měsíc |Řetězec |
| Období (datum) |Řetězec |
| Období (čas) |Řetězec |
| Období (čas s časovým pásmem) |Řetězec |
| Období (časové razítko) |Řetězec |
| Období (časové razítko s časovým pásmem) |Řetězec |
| XML |Řetězec |

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
