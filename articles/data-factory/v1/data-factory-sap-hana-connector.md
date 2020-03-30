---
title: Přesunutí dat ze SAP HANA pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data ze SAP HANA pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 361b98a1cde8ee5dee99a370b46d8fc8e0f5af28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265815"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Přesunutí dat ze SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-sap-hana-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sap-hana.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [KONEKTOR SAP HANA ve verzi V2](../connector-sap-business-warehouse.md).

Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místníSAP HANA. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat SAP HANA můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Factory aktuálně podporuje pouze přesunutí dat z SAP HANA do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do SAP HANA.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor podporuje všechny verze databáze SAP HANA. Podporuje kopírování dat z informačních modelů HANA (například zobrazení Analytica a Výpočt) a tabulek řádků a sloupců pomocí dotazů SQL.

Chcete-li povolit připojení k instanci SAP HANA, nainstalujte následující součásti:
- **Brána pro správu dat**: Služba Data Factory podporuje připojení k místním úložištím dat (včetně SAP HANA) pomocí komponenty s názvem Brána pro správu dat. Další informace o bráně pro správu dat a podrobných pokynech pro nastavení brány najdete v článku Přesunutí dat mezi místním úložištěm dat do článku [úložiště cloudových dat.](data-factory-move-data-between-onprem-and-cloud.md) Brána je vyžadována i v případě, že SAP HANA je hostovaný ve virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.
- **Sap HANA OVLADAČ ODBC** v počítači brány. Ovladač SAP HANA ODBC si můžete stáhnout z webu [SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte klíčové slovo **SAP HANA CLIENT pro Windows**. 

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data z místního úložiště dat SAP HANA pomocí různých nástrojů nebo api. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat. 
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat z místní SAP HANA, najdete v [tématu JSON příklad: Kopírování dat z SAP HANA do Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) části tohoto článku. 

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro úložiště dat SAP HANA:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu SAP HANA.

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém je umístěna instance SAP HANA. Pokud server používá vlastní port, `server:port`zadejte . | řetězec | Ano
authenticationType | Typ ověřování. | . "Základní" nebo "Windows" | Ano 
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
název brány | Název brány, kterou by měla služba Data Factory použít k připojení k místní instanci SAP HANA. | řetězec | Ano
šifrované pověření | Šifrovaný řetězec pověření. | řetězec | Ne

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Pro datovou sadu SAP HANA typu **RelationalTable**nejsou podporovány žádné vlastnosti specifické pro daný typ . 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud zdroj v aktivitě kopírování je typu **RelationalSource** (který zahrnuje SAP HANA), následující vlastnosti jsou k dispozici v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Dotaz SQL. | Ano |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Příklad JSON: Kopírování dat z SAP HANA do objektu blob Azure
Následující ukázka obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tato ukázka ukazuje, jak zkopírovat data z místní SAP HANA do úložiště objektů blob Azure. Data však můžete zkopírovat **přímo** do některého z jímky uvedené [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

1. Propojená služba typu [SapHana](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [Relační zdroj](#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z instance SAP HANA do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

### <a name="sap-hana-linked-service"></a>Propojená služba SAP HANA
Tato propojená služba propojuje vaši instanci SAP HANA s továrnou na data. Vlastnost type je nastavena na **SapHana**. Oddíl typeProperties obsahuje informace o připojení pro instanci SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Tato propojená služba propojuje váš účet Azure Storage s toto datové továrně. Vlastnost type je nastavena na **AzureStorage**. Část typeProperties obsahuje informace o připojení pro účet Úložiště Azure.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>Vstupní datová sada SAP HANA

Tato datová sada definuje datovou sadu SAP HANA. Nastavíte typ datové sady Data Factory na **RelationalTable**. V současné době nezadáte žádné vlastnosti specifické pro typ pro datovou sadu SAP HANA. Dotaz v definici aktivity kopírování určuje, jaká data se mají číst z instance SAP HANA. 

Nastavení externí vlastnosti na hodnotu true informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vytvořena aktivitou v datové továrně.

Vlastnosti frekvence a intervalu definují plán. V tomto případě jsou data čtena z instance SAP HANA každou hodinu. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob
Tato datová sada definuje výstupní datovou sadu objektů blob Azure. Vlastnost type je nastavena na AzureBlob. Oddíl typeProperties uvádí, kde jsou uložena data zkopírovaná z instance SAP HANA. Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Kanál s aktivitou Kopírování

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **Relační zdroj** (pro zdroj SAP HANA) a typ **jímky** je nastaven na **objekt BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>Mapování typů pro SAP HANA
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat z SAP HANA se používají následující mapování z typů SAP HANA na typy .NET.

Sap HANA typ | Typ založený na rozhraní .NET
------------- | ---------------
Tinyint | Byte
Smallint | Int16
INT | Int32
Bigint | Int64
REÁLNÉ | Single
Dvojité | Single
Desetinných | Desetinné číslo
Boolean | Byte
Varchar | Řetězec
Nvarchar | Řetězec
Clob | Bajt[]
ALFANUM | Řetězec
Blob | Bajt[]
DATE (Datum) | DateTime
ČAS | TimeSpan
Časové razítko | DateTime
DRUHÉ DATUM | DateTime

## <a name="known-limitations"></a>Známá omezení
Existuje několik známých omezení při kopírování dat ze SAP HANA:

- U řetězce NVARCHAR je maximální délka omezená na 4000 znaků Unicode.
- Nepodporuje se SMALLDECIMAL.
- Nepodporuje se VARBINARY.
- Platná data jsou mezi 30. 12. 1899 a 31. 12. 9999.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení ze relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
