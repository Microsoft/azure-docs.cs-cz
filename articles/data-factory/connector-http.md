---
title: Kopírování dat ze zdroje HTTP pomocí Azure Data Factory | Microsoft Docs
description: Postup kopírování dat z cloudu nebo na místní zdroj HTTP do úložiště dat podporovaných podřízený pomocí aktivity kopírování v kanál služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 3aca66d6922273e78b5100948f1b868c6c9b56af
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopírování dat z koncový bod HTTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-http-connector.md)
> * [Verze 2 – Preview](connector-http.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z koncový bod HTTP. Vychází [zkopírujte aktivity přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [HTTP konektoru V1](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Z HTTP zdroje můžete zkopírovat data do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor HTTP podporuje:

- Načítání dat z koncového bodu protokolu HTTP/s pomocí protokolu HTTP **získat** nebo **POST** metoda.
- Načítání dat pomocí následující ověření: **anonymní**, **základní**, **Digest**, **Windows**, a  **ClientCertificate**.
- Kopírování jako odpověď HTTP-je nebo je analýza [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

Rozdíl mezi tohoto konektoru a [konektor tabulky webových](connector-web-table.md) je, že k tomu slouží k extrahování obsahu tabulky z HTML webové stránky.

>[!TIP]
>K testování požadavku HTTP pro načítání před konfigurací konektoru HTTP v ADF data, můžete dozvědět se od specifikace rozhraní API v záhlaví a text požadavky a pomocí nástrojů, jako je Postman nebo webový prohlížeč k ověření.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení konkrétní entity služby Data Factory ke konektoru HTTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro HTTP propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| typ | Vlastnost typu musí být nastavena na: **HttpServer**. | Ano |
| Adresa URL | Základní adresu URL na webový server | Ano |
| enableServerCertificateValidation | Určete, zda chcete povolit ověřování certifikátu serveru SSL při připojení ke koncovému bodu HTTP. | Ne, výchozí hodnota je true |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v části dál v této tabulce na další vlastnosti a ukázky JSON pro tyto typy ověřování v uvedeném pořadí. | Ano |
| connectVia | [Integrace Runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

### <a name="using-basic-digest-or-windows-authentication"></a>Pomocí ověřování Basic, ověřování algoritmem Digest nebo systému Windows

Nastavte vlastnost "authenticationType" na **základní**, **Digest**, nebo **Windows**a zadejte následující vlastnosti společně s obecné vlastnosti, které jsou popsané v předchozí části:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| userName | Uživatelské jméno pro přístup k koncový bod HTTP. | Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

### <a name="using-clientcertificate-authentication"></a>Pomocí ClientCertificate ověřování

Chcete-li použít ověřování ClientCertificate, nastavte vlastnost "authenticationType" na **ClientCertificate**a zadejte následující vlastnosti společně s obecné vlastnosti, které jsou popsané v předchozí části:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| embeddedCertData | Data certifikátu kódováním base64. | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| certThumbprint | Kryptografický otisk certifikátu, který je nainstalován v úložišti certifikátů počítače Self-hosted integrace Runtime. Platí jenom v případě, že je zadán vlastním hostováním typ integrace Runtime v connectVia. | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| heslo | Heslo přidružené k certifikátu. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

Pokud používáte "certThumbprint" pro ověřování a je certifikát nainstalován v osobním úložišti místního počítače, je třeba udělit oprávnění ke čtení modulu runtime Self-hosted integrace:

1. Spusťte konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in zacílený **místního počítače**.
2. Rozbalte položku **certifikáty**, **osobní**a klikněte na tlačítko **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a vyberte **všechny úlohy** -> **spravovat privátní klíče...**
3. Na **zabezpečení** přidejte uživatelský účet, pod kterým je spuštěna služba integrace modulu Runtime hostitele (DIAHostService) s přístupem pro čtení k certifikátu.

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

**Příklad 2: pomocí embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností nepodporuje datovou sadu protokolu HTTP.

Ke zkopírování dat z protokolu HTTP, nastavte vlastnost typu datové sady, která **HttpFile**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| typ | Vlastnost typu datové sady musí být nastavena na: **HttpFile** | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když není tato vlastnost určena, použije se jenom adresu URL, zadaný v definici propojené služby. | Ne |
| requestMethod | Metoda HTTP.<br/>Povolené hodnoty jsou **získat** (výchozí) nebo **Post**. | Ne |
| additionalHeaders | Další hlavičky žádosti HTTP. | Ne |
| RequestBody | Text pro požadavek HTTP. | Ne |
| Formát | Pokud chcete **načtení dat z koncový bod HTTP jako-je** bez analýza ho a zkopírujte do úložiště na základě souborů, přeskočte část formátu v obou definice vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat během kopírování obsahu odpovědi HTTP, jsou podporovány následující typy souboru formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnost pod formát na jednu z těchto hodnot. Další informace najdete v tématu [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [textovém formátu](supported-file-formats-and-compression-codecs.md#text-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formátu](supported-file-formats-and-compression-codecs.md#orc-format), a [Parquet formát](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

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

**Příklad 2: pomocí metody Post**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje zdroje pomocí protokolu HTTP.

### <a name="http-as-source"></a>HTTP jako zdroj

Ke zkopírování dat z protokolu HTTP, nastavte typ zdroje v aktivitě kopírování do **HttpSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požadováno |
|:--- |:--- |:--- |
| typ | Vlastnost typ zdroje kopie aktivity musí být nastavena na: **HttpSource** | Ano |
| httpRequestTimeout | Časový limit (TimeSpan) pro získání odezvy požadavku HTTP. Získání odezvy, není časový limit číst data odpovědi je časový limit.<br/> Výchozí hodnota je: 00:01:40  | Ne |

**Příklad:**

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


## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).