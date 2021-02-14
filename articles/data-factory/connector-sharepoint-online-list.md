---
title: Kopírování dat ze seznamu SharePointu Online pomocí Azure Data Factory
description: Naučte se, jak kopírovat data ze seznamu SharePointu Online do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: 3f05c90ba3c7e6b47009cbb597c56dac8a01427a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393424"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Kopírování dat ze seznamu SharePointu Online pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat ze seznamu SharePointu Online. Článek se vytvoří na [aktivitě kopírování v Azure Data Factory](copy-activity-overview.md), která představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor seznamu SharePointu Online se podporuje pro následující aktivity:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data ze seznamu SharePointu Online můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tématu [podporované úložiště a formáty dat](copy-activity-overview.md#supported-data-stores-and-formats).

Konkrétně tento konektor online v SharePointovém seznamu používá ověřování instančního objektu a načítá data prostřednictvím protokolu OData.

> [!TIP]
> Tento konektor podporuje kopírování dat ze **seznamu** SharePointu Online, ale ne ze souboru. Přečtěte si, jak kopírovat soubor z oddílu [Kopírovat soubor ze SharePointu Online](#copy-file-from-sharepoint-online) .

## <a name="prerequisites"></a>Požadavky

Konektor online seznamu SharePointu používá pro připojení k SharePointu ověřování instančního objektu. Pomocí těchto kroků ji nastavte:

1. Pomocí [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělení oprávnění webu služby SharePoint Online pro registrovanou aplikaci: 

    > [!NOTE]
    > Tato operace vyžaduje oprávnění vlastníka webu SharePointu Online. Vlastníka můžete najít tak, že kliknete na domovskou stránku webu – > klikněte na "X" v pravém horním rohu a > ověřte, kdo má roli Owner (Vlastník).

    1. Otevřete odkaz na web SharePointu Online `https://[your_site_url]/_layouts/15/appinv.aspx` , např. (nahraďte adresu URL webu).
    2. Vyhledejte ID aplikace, které jste zaregistrovali, vyplňte prázdná pole a klikněte na vytvořit.

        - Doména aplikace: `localhost.com`
        - Adresa URL pro přesměrování: `https://www.localhost.com`
        - XML žádosti o oprávnění:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![SharePoint – udělit oprávnění](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Klikněte na důvěřovat IT pro tuto aplikaci.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které můžete použít k definování Data Factory entit, které jsou specifické pro konektor služby SharePoint Online list.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu seznamu SharePointu Online jsou podporovány následující vlastnosti:

| **Vlastnost**        | **Popis**                                              | **Povinné** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| typ                | Vlastnost Type musí být nastavená na: **SharePointOnlineList**.  | Yes          |
| siteUrl             | Adresa URL webu SharePointu Online, např `https://contoso.sharepoint.com/sites/siteName` . | Yes          |
| servicePrincipalId  | ID aplikace (klienta) aplikace zaregistrované v Azure Active Directory. | Yes          |
| servicePrincipalKey | Klíč aplikace Označte toto pole jako **SecureString** , abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Yes          |
| tenantId            | ID tenanta, pod kterým se vaše aplikace nachází.          | Yes          |
| connectVia          | [Integration runtime](concepts-integration-runtime.md) , který se má použít pro připojení k úložišti dat. Další informace o [požadavcích](#prerequisites)najdete výše v tomto článku. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No           |

**Příklad:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [datové sady a propojené služby](concepts-datasets-linked-services.md). V následující části najdete seznam vlastností podporovaných datovou sadou tabulky SAP.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** datové sady musí být nastavená na **SharePointOnlineLResource**. | Yes |
| listName | Název seznamu SharePointu Online | Yes |

**Příklad**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [kanály](concepts-pipelines-activities.md).  V následující části najdete seznam vlastností podporovaných zdrojem seznamu SharePointu Online.

### <a name="sharepoint-online-list-as-source"></a>Seznam SharePointu Online jako zdroj

Pro kopírování dat ze seznamu SharePointu Online jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na **SharePointOnlineListSource**. | Yes |
| query | Vlastní možnosti dotazů OData pro filtrování dat Příklad: `"$top=10&$select=Title,Number"`. | No |
| httpRequestTimeout | Časový limit (v sekundách), po který má požadavek HTTP získat odpověď. Výchozí hodnota je 300 (5 minut). | No |

**Příklad**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> V Azure Data Factory nemůžete pro zdroj seznamu SharePointu Online vybrat více než jeden datový typ *volby* .

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapování datových typů pro seznam SharePointu Online

Při kopírování dat ze seznamu SharePointu Online se používají následující mapování mezi datovými typy seznamu SharePointu Online a Azure Data Factory pro dočasné datové typy. 

| **Datový typ SharePointu Online**                 | **Datový typ OData**                                  | **Azure Data Factory pomocný datový typ** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Jeden řádek textu                             | Edm.String                                           | Řetězec                                   |
| Více řádků textu                          | Edm.String                                           | Řetězec                                   |
| Volba (nabídka pro výběr)                    | Edm.String                                           | Řetězec                                   |
| Číslo (1, 1,0, 100)                            | Edm.Double                                           | dvojité                                   |
| Měna ($, y, &euro; )                              | Edm.Double                                           | dvojité                                   |
| Datum a čas                                   | EDM. DateTime                                         | DateTime                                 |
| Vyhledávání (informace již na tomto webu)       | Edm.Int32                                            | Int32                                    |
| Ano/ne (zaškrtávací políčko)                              | Edm.Boolean                                          | Logická hodnota                                  |
| Osoba nebo Skupina                                 | Edm.Int32                                            | Int32                                    |
| Hypertextový odkaz nebo obrázek                            | Edm.String                                           | Řetězec                                   |
| Počítané (výpočet na základě jiných sloupců) | EDM. String/EDM. Double/EDM. DateTime/EDM. Boolean | String, Double/DateTime/Boolean     |
| Příloha                                      | Nepodporováno                                        |                                          |
| Výsledek úkolu                                    | Nepodporováno                                        |                                          |
| Externí data                                   | Nepodporováno                                        |                                          |
| Spravovaná metadata                                | Nepodporováno                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Kopírovat soubor ze SharePointu Online

Můžete zkopírovat soubor ze SharePointu Online pomocí **aktivity webu** pro ověření a získání přístupového tokenu z spo a pak předání do následné **aktivity kopírování** ke zkopírování dat pomocí **konektoru http jako zdroje**.

![tok souborů kopie SharePointu](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Pokud chcete vytvořit aplikaci AAD a udělit oprávnění k SharePointu Online, postupujte podle pokynů v části [požadavky](#prerequisites) . 

2. Vytvoření **aktivity webu** pro získání přístupového tokenu ze SharePointu Online:

    - **Adresa URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Nahraďte ID tenanta.
    - **Metoda**: post
    - **Hlavičky**:
        - Content-Type: application/x-www-form-urlencoded
    - **Tělo**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Nahraďte ID klienta, tajný klíč klienta, ID tenanta a název tenanta.

    > [!CAUTION]
    > Nastavte možnost zabezpečený výstup na hodnotu true v aktivitě webu, aby se zabránilo přihlášení hodnoty tokenu do prostého textu. Všechny další aktivity, které tuto hodnotu využívají, by měly mít možnost zabezpečeného vstupu nastavenou na hodnotu true.

3. Řetězení s **aktivitou kopírování** s konektorem http jako se zdrojem pro kopírování obsahu souborů SharePointu Online:

    - Propojená služba HTTP:
        - **Základní adresa URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Nahraďte adresu URL webu a relativní cestu k souboru. Ukázková relativní cesta k souboru jako `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Typ ověřování:** Anonymní *(pro použití tokenu nosiče nakonfigurovaného ve zdroji aktivity kopírování později)*
    - Datová sada: vyberte formát, který chcete. Pokud chcete soubor zkopírovat tak, jak je, vyberte typ Binary.
    - Zdroj aktivity kopírování:
        - **Metoda požadavku**: Get
        - **Další záhlaví**: použijte následující výraz `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , který používá token nosiče generovaný nadřazeným webem aktivity jako autorizační záhlaví. Nahraďte název webové aktivity.
    - Nakonfigurujte jímku aktivity kopírování obvyklým způsobem.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky v Azure Data Factory, najdete v tématu [podporovaná úložiště dat a formáty](copy-activity-overview.md#supported-data-stores-and-formats).
