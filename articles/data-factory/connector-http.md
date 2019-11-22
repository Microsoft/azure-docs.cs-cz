---
title: Kopírování dat ze zdroje HTTP pomocí Azure Data Factory
description: Naučte se kopírovat data z cloudového nebo místního zdroje HTTP na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 214809df692bc7e43203fe4a736d6cc53908e0bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278526"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopírování dat z koncového bodu HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-http-connector.md)
> * [Aktuální verze](connector-http.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z koncového bodu HTTP. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

Rozdíl mezi tímto konektorem HTTP, [konektorem REST](connector-rest.md) a [konektorem webové tabulky](connector-web-table.md) jsou:

- **Konektor REST** přímo podporuje kopírování dat z rozhraní API RESTful. 
- **Konektor http** je obecný k načtení dat z libovolného koncového bodu http, třeba ke stažení souboru. Než bude konektor REST k dispozici, můžete k tomu použít konektor HTTP ke kopírování dat z rozhraní RESTful API, které je podporováno, ale méně funkčních porovnání s konektorem REST.
- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor HTTP se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data ze zdroje HTTP můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Tento konektor HTTP můžete použít k těmto akcím:

- Načtěte data z koncového bodu HTTP/S pomocí metod HTTP **Get** nebo **post** .
- Načtěte data pomocí jednoho z následujících ověření: **anonymní**, **základní**, **Digest**, **Windows**nebo **ClientCertificate**.
- Zkopírujte odpověď HTTP tak, jak je, nebo ji Analyzujte pomocí [podporovaných formátů souborů a kompresních kodeků](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Chcete-li otestovat požadavek HTTP na načtení dat před konfigurací konektoru HTTP v Data Factory, přečtěte si informace o specifikaci rozhraní API pro požadavky na hlavičku a tělo. K ověření můžete použít nástroje, jako je například nástroj pro odeslání nebo webový prohlížeč.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit specifických pro konektor HTTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu HTTP jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavena na hodnotu **HttpServer**. | Ano |
| url | Základní adresa URL webového serveru. | Ano |
| enableServerCertificateValidation | Určete, jestli se při připojení ke koncovému bodu HTTP má povolit ověřování certifikátu SSL serveru. Pokud váš server HTTPS používá certifikát podepsaný svým držitelem, nastavte tuto vlastnost na **false**. | Ne<br /> (výchozí hodnota je **true**) |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou **anonymní**, **základní**, **Digest**, **Windows**a **ClientCertificate**. <br><br> Další vlastnosti a ukázky JSON pro tyto typy ověřování najdete v částech uvedených v této tabulce. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. Další informace najdete v části [požadavky](#prerequisites) . Pokud není zadán, použije se výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="using-basic-digest-or-windows-authentication"></a>Použití ověřování Basic, Digest nebo Windows

Nastavte vlastnost **AuthenticationType** na hodnotu **Basic**, **Digest**nebo **Windows**. Kromě obecných vlastností, které jsou popsány v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatelské jméno, které se má použít pro přístup ke koncovému bodu HTTP. | Ano |
| heslo | Heslo pro uživatele ( **userName** hodnota). Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

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

### <a name="using-clientcertificate-authentication"></a>Použití ověřování ClientCertificate

Chcete-li použít ověřování ClientCertificate, nastavte vlastnost **AuthenticationType** na **ClientCertificate**. Kromě obecných vlastností, které jsou popsány v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| embeddedCertData | Data certifikátu zakódovaná ve formátu base64. | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| certThumbprint | Kryptografický otisk certifikátu, který je nainstalovaný v úložišti certifikátů počítače Integration Runtime v místním prostředí. Platí pouze v případě, že je typ hosta Integration Runtime zadán ve vlastnosti **connectVia** . | Zadejte buď **embeddedCertData** nebo **certThumbprint**. |
| heslo | Heslo, které je přidruženo k certifikátu. Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

Pokud pro ověřování používáte **certThumbprint** a certifikát se instaluje do osobního úložiště místního počítače, udělte oprávnění ke čtení Integration Runtimeům v místním prostředí:

1. Otevřete konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **certifikáty** , který cílí na **místní počítač**.
2. Rozbalte **certifikáty** > **osobní**a pak vyberte **certifikáty**.
3. Pravým tlačítkem myši klikněte na certifikát z osobního úložiště a pak vyberte **všechny úlohy** > **spravovat soukromé klíče**.
3. Na kartě **zabezpečení** přidejte uživatelský účet, pod kterým je spuštěná služba Integration runtime Host (DIAHostService) s přístupem pro čtení k certifikátu.

**Příklad 1: použití certThumbprint**

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

**Příklad 2: použití embeddedCertData**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro protokol HTTP v `location` nastavení v datové sadě založené na formátu:

| Vlastnost    | Popis                                                  | Požaduje se |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **HttpServerLocation**. | Ano      |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Konektor HTTP kopíruje data z kombinované adresy URL: `[URL specified in linked service]/[relative URL specified in dataset]`.   | Ne       |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je okolo 500 KB. Pokud je velikost datové části, kterou chcete předat webovému koncovému bodu, větší než 500 KB, zvažte dávkování datové části v menších blocích.

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

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

>[!NOTE]
>Následující model DataSet je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu uvedeného výše v oddílu dál a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** datové sady musí být nastavená na **HttpFile**. | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud tato vlastnost není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. | Ne |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** (default) a **post**. | Ne |
| additionalHeaders | Další hlavičky požadavku HTTP | Ne |
| částmi | Tělo požadavku HTTP | Ne |
| format | Pokud chcete načíst data z koncového bodu HTTP, jak je bez jeho analýzy, a pak zkopírovat data do úložiště založeného na souborech, přeskočte oddíl **Formát** v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat obsah odpovědi HTTP během kopírování, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V části **Formát**nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v tématu [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), formát [textu](supported-file-formats-and-compression-codecs.md#text-format), formát [Avro](supported-file-formats-and-compression-codecs.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs.md#orc-format)a [Formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |Ne |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Podporované typy: **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně: **optimální** a **nejrychlejší**. |Ne |

> [!NOTE]
> Podporovaná velikost datové části požadavku HTTP je okolo 500 KB. Pokud je velikost datové části, kterou chcete předat webovému koncovému bodu, větší než 500 KB, zvažte dávkování datové části v menších blocích.

**Příklad 1: použití metody Get (výchozí)**

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

**Příklad 2: použití metody post**

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností, které zdroj HTTP podporuje.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP as source

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro protokol HTTP v nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **HttpReadSetting**. | Ano      |
| requestMethod            | Metoda HTTP. <br>Povolené hodnoty jsou **Get** (default) a **post**. | Ne       |
| addtionalHeaders         | Další hlavičky požadavku HTTP                             | Ne       |
| částmi              | Tělo požadavku HTTP                               | Ne       |
| httpRequestTimeout           | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**. | Ne       |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSetting",
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

#### <a name="legacy-source-model"></a>Starší zdrojový model

>[!NOTE]
>Následující zdrojový model kopírování je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **HttpSource**. | Ano |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | Ne |

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

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
