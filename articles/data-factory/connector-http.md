---
title: Kopírování dat ze zdroje HTTP pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze zdroje HTTP-místních i cloudových úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: jingwang
ms.openlocfilehash: 5afb2fccd5c7b8ca306079941837d854c0b21349
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091713"
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Kopírování dat z koncového bodu HTTP pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-http-connector.md)
> * [Aktuální verze](connector-http.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z koncového bodu HTTP. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze zdroje pomocí protokolu HTTP na libovolné podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor HTTP podporuje:

- Načítání dat z koncového bodu HTTP/s přes protokol HTTP **získat** nebo **příspěvek** metody.
- Načítání dat pomocí následující ověření: **anonymní**, **základní**, **Digest**, **Windows**, a  **ClientCertificate**.
- Kopírování odpověď HTTP jako-je nebo pomocí analýzy [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md).

Rozdíl mezi tento konektor a [webový tabulky konektor](connector-web-table.md) je, že druhá možnost se používá k extrahování obsahu tabulka z HTML webové stránky.

>[!TIP]
>K testování požadavku HTTP pro načítání před konfigurací konektoru HTTP ve službě ADF data, můžete Učte se od specifikace rozhraní API v záhlaví a text požadavky a pomocí nástrojů, jako je Postman nebo webový prohlížeč k ověření.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor HTTP.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro HTTP propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **HttpServer**. | Ano |
| url | Základní adresa URL webového serveru | Ano |
| enableServerCertificateValidation | Určete, zda povolit ověření certifikátu serveru SSL při připojování ke koncovému bodu HTTP. Pokud váš server HTTPS používá certifikát podepsaný svým držitelem, nastavte toto na false. | Ne, výchozí hodnota je true |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v oddílech dál v této tabulce na další vlastnosti a ukázky JSON pro typy ověřování v uvedeném pořadí. | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="using-basic-digest-or-windows-authentication"></a>Používá ověřování Basic, Digest nebo Windows

Nastavte vlastnost "authenticationType" **základní**, **Digest**, nebo **Windows**a zadejte následující požadované vlastnosti spolu s generické vlastnosti, které jsou popsané v předchozí části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| uživatelské jméno | Uživatelské jméno pro přístup ke koncovému bodu HTTP. | Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

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

### <a name="using-clientcertificate-authentication"></a>Pomocí ověřování ClientCertificate

Pokud chcete používat ověřování ClientCertificate, nastavte vlastnost "authenticationType" na **ClientCertificate**a zadejte následující požadované vlastnosti spolu s obecných vlastností popsaných v předchozí části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| embeddedCertData | Data certifikátu s kódováním base64. | Zadejte, jestli `embeddedCertData` nebo `certThumbprint`. |
| certThumbprint | Kryptografický otisk certifikátu, který je nainstalovaný na počítači modul Integration Runtime úložiště certifikátů. Platí pouze v případě, že v místním prostředí typu prostředí Integration Runtime je zadán v connectVia. | Zadejte, jestli `embeddedCertData` nebo `certThumbprint`. |
| heslo | Hesla přidruženého k certifikátu. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ne |

Pokud používáte "certThumbprint" pro ověřování a je tento certifikát nainstalován v osobním úložišti místního počítače, musíte udělit oprávnění ke čtení pro modul Integration Runtime:

1. Spusťte konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in, který se zaměřuje **místního počítače**.
2. Rozbalte **certifikáty**, **osobní**a klikněte na tlačítko **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a vyberte **všechny úkoly** -> **spravovat soukromé klíče...**
3. Na **zabezpečení** kartu, přidejte uživatelský účet, pod kterou je hostitelská služba modulu Integration Runtime (diahostservice byla) spuštěna s oprávněním ke čtení k certifikátu.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady najdete v článku datové sady. Tato část obsahuje seznam vlastností, které podporuje datová sada HTTP.

Ke zkopírování dat z protokolu HTTP, nastavte vlastnost typ datové sady na **HttpFile**. Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **HttpFile** | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud není tato vlastnost určena, se používá pouze adresu URL, které jsou určené v definici propojené služby. | Ne |
| requestMethod | Metoda protokolu HTTP.<br/>Povolené hodnoty jsou **získat** (výchozí) nebo **příspěvek**. | Ne |
| additionalHeaders | Další hlavičky požadavků HTTP. | Ne |
| Includesearchresults: true | Obsah žádosti protokolu HTTP. | Ne |
| Formát | Pokud chcete **načtení dat z koncového bodu HTTP jako-je** bez parsování ho a zkopírujte ho do úložiště založené na souborech, přeskočte část formátu v definicích oba vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat obsah odpovědi HTTP při kopírování, jsou podporovány následující typy formátů souboru: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Nastavte **typ** vlastnosti v části formát na jednu z těchto hodnot. Další informace najdete v tématu [formátu Json](supported-file-formats-and-compression-codecs.md#json-format), [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [Avro formát](supported-file-formats-and-compression-codecs.md#avro-format), [Orc formát](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně: **Optimal** a **nejrychlejší**. |Ne |

>[!NOTE]
>Podporovaná velikost datové části požadavku HTTP je přibližně 500KB. Pokud je větší než tato velikost datové části, které chcete předat do vašeho koncového bodu webové, zvažte do menších bloků do služby batch.

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

**Příklad 2: použití metody Post**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných zdrojem HTTP.

### <a name="http-as-source"></a>HTTP jako zdroj

Ke zkopírování dat z protokolu HTTP, nastavte typ zdroje v aktivitě kopírování do **HttpSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu zdroje aktivity kopírování musí být nastavena na: **HttpSource** | Ano |
| httpRequestTimeout | Časový limit (TimeSpan) pro požadavek HTTP získat odpověď. Časový limit je získat odpověď, nevypršel časový limit pro čtení dat odpovědi.<br/> Výchozí hodnota je: 00:01:40  | Ne |

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
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
