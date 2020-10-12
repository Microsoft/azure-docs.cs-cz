---
title: Přesunout data ze zdrojů OData
description: Přečtěte si, jak přesunout data ze zdrojů OData pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 95f92d4e5616d7754c355610685701a8e089b84e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85847565"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Přesunutí dat ze zdroje OData pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-odata-connector.md)
> * [Verze 2 (aktuální verze)](../connector-odata.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor OData ve verzi v2](../connector-odata.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data ze zdroje OData. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data ze zdroje OData můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesun dat ze zdroje OData do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do zdroje OData.

## <a name="supported-versions-and-authentication-types"></a>Podporované verze a typy ověřování
Tento konektor OData podporuje OData verze 3,0 a 4,0 a můžete kopírovat data z cloudových OData i místních zdrojů OData. V takovém případě je potřeba nainstalovat bránu Správa dat. Podrobnosti o Správa dat bráně najdete v tématu [přesun dat mezi místním a cloudovým](data-factory-move-data-between-onprem-and-cloud.md) článkem.

Níže jsou podporované typy ověřování:

* Pro přístup ke **cloudu** OData můžete použít anonymní, základní (uživatelské jméno a heslo) nebo Azure Active Directory ověřování OAuth založené na.
* Pro přístup k **místnímu** datovému kanálu OData můžete použít anonymní, základní (uživatelské jméno a heslo) nebo ověřování systému Windows.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data ze zdroje OData pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API**a **REST API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON.  Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat ze zdroje OData, najdete v části [JSON example: kopírování dat ze zdroje OData do objektu blob Azure](#json-example-copy-data-from-odata-source-to-azure-blob) v tomto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro zdroj OData:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu OData.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OData** . |Yes |
| url |Adresa URL služby OData. |Yes |
| authenticationType |Typ ověřování, který se používá pro připojení ke zdroji OData <br/><br/> Pro Cloud OData jsou možné hodnoty anonymní, základní a OAuth (Poznámka Azure Data Factory aktuálně podporuje jenom Azure Active Directory OAuth). <br/><br/> Pro místní OData jsou možné hodnoty anonymní, základní a Windows. |Yes |
| username |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| authorizedCredential |Pokud používáte OAuth, klikněte na tlačítko **autorizovat** v průvodci kopírováním Data Factory nebo v editoru a zadejte své přihlašovací údaje. hodnota této vlastnosti se vygeneruje automaticky. |Ano (jenom v případě, že používáte ověřování OAuth) |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k místní službě OData. Zadejte pouze v případě, že kopírujete data z místního zdroje OData. |No |

### <a name="using-basic-authentication"></a>Použití základního ověřování
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Použití anonymního ověřování
```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Použití ověřování systému Windows při přístupu k místnímu zdroji OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Použití ověřování OAuth při přístupu ke cloudové zdroji OData
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source e.g. https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu typu **ODataResource** (která zahrnuje datovou sadu OData) má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| program |Cesta k prostředku OData |No |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

Vlastnosti, které jsou k dispozici v části typeProperties aktivity, se liší v závislosti na jednotlivých typech aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **RelationalSource** (který zahrnuje OData), jsou v oddílu typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Příklad | Vyžadováno |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |"? $select = název, popis&$top = 5" |No |

## <a name="type-mapping-for-odata"></a>Mapování typů pro OData
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky.

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat z OData se z typů OData do typu .NET používají následující mapování.

| Datový typ OData | Typ .NET |
| --- | --- |
| EDM. Binary |Byte [] |
| Edm.Boolean |Logická hodnota |
| EDM. Byte |Byte [] |
| EDM. DateTime |DateTime |
| EDM. Decimal |Decimal |
| Edm.Double |dvojité |
| EDM. Single |Jednoduché |
| EDM. GUID |Identifikátor GUID |
| EDM. Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| EDM. SByte |Int16 |
| Edm.String |Řetězec |
| EDM. time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Komplexní datové typy OData, např. objekt, nejsou podporovány.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Příklad JSON: kopírování dat ze zdroje OData do Azure Blob
Tento příklad poskytuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak kopírovat data ze zdroje OData do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory. Ukázka má následující Data Factory entit:

1. Propojená služba typu [OData](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z dotazu na zdroj OData do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

**Propojená Služba OData:** V tomto příkladu se používá anonymní ověřování. Různé typy ověřování, které můžete použít, najdete v části [propojená Služba OData](#linked-service-properties) .

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

**Propojená služba Azure Storage:**

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

**Vstupní datová sada OData:**

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "typeProperties":
        {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Zadání **cesty** v definici datové sady je volitelné.

**Výstupní datová sada Azure Blob:**

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

```json
{
    "name": "AzureBlobODataDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Aktivita kopírování v kanálu se zdrojem a jímkou objektů a dat OData:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere nejnovější (nejnovější) data ze zdroje OData.

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "?$select=Name, Description&$top=5",
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "ODataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobODataDataSet"
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
                "name": "ODataToBlob"
            }
        ],
        "start": "2017-02-01T18:00:00Z",
        "end": "2017-02-03T19:00:00Z"
    }
}
```

Zadání **dotazu** v definici kanálu je volitelné. **Adresa URL** , kterou služba Data Factory používá k načtení dat, je: adresa URL zadaná v propojené službě (povinné) + Cesta zadaná v datové sadě (volitelné) + dotaz v kanálu (volitelné).

### <a name="type-mapping-for-odata"></a>Mapování typů pro OData
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím přístupem ke dvěma krokům:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesunu dat z úložišť dat OData jsou datové typy OData namapovány na typy .NET.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .
