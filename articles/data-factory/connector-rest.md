---
title: Kopírování dat ze zdrojového REST s využitím Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data ze zdroje REST-místních i cloudových úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 372275740b7d4fd757e97a3966e4e87c9d2de940
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105385"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopírování dat z koncového bodu REST s využitím Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat z koncového bodu REST. Tento článek vychází [aktivita kopírování ve službě Azure Data Factory](copy-activity-overview.md), který nabízí obecný přehled o aktivitě kopírování.

Rozdíly mezi tento konektor REST [konektor HTTP](connector-http.md) a [webový tabulky konektor](connector-web-table.md) jsou:

- **Konektor REST** konkrétně podpora kopírování dat z rozhraní RESTful API; 
- **Konektor HTTP** je obecný k načtení dat z jakékoli koncového bodu HTTP, třeba ke stažení souboru. Než bude tento konektor REST k dispozici, může dojít k používání konektoru HTTP ke zkopírování dat z rozhraní RESTful API, které je podporované, ale méně funkční porovnání konektor REST.
- **Webový konektor tabulky** výpisy tabulky obsah webové stránce HTML.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze zdroje REST k jakékoli podporovaného úložiště dat jímky. Seznam dat ukládá podporovanou aktivitou kopírování jako zdroje a jímky, najdete v části [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento obecný konektor REST podporuje:

- Načítání dat z koncového bodu REST s použitím **získat** nebo **příspěvek** metody.
- Načítání dat pomocí jedné z následujících ověření: **Anonymní**, **základní**, **instanční objekt služby AAD**, a **spravovaných identit pro prostředky Azure**.
- **[Stránkování](#pagination-support)**  v rozhraní REST API.
- Kopírování odpověď REST JSON [jako-je](#export-json-response-as-is) nebo analyzovat pomocí [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping). Pouze datové odpovědi v **JSON** je podporována.

> [!TIP]
> Žádost o načtení dat otestovat před konfigurací konektoru REST ve službě Data Factory, přečtěte si o specifikace rozhraní API pro záhlaví a text požadavky. Nástroje, jako je Postman nebo ve webovém prohlížeči můžete použít k ověření.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které lze použít k definování entit služby Data Factory, které jsou specifické pro konektor REST.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro ZBÝVAJÍCÍ propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **HttpServer**. | Ano |
| url | Základní adresa URL služby REST. | Ano |
| enableServerCertificateValidation | Určuje, zda při připojování ke koncovému bodu ověření certifikátu SSL na straně serveru. | Ne<br /> (výchozí hodnota je **true**) |
| authenticationType. | Typ ověřování používaný pro připojení ke službě REST. Povolené hodnoty jsou **anonymní**, **základní**, **AadServicePrincipal** a **ManagedServiceIdentity**. Odkazovat odpovídající části níže na více vlastností a příkladů | Ano |
| connectVia | [Prostředí Integration Runtime](concepts-integration-runtime.md) používat pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace v Azure nebo v místním prostředí Integration Runtime. Pokud není zadán, tuto vlastnost používá výchozí prostředí Azure Integration Runtime. |Ne |

### <a name="use-basic-authentication"></a>Základní ověřování použijte

Nastavte **authenticationType** vlastnost **základní**. Kromě generické vlastnosti, které jsou popsány v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| uživatelské jméno | Uživatelské jméno pro použití pro přístup ke koncovému bodu REST. | Ano |
| heslo | Heslo pro uživatele ( **uživatelské jméno** hodnota). Označte toto pole jako **SecureString** typ bezpečně uložit ve službě Data Factory. Můžete také [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>Ověřování instančních objektů pomocí AAD

Nastavte **authenticationType** vlastnost **AadServicePrincipal**. Kromě generické vlastnosti, které jsou popsány v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta aplikace Azure Active Directory. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace Azure Active Directory. Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtení podržením ukazatele myši v pravém horním rohu webu Azure portal. | Ano |
| aadResourceId | Zadejte prostředek AAD vyžadujete pro autorizaci, například `https://management.core.windows.net`.| Ano |

**Příklad**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Použití spravované identity pro ověřování prostředků Azure

Nastavte **authenticationType** vlastnost **ManagedServiceIdentity**. Kromě generické vlastnosti, které jsou popsány v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| aadResourceId | Zadejte prostředek AAD vyžadujete pro autorizaci, například `https://management.core.windows.net`.| Ano |

**Příklad**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Tato část obsahuje seznam vlastností, které podporuje datové sady REST. 

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Ke zkopírování dat z REST, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost datové sady **RestResource**. | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když tato vlastnost neurčí, použije se pouze adresu URL, která je zadána v definici propojené služby. | Ne |
| requestMethod | Metoda protokolu HTTP. Povolené hodnoty jsou **získat** (výchozí) a **příspěvek**. | Ne |
| additionalHeaders | Další hlavičky požadavků HTTP. | Ne |
| Includesearchresults: true | Obsah žádosti protokolu HTTP. | Ne |
| paginationRules | Pravidla stránkování sestavit požadavky na další stránku. Odkazovat na [podporu stránkování](#pagination-support) části na podrobnosti. | Ne |

**Příklad 1: Pomocí metody Get s stránkování**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": {
                "x-user-defined": "helloworld"
            },
            "paginationRules": {
                "AbsoluteUrl": "$.paging.next"
            }
        }
    }
}
```

**Příklad 2: Pomocí metody Post**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST REST request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Tato část obsahuje seznam vlastností, které podporuje zdroji REST.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>Rozhraní REST služby jako zdroj

Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být nastavena vlastnost zdroje aktivity kopírování **RestSource**. | Ano |
| httpRequestTimeout | Časový limit ( **TimeSpan** hodnotu) pro požadavek HTTP získat odpověď. Tato hodnota je časový limit získat odpověď, nevypršel časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | Ne |
| requestInterval | Čas, který se má čekat před odesílá se požadavek na další stránku. Výchozí hodnota je **00:00:01** |  Ne |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Podpora stránkování

Za normálních okolností rozhraní REST API omezit velikost datové části odpovědi jedné žádosti v rozumné číslo. Při vrácení velké množství dat, výsledek rozdělí na několik stránek a vyžaduje volající odesílat po sobě jdoucí požadavky se získat další stránky výsledků. Žádost o jednu stránku je obvykle dynamická a složené podle informace vrácené z odpovědi předchozí stránky.

Tento obecný konektor REST podporuje následující způsoby stránkování: 

* Další požadavek absolutní adresa URL = hodnotě vlastnosti v aktuální text odpovědi
* Absolutní adresa URL další požadavek = hodnota hlavičky v aktuální hlavičky odpovědi
* Další požadavek dotazu parametr = hodnota vlastnosti v aktuální text odpovědi
* Další požadavek dotazu parametr = hodnota hlavičky v aktuální hlavičky odpovědi
* Hlavička požadavku další = hodnota vlastnosti v aktuální text odpovědi
* Hlavička požadavku další = hodnota hlavičky v aktuální hlavičky odpovědi

**Pravidla stránkování** jsou definovány jako slovník v datové sadě, které obsahují jeden nebo více párů klíč hodnota velká a malá písmena. Konfigurace se použije k vygenerování žádosti od druhé stránce. Konektor se zastaví iterace při získá stavový kód HTTP 204 (žádný obsah), nebo některý z výrazu JSONPath v "paginationRules" vrátí hodnotu null.

**Nepodporuje klíče** stránkování pravidel:

| Klíč | Popis |
|:--- |:--- |
| AbsoluteUrl | Označuje adresu URL vydat další požadavek. |
| QueryParameters. *request_query_parameter* nebo QueryParameters [request_query_parameter] | "request_query_parameter" je uživatelem odkazuje na jeden název parametru dotazu v následující adrese URL požadavku HTTP. |
| Záhlaví. *request_header* nebo záhlaví [request_header] | "request_header" je uživatelem odkazuje na jeden název hlavičky v další požadavek HTTP. |

**Podporované hodnoty** stránkování pravidel:

| Hodnota | Popis |
|:--- |:--- |
| Záhlaví. *response_header* nebo záhlaví [response_header] | "response_header" je uživatelem odkazuje na jeden název hlavičky v aktuální odpověď HTTP, jehož hodnota se použije k vydat další požadavek. |
| Výraz JSONPath počínaje "$" (reprezentující kořen těla odpovědi) | Text odpovědi může obsahovat pouze jeden objekt JSON. Výraz JSONPath by měl vrátit primitivní hodnotu single, který se použije vydat další požadavek. |

**Příklad:**

Rozhraní Graph API sítě Facebook vrátí odpověď v následující strukturu, ve kterém je reprezentován případu další stránky adresu URL v ***paging.next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Odpovídající konfigurace datové sady REST zvlášť `paginationRules` vypadá takto:

```json
{
    "name": "MyFacebookAlbums",
    "properties": {
            "type": "RestResource",
            "typeProperties": {
                "relativeUrl": "albums",
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                }
            },
            "linkedServiceName": {
                "referenceName": "MyRestService",
                "type": "LinkedServiceReference"
            }
    }
}
```

## <a name="export-json-response-as-is"></a>Export odpověď JSON jako-je

Můžete použít tento konektor REST pro export odpověď REST API JSON jako – je do různých úložišť založená na souborech. K dosažení těchto kopírování dogmaticky na schématu, přeskočte "struktura" (také nazývané *schématu*) části datovou sadu a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>mapování schématu

Kopírování dat z koncového bodu REST do tabulky jímky, najdete v tématu [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další postup

Seznam úložišť dat, která aktivitu kopírování, která podporuje jako zdroje a jímky ve službě Azure Data Factory najdete v tématu [podporovaných úložišť dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
