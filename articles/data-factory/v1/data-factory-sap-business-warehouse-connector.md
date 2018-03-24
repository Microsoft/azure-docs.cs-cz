---
title: Přesun dat z SAP Business Warehouse pomocí Azure Data Factory | Microsoft Docs
description: Další informace o tom, jak přesunout data z SAP Business Warehouse pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 38c1611c0404202be2e100d3059b4ba1ed1a9236
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Přesun dat z SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-sap-business-warehouse-connector.md)
> * [Verze 2 – Preview](../connector-sap-business-warehouse.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [SAP Business Warehouse konektoru V2](../connector-sap-business-warehouse.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z místní SAP Business Warehouse (BW). Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Z místního úložiště dat SAP Business Warehouse může kopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z SAP Business Warehouse jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat do SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Podporované verze a instalaci
Tento konektor podporuje verze SAP Business Warehouse 7.x. Podporuje kopírování dat z InfoCubes a QueryCubes (včetně BEx dotazy) pomocí dotazů MDX.

Pokud chcete povolit připojení k instanci SAP BW, nainstalujte následující součásti:
- **Brána pro správu dat**: podporuje služby Data Factory připojení k datům místní úložiště (včetně SAP Business Warehouse) pomocí součásti názvem Brána pro správu dat. Další informace o Brána pro správu dat a podrobné pokyny pro nastavení brány najdete v tématu [přesouvání dat mezi místní data uložit do cloudu úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) článku. Vyžaduje se brána, i když SAP Business Warehouse je hostovaná ve virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, dokud brána se může připojit k databázi.
- **Knihovna SAP NetWeaver** na počítači s bránou. Knihovna SAP Netweaver získáte od správce SAP, nebo přímo z [SAP služby Stažení softwaru](https://support.sap.com/swdc). Vyhledejte **&#1025361; Poznámka SAP** získat umístění stahování na nejnovější verzi. Ujistěte se, že architektura pro knihovnu SAP NetWeaver (32bitová nebo 64bitová verze) odpovídá vaší instalace brány. Nainstalujte všechny soubory, které jsou součástí sady SDK SAP NetWeaver RFC podle Poznámka SAP. Knihovna SAP NetWeaver jsou také obsaženy v nástrojích klienta SAP instalace.

> [!TIP]
> Uveďte knihovny DLL do složky system32 extrahována ze sady SDK NetWeaver RFC.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště místní Cassandra data pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data. 
- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený:

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z místní SAP Business Warehouse, najdete v části [JSON příklad: kopírování dat z SAP Business Warehouse do Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat SAP BW:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro SAP Business Warehouse (BW) propojené služby.

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Číslo systému SAP BW systému. | Desetinné číslo letopočty řetězec. | Ano
clientId | ID klienta v systému SAP W klienta. | Tři číslice desítkové číslo řetězec. | Ano
uživatelské jméno | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro uživatele. | řetězec | Ano
gatewayName | Název brány, kterou služba Data Factory měla použít pro připojení k místní instanci SAP BW. | řetězec | Ano
encryptedCredential | Řetězec šifrovaný přihlašovací údaj. | řetězec | Ne

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Nejsou žádné vlastnosti specifické pro typ podporované pro SAP BW datovou sadu typu **RelationalTable**. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky, jsou zásady jsou dostupné pro všechny typy aktivit.

Vzhledem k tomu, vlastnosti dostupné ve **rámci typeProperties** části aktivity se liší podle každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (která zahrnuje SAP BW), následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX číst data z instance SAP BW. | Dotaz MDX. | Ano |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Příklad JSON: kopírování dat z SAP Business Warehouse do objektů Blob v Azure
Následující příklad uvádí ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tento příklad ukazuje, jak ke zkopírování dat z místní SAP Business Warehouse do Azure Blob Storage. Však můžete zkopírovat data **přímo** žádnému jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka obsahuje fragmenty kódu JSON. Podrobné pokyny pro vytvoření objektu pro vytváření dat neobsahuje. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) podrobné pokyny najdete v článku.

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [SapBw](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z instance SAP Business Warehouse do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse propojená služba
Tato propojená služba propojuje SAP BW instanci objektu pro vytváření dat. Vlastnost typ nastavena na **SapBw**. Rámci typeProperties část obsahuje informace o připojení pro SAP BW instanci. 

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
Tato propojená služba propojuje účet úložiště Azure pro vytváření dat. Vlastnost typ nastavena na **azurestorage**. Rámci typeProperties část obsahuje informace o připojení pro účet úložiště Azure.

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

### <a name="sap-bw-input-dataset"></a>Vstupní datové sady SAP BW
Tato datová sada definuje datovou sadu SAP Business Warehouse. Nastavte typ objektu pro vytváření dat datové sady, která **RelationalTable**. V současné době nezadáte jakékoli vlastnosti specifické pro typ pro datové sadě služby SAP BW. Dotaz v definici aktivity kopírování Určuje, jaká data načíst z instance SAP BW. 

Služba Data Factory nastavení externí vlastnost na hodnotu true informuje, že v tabulce je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

Frekvence a intervalu vlastnosti definuje plán. V takovém případě dat je pro čtení z instance SAP BW každou hodinu. 

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
Tato datová sada definuje výstupní datovou sadu objektu Blob Azure. Vlastnost typ nastavena na hodnotu AzureBlob. V rámci typeProperties části poskytuje, které jsou uložená data zkopírovány z instance SAP BW. Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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


### <a name="pipeline-with-copy-activity"></a>Kanál s aktivitou kopírování
Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** (pro SAP BW zdroj) a **podřízený** je typ nastaven na **BlobSink**. Dotaz zadaný pro **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

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



### <a name="type-mapping-for-sap-bw"></a>Mapování typu pro SAP BW
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesouvání dat od SAP BW, se používají následující mapování z typů SAP BW na typy .NET.

Typ dat ve slovníku ABAP | Datový typ rozhraní .net
-------------------------------- | --------------
ACCP |  Int
CHAR – | Řetězec
CLNT | Řetězec
AKTUÁLNÍ | Decimal
CUKY | Řetězec
DEC | Decimal
FLTP | Dvojitý
INT1 | Bajtů
INT2 | Int16
INT4 | Int
JAZYK | Řetězec
LCHR | Řetězec
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
NEZPRACOVANÁ | Byte[]
RAWSTRING | Byte[]
ŘETĚZEC | Řetězec
JEDNOTKA | Řetězec
SOUBORY DAT | Řetězec
NUMC | Řetězec
TIMS | Řetězec

> [!NOTE]
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relační zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
