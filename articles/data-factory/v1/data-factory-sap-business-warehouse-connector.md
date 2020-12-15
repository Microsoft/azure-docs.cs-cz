---
title: Přesun dat ze SAP Business Warehouse pomocí Azure Data Factory
description: Přečtěte si, jak přesunout data ze SAP Business Warehouse pomocí Azure Data Factory.
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
ms.openlocfilehash: fb91a09ed31658c2d547a7b46cf2f986bfbd0e50
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508284"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Přesun dat ze SAP Business Warehouse pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-sap-business-warehouse-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SAP Business Warehouse ve verzi v2](../connector-sap-business-warehouse.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místního skladu SAP Business Warehouse (ČERNOBÍLý). Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat SAP Business Warehouse můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje jenom přesun dat z SAP Business Warehouse do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor podporuje SAP Business Warehouse verze 7. x. Podporuje kopírování dat z InfoCubes a QueryCubes (včetně BEx dotazů) pomocí dotazů MDX.

Pokud chcete povolit připojení k instanci SAP BW, nainstalujte následující komponenty:
- **Správa dat Gateway**: Služba Data Factory podporuje připojení k místním úložištím dat (včetně SAP Business Warehouse) pomocí komponenty s názvem Správa dat Gateway. Další informace o Správa dat brány a podrobné pokyny k nastavení brány najdete v článku [přesun dat mezi místním úložištěm dat do cloudového úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) . Brána se vyžaduje i v případě, že je SAP Business Warehouse hostovaný na virtuálním počítači Azure s IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.
- **Knihovna SAP NetWeaver** na počítači brány. Knihovnu SAP NetWeaver Library můžete získat od správce SAP nebo přímo z [webu SAP software Download Center](https://support.sap.com/swdc). Vyhledejte **#1025361 poznámky SAP** a získejte umístění pro stažení nejnovější verze. Ujistěte se, že architektura knihovny SAP NetWeaver (32-bit nebo 64) odpovídá instalaci brány. Pak nainstalujte všechny soubory zahrnuté v sadě SAP NetWeaver RFC SDK podle poznámky SAP. Knihovna SAP NetWeaver Library je také součástí instalace klientských nástrojů SAP.

> [!TIP]
> Vložte knihovny DLL extrahované z NetWeaver RFC SDK do složky System32.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního úložiště dat Cassandra pomocí různých nástrojů nebo rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) . 
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Entity Data Factory, které se používají ke kopírování dat z místního datového skladu SAP, najdete v části [JSON example: kopírování dat z SAP Business Warehouse do Azure Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) tohoto článku. 

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro SAP BW úložiště dat:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu SAP Business Warehouse (ČERNOBÍLý).

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP BW. | řetězec | Ano
systemNumber | Číslo systému SAP BW systému | Desítkové číslo se dvěma číslicemi reprezentované jako řetězec. | Ano
clientId | ID klienta klienta v systému SAP W. | Desítkové číslo se třemi číslicemi reprezentované jako řetězec. | Ano
username | Jméno uživatele, který má přístup k serveru SAP | řetězec | Ano
heslo | Heslo pro tohoto uživatele. | řetězec | Ano
gatewayName | Název brány, kterou by služba Data Factory měla použít pro připojení k místní instanci SAP BW | řetězec | Ano
encryptedCredential | Šifrovaný řetězec přihlašovacích údajů. | řetězec | No

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Pro SAP BW datovou sadu **relačních** objektů typu není podporována žádná vlastnost specifická pro typ. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady dostupné pro všechny typy aktivit.

V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (který zahrnuje SAP BW), jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query | Určuje dotaz MDX pro čtení dat z instance SAP BW. | Dotaz MDX. | Ano |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Příklad JSON: kopírování dat ze SAP Business Warehouse do Azure Blob
Následující příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). V této ukázce se dozvíte, jak kopírovat data z místního skladu SAP Business Warehouse do Azure Blob Storage. Data se ale dají zkopírovat **přímo** do kterékoli z těchto umyvadel, které jsou [tady](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedené, pomocí aktivity kopírování v Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [SapBw](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z instance SAP Business Warehouse do objektu blob Azure za hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Propojená služba SAP Business Warehouse
Tato propojená služba propojuje vaši instanci SAP BW s datovou továrnou. Vlastnost Type je nastavená na **SapBw**. Část typeProperties poskytuje informace o připojení pro instanci SAP BW. 

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
Tato propojená služba propojuje váš Azure Storage účet s datovou továrnou. Vlastnost Type je nastavená na **AzureStorage**. Část typeProperties poskytuje informace o připojení pro účet Azure Storage.

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
Tato datová sada definuje datovou sadu SAP Business Warehouse. Můžete nastavit typ datové sady Data Factory na **relační**. V současné době neurčíte žádné vlastnosti specifické pro typ SAP BW DataSet. Dotaz v definici aktivity kopírování určuje, jaká data se mají číst z instance SAP BW. 

Nastavením vlastnosti External na hodnotu true dojde k informování služby Data Factory, že je tabulka z objektu pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

Vlastnosti četnosti a intervalu definují plán. V tomto případě se data čtou z SAP BW instance každou hodinu. 

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
Tato datová sada definuje výstupní datovou sadu Azure Blob. Vlastnost Type je nastavená na Azureblobu. Část typeProperties poskytuje, kde jsou uložena data zkopírovaná z SAP BW instance. Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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
Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** (pro zdroj SAP BW) a typ **jímky** je nastavený na **BlobSink**. Dotaz zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat z SAP BW se z SAP BW typů do typů .NET používají následující mapování.

Datový typ ve slovníku ABAP | .NET – datový typ
-------------------------------- | --------------
ACCP |  Int
CHAR | Řetězec
CLNT | Řetězec
CURR | Decimal
CUKY | Řetězec
18.12 | Decimal
FLTP | dvojité
INT1 | Byte
INT2 | Int16
INT4 | Int
JAZYK | Řetězec
LCHR | Řetězec
LRAW | Byte []
PREC | Int16
QUAN | Decimal
ZÍSKÁNÍ | Byte []
RAWSTRING | Byte []
ŘETEZCE | Řetězec
JEDNOTCE | Řetězec
DEFINIC | Řetězec
NUMC | Řetězec
TIMS | Řetězec

> [!NOTE]
> Chcete-li mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Zobrazit [opakující se čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
