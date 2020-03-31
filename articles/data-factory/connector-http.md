---
title: Kopírování dat ze zdroje HTTP pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z cloudu nebo místního zdroje HTTP do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 1ca439d1a82e3cdbe2cc0274cf63653d39048057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532548"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopírování dat z koncového bodu HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-http-connector.md)
> * [Aktuální verze](connector-http.md)

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat z koncového bodu HTTP. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

Rozdíl mezi tímto konektorem HTTP, [konektorem REST](connector-rest.md) a [konektorem webové tabulky](connector-web-table.md) jsou:

- **Rest konektor** konkrétně podporují kopírování dat z ROZHRANÍ API RESTful; 
- **Http konektor** je obecný pro načtení dat z libovolného koncového bodu HTTP, například ke stažení souboru. Před REST konektor k dispozici, může dojít ke použití konektoru HTTP ke kopírování dat z ROZHRANÍ RESTful API, který je podporován, ale méně funkční ve srovnání s konektorem REST.
- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor HTTP je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data ze zdroje HTTP můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Tento konektor HTTP můžete použít k:

- Načtení dat z koncového bodu HTTP/S pomocí metod HTTP **GET** nebo **POST.**
- Načtení dat pomocí jednoho z následujících ověřování: **Anonymní**, **Základní**, **Digest**, **Windows**nebo **ClientCertificate**.
- Zkopírujte odpověď HTTP tak, jak je, nebo ji analyzujte pomocí [podporovaných formátů souborů a kompresních kodeků](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Chcete-li otestovat požadavek HTTP pro načítání dat před konfigurací konektoru HTTP v datové továrně, přečtěte si informace o specifikaci rozhraní API pro požadavky na záhlaví a tělo. K ověření můžete použít nástroje, jako je Postman nebo webový prohlížeč.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Data Factory, které jsou specifické pro konektor HTTP.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu HTTP jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **httpserver**. | Ano |
| url | Základní adresa URL webového serveru. | Ano |
| enableServerCertificateValidation | Určete, zda se má povolit ověření certifikátu SSL serveru při připojení ke koncovému bodu HTTP. Pokud server HTTPS používá certifikát podepsaný svým držitelem, nastavte tuto vlastnost na **hodnotu false**. | Ne<br /> (výchozí nastavení je **pravda**) |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou **Anonymous**, **Basic**, **Digest**, **Windows**a **ClientCertificate**. <br><br> Další vlastnosti a ukázky JSON pro tyto typy ověřování naleznete v následujících částech, které následují po této tabulce. | Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="using-basic-digest-or-windows-authentication"></a>Použití základního ověřování, ověřování algoritmem Digest nebo Systému Windows

Nastavte vlastnost **authenticationType** na **Základní**, **Digest**nebo **Windows**. Kromě obecných vlastností popsaných v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatelské jméno, které se má použít pro přístup ke koncovému bodu HTTP. | Ano |
| heslo | Heslo pro uživatele (hodnota **userName).** Označte toto pole jako typ **SecureString** pro bezpečné uložení v datové továrně. Můžete také [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |

**Příklad**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Použití ověřování clientcertificate

Chcete-li použít ověřování ClientCertificate, nastavte vlastnost **authenticationType** na **ClientCertificate**. Kromě obecných vlastností popsaných v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| embeddedCertData | Data certifikátů kódovaných base64. | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| certThumbprint | Kryptografický otisk certifikátu, který je nainstalován v úložišti certifikátů vašeho počítače s vlastním hostitelem. Platí pouze v případě, že je ve vlastnosti **connectVia** zadán typ prostředí Integrace, který je hostovaný samostatně. | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| heslo | Heslo, které je přidruženo k certifikátu. Označte toto pole jako typ **SecureString** pro bezpečné uložení v datové továrně. Můžete také [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ne |

Pokud používáte **certThumbprint** pro ověřování a certifikát je nainstalován v osobním úložišti místního počítače, udělte oprávnění ke čtení prostředí Integrace s vlastním hostitelem:

1. Otevřete konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **Certifikáty,** který cílí na **místní počítač**.
2. Rozbalte **položku Certifikáty** > **osobní**a vyberte **možnost Certifikáty**.
3. Klepněte pravým tlačítkem myši na certifikát z osobního úložiště a vyberte příkaz **Všechny úkoly** > **spravovat soukromé klíče**.
3. Na kartě **Zabezpečení** přidejte uživatelský účet, pod kterým je spuštěna hostitelská služba Integration Runtime (DIAHostService) s přístupem pro čtení k certifikátu.

**Příklad 1: Použití certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad 2: Použití embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro protokol HTTP v nastavení `location` v datové sadě založené na formátu:

| Vlastnost    | Popis                                                  | Požaduje se |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Vlastnost type `location` under v datové sadě musí být nastavena na **HttpServerLocation**. | Ano      |
| Relativeurl | Relativní adresa URL prostředku, který obsahuje data. Konektor HTTP zkopíruje data z `[URL specified in linked service][relative URL specified in dataset]`kombinované adresy URL: .   | Ne       |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je přibližně 500 kB. Pokud je velikost datové části, kterou chcete předat webovému koncovému bodu, větší než 500 kB, zvažte dávkování datové části v menších blocích.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které podporuje zdroj HTTP.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro protokol HTTP v rámci `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **HttpReadSettings**. | Ano      |
| requestMethod            | Metoda HTTP. <br>Povolené hodnoty jsou **Get** (výchozí) a **Post**. | Ne       |
| addtionalZáhlaví         | Další hlavičky požadavku HTTP.                             | Ne       |
| requestBody              | Tělo pro požadavek HTTP.                               | Ne       |
| httpRequestTimeout           | Časový čas (hodnota **TimeSpan)** pro požadavek HTTP získat odpověď. Tato hodnota je časový čas získat odpověď, nikoli časový čas pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**. | Ne       |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **httpfile**. | Ano |
| Relativeurl | Relativní adresa URL prostředku, který obsahuje data. Pokud tato vlastnost není zadána, použije se pouze adresa URL zadaná v definici propojené služby. | Ne |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** (výchozí) a **Post**. | Ne |
| additionalHeaders | Další hlavičky požadavku HTTP. | Ne |
| requestBody | Tělo pro požadavek HTTP. | Ne |
| formát | Pokud chcete načíst data z koncového bodu HTTP tak, jak je bez analýzy, a potom je zkopírujte do úložiště založeného na souborech, přeskočte oddíl **formátu** v definicích vstupní i výstupní datové sady.<br/><br/>Chcete-li během kopírování analyzovat obsah odpovědi HTTP, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V **části format**nastavte vlastnost **type** na jednu z těchto hodnot. Další informace naleznete v [tématech Formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Textový formát](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formát orků](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Ne |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Podporované typy: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně: **Optimální** a **nejrychlejší**. |Ne |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je přibližně 500 kB. Pokud je velikost datové části, kterou chcete předat webovému koncovému bodu, větší než 500 kB, zvažte dávkování datové části v menších blocích.

**Příklad 1: Použití metody Get (výchozí)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Příklad 2: Použití metody Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **httpsource**. | Ano |
| httpRequestTimeout | Časový čas (hodnota **TimeSpan)** pro požadavek HTTP získat odpověď. Tato hodnota je časový čas získat odpověď, nikoli časový čas pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | Ne |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu Podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
