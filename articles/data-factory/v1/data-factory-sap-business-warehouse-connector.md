---
title: Přesunutí dat z obchodního skladu SAP pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data z SAP Business Warehouse pomocí Azure Data Factory.
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
ms.openlocfilehash: 108bdf057cd375e28b10a6838ec5c8c6f57749a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281051"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Přesunutí dat z obchodního skladu SAP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-sap-business-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SAP Business Warehouse ve verzi V2](../connector-sap-business-warehouse.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat z místního SAP Business Warehouse (BW). Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat SAP Business Warehouse můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Data Factory aktuálně podporuje pouze přesunutí dat z obchodního skladu SAP do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do obchodního skladu SAP. 

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor podporuje SAP Business Warehouse verze 7.x. Podporuje kopírování dat z InfoCubes a QueryCubes (včetně dotazů BEx) pomocí dotazů MDX.

Chcete-li povolit připojení k instanci SAP BW, nainstalujte následující součásti:
- **Brána pro správu dat**: Služba Data Factory podporuje připojení k místním úložištím dat (včetně SAP Business Warehouse) pomocí komponenty s názvem Brána pro správu dat. Další informace o bráně pro správu dat a podrobných pokynech pro nastavení brány najdete v článku Přesunutí dat mezi místním úložištěm dat do článku [úložiště cloudových dat.](data-factory-move-data-between-onprem-and-cloud.md) Brána je povinná i v případě, že SAP Business Warehouse je hostovaný ve virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.
- **Knihovna SAP NetWeaver** v počítači brány. Knihovnu SAP Netweaver můžete získat od správce SAP nebo přímo ze [služby SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte **poznámku SAP #1025361** a získejte umístění pro stažení nejnovější verze. Ujistěte se, že architektura pro knihovnu SAP NetWeaver (32bitová nebo 64bitová) odpovídá instalaci brány. Poté nainstalujte všechny soubory obsažené v sadě SAP NetWeaver RFC SDK podle poznámky SAP. Knihovna SAP NetWeaver je také součástí instalace klientských nástrojů SAP.

> [!TIP]
> Vložte knihovny DLL extrahované ze sady NetWeaver RFC SDK do složky system32.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat. 
- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity. 

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat z místního obchodního skladu SAP, najdete v [tématu JSON příklad: Kopírování dat z SAP Business Warehouse do Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) části tohoto článku. 

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro úložiště dat SAP BW:

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu SAP Business Warehouse (BW).

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Systémové číslo systému SAP BW. | Dvoumístné desetinné číslo reprezentované jako řetězec. | Ano
clientId | ID klienta v systému SAP W. | Třímístné desetinné číslo reprezentované jako řetězec. | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
název brány | Název brány, kterou by měla služba Data Factory použít k připojení k místní instanci SAP BW. | řetězec | Ano
šifrované pověření | Šifrovaný řetězec pověření. | řetězec | Ne

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Pro datovou sadu SAP BW typu **RelationalTable**nejsou podporovány žádné vlastnosti specifické pro daný typ . 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady jsou k dispozici pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s každým typem aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud zdroj v aktivitě kopírování je typu **RelationalSource** (který zahrnuje SAP BW), následující vlastnosti jsou k dispozici v části typeProperties:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | dotaz u MDX. | Ano |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Příklad JSON: Kopírování dat z obchodního skladu SAP do objektu Blob Azure
Následující příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tato ukázka ukazuje, jak zkopírovat data z místního SAP Business Warehouse do úložiště objektů blob Azure. Data však můžete zkopírovat **přímo** do některého z jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka obsahuje úryvky JSON. Neobsahuje podrobné pokyny pro vytvoření datové továrny. Podrobné pokyny najdete v článku [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

Ukázka má následující entity datové továrny:

1. Propojená služba typu [SapBw](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [Relační zdroj](#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z instance SAP Business Warehouse do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

Jako první krok nastavte bránu pro správu dat. Pokyny jsou v [přesunutí dat mezi místními umístěními a článkem cloudu.](data-factory-move-data-between-onprem-and-cloud.md)

### <a name="sap-business-warehouse-linked-service"></a>Propojená služba SAP Business Warehouse
Tato propojená služba propojuje vaši instanci SAP BW s továrnou na data. Vlastnost type je nastavena na **SapBw**. Část typeProperties obsahuje informace o připojení pro instanci SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

### <a name="sap-bw-input-dataset"></a>Vstupní datová sada SAP BW
Tato datová sada definuje datovou sadu SAP Business Warehouse. Nastavíte typ datové sady Data Factory na **RelationalTable**. V současné době nezadáte žádné vlastnosti specifické pro typ pro datovou sadu SAP BW. Dotaz v definici aktivity kopírování určuje, jaká data se mají číst z instance SAP BW. 

Nastavení externí vlastnosti na hodnotu true informuje službu Data Factory, že tabulka je externí pro datovou továrnu a není vytvořena aktivitou v datové továrně.

Vlastnosti frekvence a intervalu definují plán. V tomto případě jsou data čtena z instance SAP BW každou hodinu. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Tato datová sada definuje výstupní datovou sadu objektů blob Azure. Vlastnost type je nastavena na AzureBlob. Oddíl typeProperties uvádí, kde jsou uložena data zkopírovaná z instance SAP BW. Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **Relační zdroj** (pro zdroj SAP BW) a typ **jímky** je nastaven na **objekt BlobSink**. Dotaz zadaný pro vlastnost **dotazu** vybere data za poslední hodinu ke kopírování.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>Mapování typů pro SAP BW
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat z SAP BW se používají následující mapování z typů SAP BW na typy .NET.

Datový typ ve slovníku ABAP | Datový typ .NET
-------------------------------- | --------------
Accp |  Int
Char | Řetězec
Clnt | Řetězec
CURR | Desetinné číslo
CUKY | Řetězec
Prosince | Desetinné číslo
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
Lang | Řetězec
LCHR | Řetězec
LRAW | Bajt[]
PŘEDŠkolní hod | Int16
Quan | Desetinné číslo
Syrové | Bajt[]
RAWSTRING | Bajt[]
Řetězec | Řetězec
Jednotka | Řetězec
Dat | Řetězec
SPOLEČNOST NUMC | Řetězec
Tims | Řetězec

> [!NOTE]
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení ze relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
