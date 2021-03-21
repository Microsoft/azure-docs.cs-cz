---
title: Kopírování dat z a do koncového bodu REST pomocí Azure Data Factory
description: Naučte se kopírovat data z cloudu nebo místního zdroje REST do podporovaných úložišť dat jímky nebo z podporovaného zdrojového úložiště dat do jímky REST pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: jingwang
ms.openlocfilehash: 45e71b636d43633d5b157db2815ddd19c31395b3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608125"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Kopírování dat z a do koncového bodu REST pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do koncového bodu REST. Článek se vytvoří na [aktivitě kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

Rozdíl mezi tímto konektorem REST, [konektorem http](connector-http.md)a [konektorem webové tabulky](connector-web-table.md) :

- **Konektor REST** přímo podporuje kopírování dat z rozhraní API RESTful. 
- **Konektor http** je obecný k načtení dat z libovolného koncového bodu http, například ke stažení souboru. Než začnete s tímto konektorem REST, můžete k tomu použít konektor HTTP ke kopírování dat z rozhraní RESTful API, které je podporované, ale míň funkční porovnávání s konektorem REST.
- **Konektor webové tabulky** extrahuje obsah tabulky z webové stránky HTML.

## <a name="supported-capabilities"></a>Podporované funkce

Data ze zdroje REST můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do jímky REST. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tématu [podporované úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento obecný konektor REST podporuje:

- Kopírování dat z koncového bodu REST pomocí metod **Get** nebo **post** a zkopírování dat do koncového bodu REST pomocí metod **post**, **Put** nebo **patch** .
- Kopírování dat pomocí jednoho z následujících ověřování: **anonymní**, **základní**, **AAD instanční objekt** a **spravované identity pro prostředky Azure**.
- **[Stránkování](#pagination-support)** v rozhraní REST API.
- Pro REST as source zkopírujte odpověď REST JSON tak, [jak je](#export-json-response-as-is) , nebo ji Analyzujte pomocí [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping). Je podporována pouze datová část odpovědi ve formátu **JSON** .

> [!TIP]
> Chcete-li otestovat požadavek na načtení dat před konfigurací konektoru REST v Data Factory, přečtěte si informace o specifikaci rozhraní API pro požadavky hlaviček a textu. K ověření můžete použít nástroje, jako je například nástroj pro odeslání nebo webový prohlížeč.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit specifických pro konektor REST.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu REST jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **RestService**. | Yes |
| url | Základní adresa URL služby REST. | Yes |
| enableServerCertificateValidation | Zda se má při připojování ke koncovému bodu ověřit certifikát TLS/SSL na straně serveru. | No<br /> (výchozí hodnota je **true**) |
| authenticationType | Typ ověřování, který se používá pro připojení ke službě REST Povolené hodnoty jsou **anonymní**, **základní**, **AadServicePrincipal** a **ManagedServiceIdentity**. Uživatel s podporou OAuth není podporován. Ve vlastnosti můžete také nakonfigurovat ověřovací hlavičky `authHeader` . Další informace a příklady najdete v odpovídajících částech.| Yes |
| authHeaders | Další hlavičky požadavků HTTP pro ověřování.<br/> Pokud například chcete použít ověřování pomocí klíče rozhraní API, můžete jako typ ověřování vybrat "anonymní" a v hlavičce zadat klíč rozhraní API. | No |
| connectVia | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Další informace najdete v části [požadavky](#prerequisites) . Pokud tento parametr nezadáte, použije tato vlastnost výchozí Azure Integration Runtime. |No |

### <a name="use-basic-authentication"></a>Použít základní ověřování

Nastavte vlastnost **AuthenticationType** na hodnotu **Basic**. Kromě obecných vlastností, které jsou popsány v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| userName | Uživatelské jméno, které se má použít pro přístup ke koncovému bodu REST. | Yes |
| heslo | Heslo pro uživatele (hodnota uživatelského **jména** ). Označte toto pole jako typ **SecureString** a bezpečně ho uložte do Data Factory. Můžete také [odkazovat na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

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

### <a name="use-aad-service-principal-authentication"></a>Použít ověřování instančního objektu služby AAD

Nastavte vlastnost **AuthenticationType** na **AadServicePrincipal**. Kromě obecných vlastností, které jsou popsány v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta Azure Active Directory aplikace. | Yes |
| servicePrincipalKey | Zadejte klíč Azure Active Directory aplikace. Označte toto pole jako **SecureString** , abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Yes |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci, například `https://management.core.windows.net` .| Yes |
| azureCloudType | Pro ověřování instančního objektu zadejte typ cloudového prostředí Azure, do kterého se zaregistruje vaše aplikace AAD. <br/> Povolené hodnoty jsou **AzurePublic**, **AzureChina**, **AzureUsGovernment** a **AzureGermany**. Ve výchozím nastavení se používá cloudové prostředí pro datovou továrnu. | No |

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Použití spravovaných identit pro ověřování prostředků Azure

Nastavte vlastnost **AuthenticationType** na **ManagedServiceIdentity**. Kromě obecných vlastností, které jsou popsány v předchozí části, zadejte následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| aadResourceId | Zadejte prostředek AAD, který požadujete pro autorizaci, například `https://management.core.windows.net` .| Yes |

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

### <a name="using-authentication-headers"></a>Použití ověřovacích hlaviček

Kromě toho můžete nakonfigurovat hlavičky požadavků pro ověřování spolu s vestavěnými typy ověřování.

**Příklad: použití ověřování pomocí klíče rozhraní API**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
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

V této části najdete seznam vlastností, které datová sada REST podporuje. 

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). 

Chcete-li kopírovat data z REST, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **RestResource**. | Yes |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud tato vlastnost není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. Konektor HTTP kopíruje data z kombinované adresy URL: `[URL specified in linked service]/[relative URL specified in dataset]` . | No |

Pokud jste nastavování `requestMethod` , `additionalHeaders` , `requestBody` a `paginationRules` v datové sadě, je stále podporováno tak, jak jsou, a když jste se rozhodli použít nový model v aktivitě, který se chystá dál.

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

V této části najdete seznam vlastností podporovaných zdrojem a jímkou REST.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST as source

V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **RestSource**. | Yes |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** (default) a **post**. | No |
| additionalHeaders | Další hlavičky požadavku HTTP | No |
| částmi | Tělo požadavku HTTP | No |
| paginationRules | Pravidla stránkování pro vytváření žádostí o další stránku. Podrobnosti najdete v části [Podpora stránkování](#pagination-support) . | No |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. Výchozí hodnota je **00:01:40**.  | No |
| requestInterval | Doba, po kterou se má čekat před odesláním žádosti o další stránku. Výchozí hodnota je **00:00:01** . |  No |

>[!NOTE]
>Konektor REST ignoruje všechna zadaná záhlaví Accept v `additionalHeaders` . Protože konektor REST podporuje pouze odpověď ve formátu JSON, automaticky vygeneruje hlavičku `Accept: application/json` .

**Příklad 1: použití metody Get se stránkováním**

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

**Příklad 2: použití metody post**

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

### <a name="rest-as-sink"></a>REST jako jímka

V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na **RestSink**. | Yes |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **post** (výchozí), **Put** a **patch**. | No |
| additionalHeaders | Další hlavičky požadavku HTTP | No |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Tato hodnota představuje časový limit pro získání odpovědi, nikoli časový limit pro zápis dat. Výchozí hodnota je **00:01:40**.  | No |
| requestInterval | Doba intervalu mezi různými požadavky v milisekundách. Hodnota intervalu žádosti musí být číslo v rozsahu [10, 60000]. |  No |
| httpCompressionType | Typ komprese HTTP, který se má použít při odesílání dat s optimální úrovní komprese. Povolené hodnoty jsou **none** a **gzip**. | No |
| writeBatchSize | Počet záznamů, které mají být zapsány do jímky REST na dávku. Výchozí hodnota je 10000. | No |

Konektor REST jako jímka funguje s rozhraními REST API, která přijímají JSON. Data budou odeslána ve formátu JSON s následujícím vzorem. Podle potřeby můžete pomocí [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping) aktivity kopírování změnit tvar zdrojových dat tak, aby splňoval očekávanou datovou část REST API.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Podpora stránkování

Při kopírování dat z rozhraní REST API obvykle REST API omezuje velikost datové části odpovědi na jednu žádost v rozumném čísle. i když vrátí velké množství dat, rozdělí výsledek na více stránek a vyžaduje volajícím odeslání po sobě jdoucích požadavků na získání další stránky výsledku. Požadavek na jednu stránku je obvykle dynamický a skládá se z informací vrácených z odpovědi na předchozí stránku.

Tento obecný konektor REST podporuje následující vzory stránkování: 

* Absolutní nebo relativní adresa URL nové žádosti = hodnota vlastnosti v aktuálním textu odpovědi
* Absolutní nebo relativní URL nové žádosti = hodnota hlavičky v aktuálních hlavičkách odpovědi
* Parametr dotazu další žádosti = hodnota vlastnosti v aktuálním textu odpovědi
* Parametr dotazu další žádosti = hodnota hlavičky v aktuálních hlavičkách odpovědi
* Hlavička další žádosti = hodnota vlastnosti v aktuálním těle odpovědi
* Hlavička další žádosti = hodnota hlavičky v aktuálních hlavičkách odpovědi

**Pravidla stránkování** jsou definována jako slovník v datové sadě, který obsahuje jeden nebo více párů klíč-hodnota s rozlišováním velkých a malých písmen. Konfigurace se použije k vygenerování požadavku od druhé stránky. Konektor ukončí iteraci, když Získá stavový kód HTTP 204 (žádný obsah) nebo žádný z výrazů JSONPath v "paginationRules" vrátí hodnotu null.

**Podporované klíče** v pravidlech stránkování:

| Klíč | Popis |
|:--- |:--- |
| AbsoluteUrl | Označuje adresu URL pro vydání dalšího požadavku. Může to být **buď absolutní adresa URL, nebo relativní adresa URL**. |
| QueryParameters. *request_query_parameter* NEBO QueryParameters [' request_query_parameter '] | "request_query_parameter" je definováno uživatelem, který odkazuje na jeden název parametru dotazu v další adrese URL požadavku HTTP. |
| Záhlaví. *request_header* NEBO hlavičky [' request_header '] | "request_header" je definováno uživatelem, které odkazuje na jeden název záhlaví v další žádosti HTTP. |

**Podporované hodnoty** v pravidlech stránkování:

| Hodnota | Popis |
|:--- |:--- |
| Záhlaví. *response_header* NEBO hlavičky [' response_header '] | "response_header" je definováno uživatelem, které odkazuje na jeden název záhlaví v aktuální odpovědi HTTP, hodnota, která bude použita k vystavení dalšího požadavku. |
| Výraz JSONPath začínající znakem "$" (představuje kořen textu odpovědi) | Tělo odpovědi by mělo obsahovat pouze jeden objekt JSON. Výraz JSONPath by měl vracet jedinou primitivní hodnotu, která bude použita k vystavení dalšího požadavku. |

**Příklad:**

Facebook Graph API vrátí odpověď v následující struktuře, kde adresa URL další stránky je reprezentovaná na ***stránkování. další***:

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

Odpovídající konfigurace zdroje aktivity pro kopírování REST je obzvláště `paginationRules` následující:

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
Tato část popisuje, jak použít šablonu řešení ke kopírování dat z konektoru REST do Azure Data Lake Storage ve formátu JSON pomocí OAuth. 

### <a name="about-the-solution-template"></a>O šabloně řešení

Šablona obsahuje dvě aktivity:
- Aktivita **webu** načte nosný token a pak ho předá do následné aktivity kopírování jako autorizaci.
- Aktivita **kopírování** kopíruje data z REST do Azure Data Lake Storage.

Šablona definuje dva parametry:
- **SinkContainer** je cesta ke kořenové složce, do které se zkopírují data do svého Azure Data Lake Storage. 
- **SinkDirectory** je cesta k adresáři v kořenovém adresáři, kam se zkopírují data do Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Jak používat tuto šablonu řešení

1. Přejděte na adresu **REST nebo HTTP pomocí šablony OAuth** . Vytvořte nové připojení pro zdrojové připojení. 
    ![Vytvořit nová připojení](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Níže jsou uvedené klíčové kroky pro nastavení nové propojené služby (REST):
    
     1. V části **základní adresa URL** zadejte parametr adresy URL pro vlastní zdrojovou službu REST. 
     2. Jako **typ ověřování** vyberte *Anonymous (anonymní*).
        ![Nové připojení REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Vytvoří nové připojení pro cílové připojení.  
    ![Nové připojení Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Vyberte **Použít tuto šablonu**.
    ![Použít tuto šablonu](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Zobrazí se vytvořený kanál, jak je znázorněno v následujícím příkladu:  ![ snímek obrazovky zobrazuje kanál vytvořený ze šablony.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Vyberte aktivitu **webu** . V **Nastavení** zadejte odpovídající **adresu URL**, **metodu**, **záhlaví** a **text** pro načtení tokenu Bearer OAuth z rozhraní API pro přihlášení služby, ze které chcete kopírovat data. Zástupný symbol v šabloně prezentuje ukázku protokolu OAuth pro Azure Active Directory (AAD). Poznámka: ověřování AAD je nativně podporované konektorem REST, tady je jenom příklad toku OAuth. 

    | Vlastnost | Popis |
    |:--- |:--- |:--- |
    | URL |Zadejte adresu URL, ze které se má načíst token nosiče OAuth. například v ukázce tady je https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metoda | Metoda HTTP. Povolené hodnoty jsou **post** a **Get**. | 
    | Hlavičky | Záhlaví je definováno uživatelem, které odkazuje na jeden název záhlaví v požadavku HTTP. | 
    | Text | Tělo požadavku HTTP | 

    ![Kanál](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. V části aktivita **kopírování dat** vyberte kartu *zdroj* . můžete vidět, že nosný token (access_token) načtený z předchozího kroku se předává aktivitě kopírovat data jako **autorizace** v dalších hlavičkách. Před spuštěním spuštění kanálu potvrďte nastavení následujících vlastností.

    | Vlastnost | Popis |
    |:--- |:--- |:--- | 
    | Request – metoda | Metoda HTTP. Povolené hodnoty jsou **Get** (default) a **post**. | 
    | Další záhlaví | Další hlavičky požadavku HTTP| 

   ![Kopírovat ověření zdroje](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Vyberte **ladit**, zadejte **parametry** a pak vyberte **Dokončit**.
   ![Spuštění kanálu](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Po úspěšném dokončení kanálu by se zobrazil výsledek podobný následujícímu příkladu: ![ výsledek spuštění kanálu](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Klikněte na ikonu "výstup" aktivity webactivity ve sloupci **Actions (akce** ). zobrazí se access_token vrácená službou.

   ![Výstup tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Klikněte na ikonu Input (vstup) CopyActivity ve sloupci **Actions (akce** ). zobrazí se zpráva, Access_token že aktivita získaná funkcí webactivity se předává CopyActivity pro ověřování. 

    ![Vstup tokenu](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Aby nedocházelo k přihlašování do prostého textu, povolte v aktivitě kopírování "zabezpečený výstup" v aktivitě web a "zabezpečený vstup".


## <a name="export-json-response-as-is"></a>Exportovat odpověď JSON tak, jak je

Pomocí tohoto konektoru REST můžete exportovat REST API odpověď JSON tak, jak se nachází v různých úložištích založených na souborech. Chcete-li dosáhnout takového schématu – nezávislá kopírování, přeskočte oddíl Structure (označuje se také jako *schéma*) v datové sadě a mapování schématu v aktivitě kopírování.

## <a name="schema-mapping"></a>Mapování schématu

Chcete-li kopírovat data z koncového bodu REST do tabulkové jímky, přečtěte si téma [mapování schématu](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
