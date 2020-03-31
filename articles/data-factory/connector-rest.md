---
title: Kopírování dat ze zdroje REST pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z cloudu nebo místního zdroje REST do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 3e0dd6e0bb81aef340dc83288e6e5c0af0bf11c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867370"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Kopírování dat z koncového bodu REST pomocí Azure Data Factory

Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory ke kopírování dat z koncového bodu REST. Článek vychází [z aktivity kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

Rozdíl mezi tímto konektorem REST, [konektorem HTTP](connector-http.md) a [konektorem webové tabulky](connector-web-table.md) jsou:

- **REST konektor** konkrétně podporuje kopírování dat z RESTful rozhraní API; 
- **Http konektor** je obecný pro načtení dat z libovolného koncového bodu HTTP, například ke stažení souboru. Před tento konektor REST k dispozici, může dojít ke použití konektoru HTTP ke kopírování dat z rozhraní RESTful API, který je podporován, ale méně funkční ve srovnání s konektorem REST.
- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.

## <a name="supported-capabilities"></a>Podporované možnosti

Data ze zdroje REST můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která podporují aktivita kopírování jako zdroje a propady, naleznete v [tématu Podporovaná úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento obecný konektor REST podporuje:

- Načítání dat z koncového bodu REST pomocí get **nebo** **POST** metody.
- Načítání dat pomocí jednoho z následujících ověřování: **Anonymní**, **Základní**, **Instanční objekt Služby AAD**a **spravované identity pro prostředky Azure**.
- **[Stránkování](#pagination-support)** v rest API.
- Kopírování odpovědi REST JSON [jako-je](#export-json-response-as-is) nebo analyzovat pomocí [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping). Podporována je pouze datová část odpovědi v **JSON.**

> [!TIP]
> Chcete-li otestovat požadavek na načtení dat před konfigurací konektoru REST v datové továrně, přečtěte si informace o specifikaci rozhraní API pro požadavky na záhlaví a tělo. K ověření můžete použít nástroje, jako je Postman nebo webový prohlížeč.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které můžete použít k definování entit Data Factory, které jsou specifické pro konektor REST.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu REST jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **RestService**. | Ano |
| url | Základní adresa URL služby REST. | Ano |
| enableServerCertificateValidation | Určuje, zda se má při připojování ke koncovému bodu ověřit certifikát SSL na straně serveru. | Ne<br /> (výchozí nastavení je **pravda**) |
| authenticationType | Typ ověřování používaný pro připojení ke službě REST. Povolené hodnoty jsou **Anonymous**, **Basic**, **AadServicePrincipal** a **ManagedServiceIdentity**. Podívejte se na odpovídající části níže na více vlastností a příklady, resp. | Ano |
| connectVia | [Prostředí Integrace Runtime](concepts-integration-runtime.md) pro připojení k úložišti dat. Další informace naleznete v části [Požadavky.](#prerequisites) Pokud není zadán, tato vlastnost používá výchozí Azure Integration Runtime. |Ne |

### <a name="use-basic-authentication"></a>Použití základního ověřování

Nastavte vlastnost **authenticationType** na **základní**. Kromě obecných vlastností popsaných v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| userName | Uživatelské jméno, které chcete použít pro přístup ke koncovému bodu REST. | Ano |
| heslo | Heslo pro uživatele (hodnota **userName).** Označte toto pole jako typ **SecureString** pro bezpečné uložení v datové továrně. Můžete také [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |

**Příklad**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
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

### <a name="use-aad-service-principal-authentication"></a>Použití ověřování instančního objektu služby AAD

Nastavte vlastnost **authenticationType** na **AadServicePrincipal**. Kromě obecných vlastností popsaných v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta aplikace Azure Active Directory. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace Azure Active Directory. Označte toto pole jako **SecureString** bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Načtěte ji najetím myší v pravém horním rohu portálu Azure. | Ano |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro `https://management.core.windows.net`autorizaci, například .| Ano |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Použití spravovaných identit pro ověřování prostředků Azure

Nastavte vlastnost **authenticationType** na **ManagedServiceIdentity**. Kromě obecných vlastností popsaných v předchozí části zadejte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro `https://management.core.windows.net`autorizaci, například .| Ano |

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

Tato část obsahuje seznam vlastností, které podporuje datová sada REST. 

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li zkopírovat data z REST, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** datové sady musí být nastavena na **RestResource**. | Ano |
| Relativeurl | Relativní adresa URL prostředku, který obsahuje data. Pokud tato vlastnost není zadána, použije se pouze adresa URL zadaná v definici propojené služby. Konektor HTTP zkopíruje data z `[URL specified in linked service]/[relative URL specified in dataset]`kombinované adresy URL: . | Ne |

Pokud jste `requestMethod`nastavili `requestBody` `paginationRules` , `additionalHeaders`a v datové sadě, je stále podporována tak, jak je, zatímco se doporučuje použít nový model ve zdroji aktivity do budoucna.

**Příklad:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Tato část obsahuje seznam vlastností, které podporuje zdroj REST.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Potrubí](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST jako zdroj

V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na **stav RestSource**. | Ano |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** (výchozí) a **Post**. | Ne |
| additionalHeaders | Další hlavičky požadavku HTTP. | Ne |
| requestBody | Tělo pro požadavek HTTP. | Ne |
| paginationRules | Stránkování pravidla pro skládání další chpožadavků stránky. Podrobnosti naleznete v části [podpory stránkování.](#pagination-support) | Ne |
| httpRequestTimeout | Časový čas (hodnota **TimeSpan)** pro požadavek HTTP získat odpověď. Tato hodnota je časový čas získat odpověď, nikoli časový čas pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | Ne |
| requestInterval | Doba čekání před odesláním požadavku na další stránku. Výchozí hodnota je **00:00:01** |  Ne |

>[!NOTE]
>Konektor REST ignoruje všechny hlavičky `additionalHeaders`"Přijmout" zadané v . Jako REST konektor podporuje pouze odpověď v JSON, `Accept: application/json`bude automaticky generovat záhlaví .

**Příklad 1: Použití metody Get s stránkováním**

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
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Příklad 2: Použití metody Post**

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
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Podpora pro pagination

Rozhraní REST API obvykle omezuje velikost užitečné ho zatížení odpovědi jednoho požadavku na základě přiměřeného počtu. zatímco vrátit velké množství dat, rozdělí výsledek na více stránek a vyžaduje, aby volající odeslat po sobě jdoucí chyzení, aby získali další stránku výsledku. Požadavek na jednu stránku je obvykle dynamický a skládá se z informací vrácených z odpovědi předchozí stránky.

Tento obecný konektor REST podporuje následující vzory stránkování: 

* Absolutní nebo relativní adresa URL dalšího požadavku = hodnota vlastnosti v aktuálním těle odpovědi
* Absolutní nebo relativní adresa URL dalšího požadavku = hodnota záhlaví v aktuálních hlavičkách odpovědí
* Parametr dotazu dalšího požadavku = hodnota vlastnosti v aktuálním těle odpovědi
* Parametr dotazu dalšího požadavku = hodnota záhlaví v aktuálních hlavičkách odpovědí
* Hlavička dalšího požadavku = hodnota vlastnosti v aktuálním těle odpovědi
* Hlavička dalšího požadavku = hodnota hlavičky v aktuálních hlavičkách odpovědí

**Stránkovací pravidla** jsou definována jako slovník v datové sadě, která obsahuje jeden nebo více párů klíč-hodnota rozlišující malá a velká písmena. Konfigurace bude použita ke generování požadavku od druhé stránky. Konektor přestane iterace, když získá stavový kód HTTP 204 (žádný obsah) nebo některý z výrazů JSONPath v "paginationRules" vrátí null.

**Podporované klíče** v pravidlech stránkování:

| Klíč | Popis |
|:--- |:--- |
| AbsoluteUrl | Označuje adresu URL, která má vydat další požadavek. Může to být **absolutní nebo relativní adresa URL**. |
| Parametry dotazu. *request_query_parameter* NEBO QueryParameters['request_query_parameter'] | "request_query_parameter" je definován uživatelem, který odkazuje na jeden název parametru dotazu v další adrese URL požadavku HTTP. |
| Záhlaví. *request_header* Nebo záhlaví['request_header'] | "request_header" je definován uživatelem, který odkazuje na jeden název záhlaví v dalším požadavku HTTP. |

**Podporované hodnoty** v pravidlech stránkování:

| Hodnota | Popis |
|:--- |:--- |
| Záhlaví. *response_header* Nebo záhlaví['response_header'] | "response_header" je definován uživatelem, který odkazuje na jeden název záhlaví v aktuální odpovědi HTTP, jejíž hodnota bude použita k vydání dalšího požadavku. |
| Výraz JSONPath začínající písmenem $(představující kořen těla odezvy) | Tělo odpovědi by mělo obsahovat pouze jeden objekt JSON. Výraz JSONPath by měl vrátit jednu primitivní hodnotu, která bude použita k vydání dalšího požadavku. |

**Příklad:**

Rozhraní FACEBOOK Graph API vrátí odpověď v následující struktuře, v takovém případě je adresa URL následující stránky reprezentována v ***stránkování.next***:

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

Odpovídající konfigurace zdroje aktivity `paginationRules` kopírování REST, zejména je následující:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Použití OAuth
Tato část popisuje, jak pomocí šablony řešení kopírovat data z konektoru REST do Azure Data Lake Storage ve formátu JSON pomocí OAuth. 

### <a name="about-the-solution-template"></a>Šablona řešení

Šablona obsahuje dvě aktivity:
- **Webová** aktivita načte token nosiče a pak jej předá následné copy aktivity jako autorizace.
- **Kopírování** aktivity zkopíruje data z REST do Azure Data Lake Storage.

Šablona definuje dva parametry:
- **SinkContainer** je cesta kořenové složky, do které se data zkopírují ve vašem azure úložiště datového jezera. 
- **SinkDirectory** je cesta k adresáři pod kořenem, kde se data zkopírují do úložiště Azure Data Lake. 

### <a name="how-to-use-this-solution-template"></a>Použití této šablony řešení

1. Přejděte na kopii z REST nebo HTTP pomocí šablony **OAuth.** Vytvořte nové připojení pro zdrojové připojení. 
    ![Vytvoření nových připojení](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Níže jsou uvedeny klíčové kroky pro nastavení nových propojených služeb (REST):
    
     1. V části **Základní adresa URL**zadejte parametr url pro vlastní zdrojovou službu REST. 
     2. Pro **typ ověřování**zvolte *Anonymní*.
        ![Nové připojení REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Vytvořte nové připojení pro cílové připojení.  
    ![Nové připojení Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Vyberte **Použít tuto šablonu**.
    ![Použít tuto šablonu](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Zobrazí se vytvořený kanál, jak je ![znázorněno v následujícím příkladu: Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Vyberte **aktivitu na webu.** V **nastavení**zadejte odpovídající **adresu URL**, **metodu**, **záhlaví**a **tělo** pro načtení tokenu nosiče OAuth z přihlašovacího rozhraní API služby, ze které chcete kopírovat data. Zástupný symbol v šabloně představuje ukázku OAuth služby Azure Active Directory (AAD). Poznámka: Ověřování AAD je nativně podporováno konektorem REST, zde je pouze příklad toku OAuth. 

    | Vlastnost | Popis |
    |:--- |:--- |:--- |
    | zprostředkovatele identity |Zadejte adresu url, ze které chcete načíst token nosiče OAuth. například ve vzorku zde je tohttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metoda | Metoda HTTP. Povolené hodnoty jsou **Zaúčtovat** a **získat**. | 
    | Hlavičky | Hlavička je definována uživatelem, která odkazuje na jeden název záhlaví v požadavku HTTP. | 
    | Tělo | Tělo pro požadavek HTTP. | 

    ![Kanál](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. V **aktivitě Kopírovat data** vyberte kartu *Zdroj,* můžete vidět, že token nosiče (access_token) načtený z předchozího kroku by byl předán kopírovat aktivitu dat jako **autorizaci** v části Další záhlaví. Před spuštěním spuštění kanálu potvrďte nastavení následujících vlastností.

    | Vlastnost | Popis |
    |:--- |:--- |:--- | 
    | Metoda požadavku | Metoda HTTP. Povolené hodnoty jsou **Get** (výchozí) a **Post**. | 
    | Další záhlaví | Další hlavičky požadavku HTTP.| 

   ![Ověřování zdroje kopírování](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Vyberte **Ladit**, zadejte **parametry**a pak vyberte **Dokončit**.
   ![Spuštění kanálu](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Po úspěšném dokončení spuštění kanálu se zobrazí výsledek podobný následujícímu příkladu: ![Výsledek spuštění kanálu](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klikněte na ikonu "Výstup" ve sloupci WebActivity ve **sloupci Akce,** zobrazí se access_token vrácená službou.

   ![Výstup tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klikněte na ikonu "Vstup" copyactivity ve **sloupci Akce,** uvidíte access_token načtený WebActivity je předán CopyActivity pro ověřování. 

    ![Vstup tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Chcete-li zabránit protokolovanému tokenu ve formátu prostého textu, povolte "Zabezpečený výstup" ve webové aktivitě a "Zabezpečený vstup" v aktivitě kopírování.


## <a name="export-json-response-as-is"></a>Exportovat odpověď JSON jako-je

Tento konektor REST můžete použít k exportu odpovědi REST API JSON jako-je do různých úložišť založených na souborech. Chcete-li dosáhnout takové schéma-agnostik kopírování, přeskočte část "struktura" (také volal *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>Mapování schématu

Chcete-li zkopírovat data z koncového bodu REST do tabulkového jímky, podívejte se na [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která podporují aktivity kopírování jako zdroje a propady v Azure Data Factory, najdete v [tématu Podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
