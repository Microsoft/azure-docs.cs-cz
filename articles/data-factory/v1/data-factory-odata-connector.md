---
title: Přesun dat z OData zdroje | Microsoft Docs
description: Další informace o tom, jak přesunout data ze zdroje OData pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: de28fa56-3204-4546-a4df-21a21de43ed7
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b87ed8b9d9b43de81bfe4173d117d9f1e2bd7abd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622002"
---
# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Přesun dat z OData zdroje pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-odata-connector.md)
> * [Verze 2 – Preview](../connector-odata.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [konektor OData v V2](../connector-odata.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z na zdroj OData. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Z OData zdroje můžete zkopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z zdroje OData k jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat do zdroje OData. 

## <a name="supported-versions-and-authentication-types"></a>Podporované verze a typy ověřování
Tento konektor OData podporu OData verze 3.0 a 4.0 a můžete kopírovat data z obou cloudu OData a místním zdrojům OData. K tomu musíte nainstalovat bránu dat správy. V tématu [přesun dat mezi místními a cloudovými](data-factory-move-data-between-onprem-and-cloud.md) podrobnosti o Brána pro správu dat najdete v článku.

Níže ověřování jsou podporované typy:

* Pro přístup k **cloudu** datového kanálu OData, můžete použít anonymní, základní (uživatelské jméno a heslo) nebo Azure Active Directory na základě ověřování OAuth.
* Pro přístup k **místní** datového kanálu OData, můžete použít anonymní, základní (uživatelské jméno a heslo) nebo ověřování systému Windows.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z zdroje OData pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z zdroje OData, naleznete v tématu [JSON příklad: kopírování dat ze zdroje OData do objektu Blob Azure](#json-example-copy-data-from-odata-source-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory na zdroj OData:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro OData propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OData** |Ano |
| url |Adresa URL služby OData. |Ano |
| authenticationType. |Typ ověřování používaný pro připojení ke zdroji OData. <br/><br/> Možné hodnoty pro cloudové prostředí OData, jsou anonymní, základní a OAuth (Upozorňujeme, že Azure Active Directory na základě OAuth aktuálně jedinou podpory Azure Data Factory). <br/><br/> Pro místní OData možné hodnoty jsou anonymní, Basic a Windows. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ano (jenom Pokud používáte základní ověřování) |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ano (jenom Pokud používáte základní ověřování) |
| authorizedCredential |Pokud používáte OAuth, klikněte na tlačítko **Autorizovat** tlačítko Průvodce kopírováním služby Data Factory nebo editoru a zadejte svoje přihlašovací údaje, pak bude automaticky generovaný hodnota této vlastnosti. |Ano (jenom Pokud používáte ověřování OAuth) |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení ke službě OData na místě. Zadejte, pokud jsou kopírování dat z místní zdroj OData. |Ne |

### <a name="using-basic-authentication"></a>Použití základního ověřování
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Pomocí anonymní ověřování
```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Pomocí ověřování systému Windows přístup k místnímu zdroji OData
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

### <a name="using-oauth-authentication-accessing-cloud-odata-source"></a>Pomocí OAuth ověřování přístupu k cloudu zdroj OData
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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **ODataResource** (což zahrnuje datová sada OData) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| path |Cesta k prostředku OData |Ne |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti dostupné v rámci typeProperties části aktivity se liší na druhé straně každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Pokud je zdroj typu **RelationalSource** (která zahrnuje OData) následující vlastnosti jsou k dispozici v rámci typeProperties části:

| Vlastnost | Popis | Příklad: | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |"? $select = název, popis a $top = 5" |Ne |

## <a name="type-mapping-for-odata"></a>Mapování typu pro OData
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích.

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesouvání dat od OData, se používají následující mapování z typů OData k typ formátu .NET.

| Typ dat OData | Typ formátu .NET |
| --- | --- |
| Edm.Binary |Byte] |
| Edm.Boolean |BOOL |
| Edm.Byte |Byte] |
| Edm.DateTime |DateTime |
| Edm.Decimal |Decimal |
| Edm.Double |Double |
| Edm.Single |Jednoduchá |
| Edm.Guid |Guid |
| Edm.Int16 |Int16 |
| Edm.Int32 |Int32 |
| Edm.Int64 |Int64 |
| Edm.SByte |Int16 |
| Edm.String |Řetězec |
| Edm.Time |Časový interval |
| Edm.DateTimeOffset |Datový typ DateTimeOffset |

> [!Note]
> Komplexní data OData typy například objekt nejsou podporovány.

## <a name="json-example-copy-data-from-odata-source-to-azure-blob"></a>Příklad JSON: kopírování dat ze zdroje OData do objektu Blob Azure
Tento příklad obsahuje ukázkové JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data z zdroje OData do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory. Ukázka má následující entity služby Data Factory:

1. Propojené služby typu [OData](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [ODataResource](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z dotazování na zdroj OData do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

**Propojená služba OData:** tento příklad používá anonymní ověřování. V tématu [OData propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

```json
{
    "name": "ODataLinkedService",
        "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
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

**Vstupní datové sady OData:**

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

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

Určení **cesta** v datové sadě definice je volitelné.

**Azure Blob výstupní datovou sadu:**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

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


**Aktivita kopírování v kanálu s OData zdrojový a podřízený objekt Blob:**

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere nejnovější (nejnovější) data ze zdroje OData.

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

Určení **dotazu** v kanálu definice je volitelné. **URL** , služba Data Factory používá k načtení dat je: adresa URL zadaná v propojené službě (povinné) + cesta zadaná v datové sadě (volitelné) + dotazu v kanálu (volitelné).


### <a name="type-mapping-for-odata"></a>Mapování typu pro OData
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup krok 2:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesunutí dat od OData ukládá, OData datové typy jsou namapované na typy .NET.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
