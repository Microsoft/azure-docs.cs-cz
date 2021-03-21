---
title: Přesun dat z SAP HANA pomocí Azure Data Factory
description: Přečtěte si informace o tom, jak přesouvat data z SAP HANA pomocí Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 9c192a5f84f36fbdea0dd1d280928aa40186d4ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100386981"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Přesun dat z SAP HANA pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-sap-hana-connector.md)
> * [Verze 2 (aktuální verze)](../connector-sap-hana.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor SAP HANA v v2](../connector-sap-business-warehouse.md).

Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místní SAP HANA. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z místního úložiště dat SAP HANA můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesun dat z SAP HANA do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do SAP HANA.

## <a name="supported-versions-and-installation"></a>Podporované verze a instalace
Tento konektor podporuje jakoukoli verzi SAP HANA databáze. Podporuje kopírování dat z datových modelů HANA (například analytických a výpočetních zobrazení) a tabulek řádků a sloupců pomocí dotazů SQL.

Pokud chcete povolit připojení k instanci SAP HANA, nainstalujte následující komponenty:
- **Správa dat brána**: Služba Data Factory podporuje připojení k místním úložištím dat (včetně SAP HANA) pomocí komponenty s názvem Správa dat Gateway. Další informace o Správa dat brány a podrobné pokyny k nastavení brány najdete v článku [přesun dat mezi místním úložištěm dat do cloudového úložiště dat](data-factory-move-data-between-onprem-and-cloud.md) . Brána je povinná i v případě, že je SAP HANA hostovaná na virtuálním počítači Azure IaaS (VM). Bránu můžete nainstalovat na stejný virtuální počítač jako úložiště dat nebo na jiný virtuální počítač, pokud se brána může připojit k databázi.
- **SAP HANA ovladač ODBC** na počítači brány. Ovladač SAP HANA ODBC si můžete stáhnout z webu [SAP Software Download Center](https://support.sap.com/swdc). Vyhledejte pomocí klíčového slova **SAP HANA klienta pro Windows**. 

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z místního SAP HANA úložiště dat pomocí různých nástrojů/rozhraní API. 

- Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) . 
- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování. 
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup. 

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z místního SAP HANA, najdete v části [JSON example: kopírování dat z SAP HANA do objektu blob Azure](#json-example-copy-data-from-sap-hana-to-azure-blob) v tomto článku. 

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro SAP HANA úložiště dat:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro SAP HANA propojenou službu.

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
server | Název serveru, na kterém se nachází instance SAP HANA. Pokud váš server používá přizpůsobený port, zadejte `server:port` . | řetězec | Yes
authenticationType | Typ ověřování. | . "Basic" nebo "Windows" | Yes 
username | Jméno uživatele, který má přístup k serveru SAP | řetězec | Yes
heslo | Heslo pro tohoto uživatele. | řetězec | Yes
gatewayName | Název brány, kterou by služba Data Factory měla použít pro připojení k místní instanci SAP HANA | řetězec | Yes
encryptedCredential | Šifrovaný řetězec přihlašovacích údajů. | řetězec | No

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Pro SAP HANA datovou sadu **relačních** objektů typu není podporována žádná vlastnost specifická pro typ. 


## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Vlastnosti, jako je název, popis, vstupní a výstupní tabulky, jsou zásady dostupné pro všechny typy aktivit.

V takovém případě se vlastnosti dostupné v části **typeProperties** v aktivitě liší podle typu aktivity. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (který zahrnuje SAP HANA), jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| query | Určuje dotaz SQL pro čtení dat z instance SAP HANA. | Dotaz SQL. | Yes |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>Příklad JSON: kopírování dat z SAP HANA do objektu blob Azure
Následující ukázka poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). V této ukázce se dozvíte, jak kopírovat data z místního SAP HANA do Azure Blob Storage. Data je ale možné zkopírovat **přímo** do kterékoli z těchto [umyvadel pomocí aktivity](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopírování v Azure Data Factory.  

> [!IMPORTANT]
> Tato ukázka poskytuje fragmenty kódu JSON. Nezahrnuje podrobné pokyny k vytvoření datové továrny. Podrobné pokyny najdete v článku [přesun dat mezi místními umístěními a v cloudu](data-factory-move-data-between-onprem-and-cloud.md) .

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [SapHana](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z instance SAP HANA do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>Propojená služba SAP HANA
Tato propojená služba propojuje vaši instanci SAP HANA s datovou továrnou. Vlastnost Type je nastavená na **SapHana**. Část typeProperties poskytuje informace o připojení pro instanci SAP HANA.

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

### <a name="sap-hana-input-dataset"></a>Vstupní datová sada SAP HANA

Tato datová sada definuje SAP HANA datovou sadu. Můžete nastavit typ datové sady Data Factory na **relační**. V současné době neurčíte žádné vlastnosti specifické pro typ SAP HANA DataSet. Dotaz v definici aktivity kopírování určuje, jaká data se mají číst z instance SAP HANA. 

Nastavením vlastnosti External na hodnotu true dojde k informování služby Data Factory, že je tabulka z objektu pro vytváření dat externá a není vytvořená aktivitou v datové továrně.

Vlastnosti četnosti a intervalu definují plán. V tomto případě se data čtou z SAP HANA instance každou hodinu. 

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
Tato datová sada definuje výstupní datovou sadu Azure Blob. Vlastnost Type je nastavená na Azureblobu. Část typeProperties poskytuje, kde jsou uložena data zkopírovaná z SAP HANA instance. Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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


### <a name="pipeline-with-copy-activity"></a>Kanál s aktivitou kopírování

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** (pro zdroj SAP HANA) a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat z SAP HANA se z SAP HANA typů do typů .NET používají následující mapování.

Typ SAP HANA | Typ založený na .NET
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REÁLNÉ | Jednoduché
KLEPAT | Jednoduché
NOTACI | Decimal
DATOVÉHO | Byte
VARCHAR | Řetězec
NVARCHAR | Řetězec
DATOVÝ typ CLOB | Byte []
ALPHANUM | Řetězec
PŘÍZNAKY | Byte []
DATE | DateTime
ČAS | TimeSpan
ČASOVÉ RAZÍTKO | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Známá omezení
Při kopírování dat z SAP HANA existuje několik známých omezení:

- U řetězce NVARCHAR je maximální délka omezená na 4000 znaků Unicode.
- Nepodporuje se SMALLDECIMAL.
- Nepodporuje se VARBINARY.
- Platná data jsou mezi 30. 12. 1899 a 31. 12. 9999.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Zobrazit [opakující se čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
