---
title: Přesunutí dat ze zdrojů OData
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265906"
---
# <a name="move-data-from-an-odata-source-using-azure-data-factory"></a>Přesunutí dat ze zdroje OData pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-odata-connector.md)
> * [Verze 2 (aktuální verze)](../connector-odata.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [téma Konektor OData ve verzi 2](../connector-odata.md).


Tento článek vysvětluje, jak použít aktivitu kopírování v Azure Data Factory k přesunutí dat ze zdroje OData. Vychází z článku [Aktivity přesunu dat,](data-factory-data-movement-activities.md) který představuje obecný přehled přesunu dat s aktivitou kopírování.

Data ze zdroje OData můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako jímky naleznete v tabulce [Podporovaná úložiště dat.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Factory aktuálně podporuje pouze přesunutí dat ze zdroje OData do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do zdroje OData.

## <a name="supported-versions-and-authentication-types"></a>Podporované verze a typy ověřování
Tento konektor OData podporuje OData verze 3.0 a 4.0 a můžete kopírovat data z cloudových OData i z místních zdrojů OData. Pro druhé, je třeba nainstalovat bránu pro správu dat. Podrobnosti o bráně pro správu dat najdete v článku [Přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) články.

Níže uvedené typy ověřování jsou podporovány:

* Pro přístup ke **cloudovému** kanálu OData můžete použít anonymní, základní (uživatelské jméno a heslo) nebo ověřování OAuth založené na Službě Azure Active Directory.
* Pro přístup **k místnímu informačnímu** kanálu OData můžete použít anonymní, základní (uživatelské jméno a heslo) nebo ověřování systému Windows.

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesune data ze zdroje OData pomocí různých nástrojů nebo api.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním**. Viz [Kurz: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) pro rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat.

K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **Šablona Azure Resource Manager**, Rozhraní **.NET API**a REST **API**. Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v tématu [Kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aktivity.

Bez ohledu na to, zda používáte nástroje nebo api, provedete následující kroky k vytvoření kanálu, který přesune data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** pro propojení vstupních a výstupních úložišť dat s vaší továrně dat.
2. Vytvořte **datové sady** představující vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup.

Při použití průvodce jsou automaticky vytvořeny definice JSON pro tyto entity Data Factory (propojené služby, datové sady a kanál). Při použití nástrojů nebo rozhraní API (s výjimkou rozhraní .NET API) definujete tyto entity Data Factory pomocí formátu JSON.  Ukázka s definicemi JSON pro entity Factory dat, které se používají ke kopírování dat ze zdroje OData, najdete v [tématu Příklad JSON: Kopírování dat ze zdroje OData do](#json-example-copy-data-from-odata-source-to-azure-blob) azure blob části tohoto článku.

V následujících částech jsou uvedeny podrobnosti o vlastnostech JSON, které se používají k definování entit Factory dat specifických pro zdroj OData:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis prvků JSON specifických pro propojenou službu OData.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost type musí být nastavena na: **OData** |Ano |
| url |Adresa URL služby OData. |Ano |
| authenticationType |Typ ověřování používaný pro připojení ke zdroji OData. <br/><br/> Pro cloudOData možné hodnoty jsou anonymní, základní a OAuth (poznámka: Azure Data Factory aktuálně podporují jenom Azure Active Directory na Základě OAuth). <br/><br/> Pro místní OData možné hodnoty jsou Anonymní, Základní a Windows. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (pouze pokud používáte základní ověřování) |
| autorizované pověření |Pokud používáte OAuth, klepněte na tlačítko **Autorizovat** v Průvodci kopírováním datové továrny nebo v editoru a zadejte své přihlašovací údaje, pak bude hodnota této vlastnosti automaticky generována. |Ano (pouze pokud používáte ověřování OAuth) |
| název brány |Název brány, kterou by měla služba Data Factory použít pro připojení k místní službě OData. Zadejte pouze v případě, že kopírujete data z místního zdroje OData. |Ne |

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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Použití ověřování OAuth přistupující ke zdroji OData cloudu
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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datových sad, naleznete v článku [Vytváření datových sad.](data-factory-create-datasets.md) Oddíly, jako je struktura, dostupnost a zásady datové sady JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure blob, Tabulka Azure atd.).

Sekce **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl TypeProperties pro datovou sadu typu **ODataResource** (která zahrnuje datovou sadu OData) má následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| cesta |Cesta k prostředku OData |Ne |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivit, naleznete v článku [Vytváření kanálů.](data-factory-create-pipelines.md) Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Vlastnosti dostupné v části typeProperties aktivity na druhé straně se liší podle jednotlivých typů aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a propadů.

Pokud je zdroj typu **RelationalSource** (který zahrnuje OData), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Příklad | Požaduje se |
| --- | --- | --- | --- |
| query |Ke čtení dat použijte vlastní dotaz. |"?$select=Název, Popis&$top=5" |Ne |

## <a name="type-mapping-for-odata"></a>Mapování typů pro OData
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím dvoustupňovým přístupem.

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat z OData se používají následující mapování z typů OData na typ .NET.

| Datový typ OData | Typ .NET |
| --- | --- |
| Edm.Binary |Bajt[] |
| Edm.Boolean |Logická hodnota |
| Edm.Byte |Bajt[] |
| Edm.DateČas |DateTime |
| Edm.Desetinné místo |Desetinné číslo |
| Edm.Double |Double |
| Edm.Single |Single |
| Edm.Guid |Identifikátor GUID |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Řetězec |
| Edm.Time |TimeSpan |
| Edm.DateTimeOffset |DateTimeOffset |

> [!Note]
> Datové typy OData, například Objekt, nejsou podporovány.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Příklad JSON: Kopírování dat ze zdroje OData do objektu Blob Azure
Tento příklad obsahuje ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazují, jak zkopírovat data ze zdroje OData do úložiště objektů blob Azure. Data však můžete zkopírovat do libovolného jímky [uvedené zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory. Ukázka má následující entity Data Factory:

1. Propojená služba typu [OData](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, který používá [Relační zdroj](#copy-activity-properties) a [blobsink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z dotazování proti zdroj OData na objekt blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v následujících částech.

**OData propojená služba:** Tento příklad používá anonymní ověřování. Část [služby propojená s OData](#linked-service-properties) naleznete v části pro různé typy ověřování, které můžete použít.

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

**Propojené služby Azure Storage:**

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

**ODatová datová sada:**

Nastavení "externí": "true" informuje službu Data Factory, že datová sada je externí pro datové továrny a není vyráběna aktivitou v datové továrně.

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

Určení **cesty** v definici datové sady je volitelné.

**Výstupní datová sada objektu Blob Azure:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt blob je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části počátečního času rok, měsíc, den a hodiny.

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

**Kopírování aktivity v kanálu se zdrojem OData a jímkou blob:**

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad a je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **RelationalSource** a typ **jímky** je nastaven na **Objekt blobSink**. Dotaz SQL zadaný pro vlastnost **dotazu** vybere nejnovější (nejnovější) data ze zdroje OData.

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

Zadání **dotazu** v definici kanálu je volitelné. **Adresa URL,** kterou služba Data Factory používá k načtení dat, je: ADRESA URL zadaná v propojené službě (povinná) + cesta zadaná v datové sadě (volitelné) + dotaz v kanálu (volitelné).

### <a name="type-mapping-for-odata"></a>Mapování typů pro OData
Jak je uvedeno v článku [aktivity přesunu dat,](data-factory-data-movement-activities.md) copy aktivita provádí převody automatického typu z typů zdrojů na typy jímek s následujícím přístupem 2 kroky:

1. Převod z nativních typů zdrojů na typ .NET
2. Převod z typu .NET na nativní typ jímky

Při přesouvání dat z datových úložišť OData jsou datové typy OData mapovány na typy .NET.

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj pro jímací sloupce
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete [v tématu Mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [Opakovatelné čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
[V tématu Průvodce sledováním výkonu & optimalizací se](data-factory-copy-activity-performance.md) dozvíte o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) ve Službě Azure Data Factory, a o různých způsobech jeho optimalizace.
