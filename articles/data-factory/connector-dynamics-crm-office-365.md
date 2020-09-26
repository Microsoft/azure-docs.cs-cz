---
title: Kopírovat data v aplikaci Dynamics (Common Data Service)
description: Naučte se, jak kopírovat data z Microsoft Dynamics CRM nebo Microsoft Dynamics 365 (Common Data Service) do podporovaných úložišť dat jímky nebo z podporovaných zdrojových úložišť dat do Dynamics CRM nebo Dynamics 365 pomocí aktivity kopírování v kanálu služby Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/23/2020
ms.openlocfilehash: 942cbda3652692acc8eedf2ec9508bb501a60547
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332096"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 (Common Data Service) nebo Dynamics CRM pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Microsoft Dynamics 365 a Microsoft Dynamics CRM. Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje a jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Dynamics 365 (Common Data Service) nebo Dynamics CRM můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do Dynamics 365 (Common Data Service) nebo Dynamics CRM. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Tento Dynamics Connector podporuje Dynamics verze 7 až 9 pro online i místní prostředí. A konkrétně:

- Verze 7 se mapuje na Dynamics CRM 2015.
- Verze 8 se mapuje na Dynamics CRM 2016 a dřívější verzi Dynamics 365.
- Verze 9 se mapuje na novější verzi Dynamics 365.

Podívejte se na následující tabulku podporovaných typů a konfigurací ověřování pro verze a produkty Dynamics.

| Verze Dynamics | Typy ověřování | Ukázky propojených služeb |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM Online | Objekt služby Azure Active Directory (Azure AD) <br/><br/> Office 365 | [Služby Dynamics Online a Azure AD – zabezpečení nebo ověřování Office 365](#dynamics-365-and-dynamics-crm-online) |
| Místní Dynamics 365 s internetovým nasazením (IFD) <br/><br/> Dynamics CRM 2016 v místním prostředí s IFD <br/><br/> Dynamics CRM 2015 v místním prostředí s IFD | IFD | [Místní Dynamics s ověřováním pomocí internetového nasazení a internetového nasazení](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Pro Dynamics 365 jsou podporovány následující typy aplikací:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Tento konektor nepodporuje jiné typy aplikací, jako je finance, operace a talentů.

Tento konektor Dynamics je postaven nad [nástroji Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Pokud chcete kopírovat data z Dynamics 365 finance a operací, můžete použít [konektor Dynamics AX](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete tento konektor používat s ověřováním instančního objektu služby Azure AD, musíte v Common Data Service nebo Dynamics nastavit ověřování S2S (Server-to-Server). Podrobné pokyny najdete v [tomto článku](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) .

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro aplikaci Dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 a Dynamics CRM Online

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na "Dynamics", "DynamicsCrm" nebo "CommonDataServiceForApps". | Yes |
| Typ nasazení | Typ nasazení instance Dynamics Hodnota musí být online pro Dynamics Online. | Yes |
| serviceUri | Adresa URL služby vaší instance Dynamics, kterou jste přistupují z prohlížeče. Příkladem je "https:// \<organization-name> . CRM [x]. Dynamics. com". | Yes |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Platné hodnoty jsou "AADServicePrincipal" a "Office 365". | Yes |
| servicePrincipalId | ID klienta aplikace Azure AD | Ano, pokud je ověřování "AADServicePrincipal" |
| servicePrincipalCredentialType | Typ přihlašovacích údajů, který se má použít pro ověřování instančního objektu. Platné hodnoty jsou "ServicePrincipalKey" a "ServicePrincipalCert". | Ano, pokud je ověřování "AADServicePrincipal" |
| servicePrincipalCredential | Přihlašovací údaje instančního objektu. <br/><br/>Když jako typ přihlašovacích údajů použijete "ServicePrincipalKey", `servicePrincipalCredential` může se jednat o řetězec, který Azure Data Factory šifrování při nasazení propojené služby. Nebo se může jednat o odkaz na tajný kód v Azure Key Vault. <br/><br/>Pokud jako přihlašovací údaje použijete "ServicePrincipalCert", `servicePrincipalCredential` musí se jednat o odkaz na certifikát v Azure Key Vault. | Ano, pokud je ověřování "AADServicePrincipal" |
| username | Uživatelské jméno pro připojení k Dynamics. | Ano, pokud je ověřování "Office 365" |
| heslo | Heslo pro uživatelský účet, který jste zadali jako uživatelské jméno. Označte toto pole pomocí "SecureString", abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano, pokud je ověřování "Office 365" |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud není zadaná žádná hodnota, použije vlastnost výchozí prostředí Azure Integration runtime. | Ne pro zdroj a Ano pro jímku, pokud zdrojová propojená služba nemá modul runtime integrace |

>[!NOTE]
>Konektor Dynamics dřív používal volitelnou vlastnost **NázevOrganizace** k identifikaci vaší online instance Dynamics CRM nebo Dynamics 365. I když tato vlastnost stále funguje, doporučujeme místo toho zadat novou vlastnost **ServiceUri** , abyste získali lepší výkon pro zjišťování instancí.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Příklad: Dynamics Online s použitím služby Azure AD – zabezpečení a ověřování klíčů

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Příklad: Dynamics Online s využitím služby Azure AD – zabezpečení a ověřování certifikátů

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Příklad: Dynamics Online s použitím ověřování Office 365

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 a Dynamics CRM v místním prostředí s IFD

Další vlastnosti, které se porovnávají s Dynamics Online, jsou **název hostitele** a **port**.

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na "Dynamics", "DynamicsCrm" nebo "CommonDataServiceForApps". | Ano. |
| Typ nasazení | Typ nasazení instance Dynamics Tato hodnota musí být "OnPremisesWithIfd" pro místní prostředí Dynamics pomocí internetového nasazení.| Ano. |
| Název hostitele | Název hostitele místního Dynamics serveru. | Ano. |
| port | Port místního Dynamics serveru. | No. Výchozí hodnota je 443. |
| Organizace | Název organizace instance Dynamics | Ano. |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Zadejte "IFD" pro místní prostředí Dynamics pomocí internetového nasazení. | Ano. |
| username | Uživatelské jméno pro připojení k Dynamics. | Ano. |
| heslo | Heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole můžete označit jako "SecureString" a bezpečně ho ukládat do Data Factory. Můžete také uložit heslo v Key Vault a nechat si aktivitu kopírování vyžádat z toho, kdy se data zkopírují. Další informace o [přihlašovacích údajích pro Store najdete v Key Vault](store-credentials-in-key-vault.md). | Ano. |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud není zadaná žádná hodnota, použije vlastnost výchozí prostředí Azure Integration runtime. | Ne pro zdroj a Ano pro jímku |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Příklad: místní prostředí Dynamics s IFD pomocí ověřování IFD

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných sadou Dynamics DataSet.

Chcete-li kopírovat data z a do Dynamics, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na "DynamicsEntity", "DynamicsCrmEntity" nebo "CommonDataServiceForAppsEntity". |Yes |
| entityName | Logický název entity, která se má načíst | Ne pro zdroj, pokud je zdroj aktivity zadaný jako "dotaz" a Ano pro jímku |

#### <a name="example"></a>Příklad

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a typy jímky Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ zdroje

Chcete-li kopírovat data z Dynamics, část **zdroje** aktivity kopírování podporuje následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavena na hodnotu "DynamicsSource", "DynamicsCrmSource" nebo "CommonDataServiceForAppsSource". | Yes |
| query | FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics Online i v místním prostředí. Prohlédněte si následující příklad. Další informace najdete v tématu [sestavování dotazů pomocí FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Ne, pokud `entityName` je zadaná datová sada |

>[!NOTE]
>Sloupec PK bude vždy zkopírován i v případě, že projekce sloupce, kterou nakonfigurujete v dotazu FetchXML, ji neobsahuje.

> [!IMPORTANT]
>- Když kopírujete data z Dynamics, explicitní mapování sloupce z Dynamics na jímku je volitelné. Důrazně doporučujeme mapování, abychom zajistili deterministické výsledky kopírování.
>- Když Data Factory importuje schéma v uživatelském rozhraní pro vytváření obsahu, odvodí schéma. Provede tak vzorkováním horních řádků z výsledku dotazu Dynamics pro inicializaci seznamu zdrojového sloupce. V takovém případě jsou sloupce bez hodnot v horních řádcích vynechány. Stejné chování platí pro spuštění kopírování, pokud neexistuje explicitní mapování. Můžete zkontrolovat a přidat do mapování další sloupce, které jsou během kopírování za běhu dodrženy.

#### <a name="example"></a>Příklad

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Ukázkový dotaz FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamika jako typ jímky

Pro kopírování dat do Dynamics je v části **jímka** aktivity kopírování podporována následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type jímky aktivity kopírování musí být nastavená na "DynamicsSink", "DynamicsCrmSink" nebo "CommonDataServiceForAppsSink". | Ano. |
| writeBehavior | Chování operace zápisu. Hodnota musí být "Upsert". | Yes |
| alternateKeyName | Název alternativního klíče definovaný v entitě pro Upsert. | No. |
| writeBatchSize | Počet řádků dat zapsaných do Dynamics v každé dávce. | No. Výchozí hodnota je 10. |
| ignoreNullValues | Určuje, zda se mají ignorovat hodnoty null ze vstupních dat kromě klíčových polí během operace zápisu.<br/><br/>Platné hodnoty jsou **true** a **false**:<ul><li>**True**: když provedete operaci Upsert nebo Update, ponechte data v cílovém objektu beze změny. Při operaci INSERT vložte definovanou výchozí hodnotu.</li><li>**False**: Pokud provádíte operaci Upsert nebo Update, aktualizujte data v cílovém objektu na hodnotu null. Při operaci vložení vložte hodnotu null.</li></ul> | No. Výchozí hodnota je **false (NEPRAVDA**). |

>[!NOTE]
>Výchozí hodnota pro **writeBatchSize** jímky a aktivita kopírování **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** pro jímku aplikace Dynamics je 10. Proto jsou záznamy 100 souběžně odesílány ve výchozím nastavení do Dynamics.

V případě Dynamics 365 online existuje omezení [dvou souběžných volání dávky na jednu organizaci](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). V případě překročení tohoto limitu je vyvolána výjimka "Server zaneprázdněna" před prvním požadavkem, který je někdy spuštěn. Nechte **writeBatchSize** 10 nebo méně, aby se zabránilo takovému omezení souběžných volání.

Optimální kombinace **writeBatchSize** a **parallelCopies** závisí na schématu vaší entity. Prvky schématu zahrnují počet sloupců, velikost řádku a počet modulů plug-in, pracovní postupy nebo aktivity pracovního postupu, které jsou k těmto voláním zaúčtovány. Výchozím nastavením **writeBatchSize** (10) &times; **parallelCopies** (10) je doporučení podle služby Dynamics. Tato hodnota funguje pro většinu entit Dynamics, i když nemusí poskytovat nejlepší výkon. Výkon můžete vyladit úpravou kombinace v nastavení aktivity kopírování.

#### <a name="example"></a>Příklad

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapování datových typů pro Dynamics

Když kopírujete data z Dynamics, v následující tabulce jsou uvedena mapování z datových typů Dynamics na Data Factory dočasných datových typů. Informace o tom, jak se aktivita kopírování mapuje ke zdrojovému schématu a datový typ se mapuje na jímku, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající datový typ Data Factory ve struktuře datové sady, která je založená na vašem zdrojovém datovém typu Dynamics, pomocí následující tabulky mapování:

| Datový typ Dynamics | Data Factory pomocný datový typ | Podporováno jako zdroj | Podporováno jako jímka |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Dlouhou | ✓ | ✓ |
| AttributeTypeCode. Boolean | Logická hodnota | ✓ | ✓ |
| Zaatributu. Customer | Identifikátor GUID | ✓ | ✓ (Viz [doprovodné](#writing-data-to-a-lookup-field)materiály) |
| Typ atributu. DateTime | Datum a čas | ✓ | ✓ |
| Typ atributu. Decimal | Decimal | ✓ | ✓ |
| Typ atributu. Double | dvojité | ✓ | ✓ |
| Atribut atributu. EntityName | Řetězec | ✓ | ✓ |
| Typ atributu. Integer | Int32 | ✓ | ✓ |
| Vymezit. Lookup | Identifikátor GUID | ✓ | ✓ (Viz [doprovodné](#writing-data-to-a-lookup-field)materiály) |
| NeManagedProperty. | Logická hodnota | ✓ | |
| Vydaný zápis | Řetězec | ✓ | ✓ |
| . Peníze | Decimal | ✓ | ✓ |
| Atribut atributu. Owner | Identifikátor GUID | ✓ | ✓ (Viz [doprovodné](#writing-data-to-a-lookup-field)materiály) |
| Atribut atributu. picklist | Int32 | ✓ | ✓ |
| Typ atributu. uniqueidentifier | Identifikátor GUID | ✓ | ✓ |
| Řetězec atributu. String | Řetězec | ✓ | ✓ |
| Atribut atributu. State | Int32 | ✓ | ✓ |
| Stav atributu. | Int32 | ✓ | ✓ |

> [!NOTE]
> Datové typy dynamiky **. CalendarRules**, atributu '. **MultiSelectPicklist**a typu **atributu '. partylist** nejsou podporovány.

## <a name="writing-data-to-a-lookup-field"></a>Zápis dat do vyhledávacího pole

Chcete-li zapsat data do vyhledávacího pole s více cíli, jako je zákazník a vlastník, postupujte podle těchto pokynů a příklad:

1. Nastavte zdroj tak, aby obsahoval hodnotu pole i odpovídající název cílové entity.
   - Pokud jsou všechny záznamy namapovány na stejnou cílovou entitu, zajistěte jednu z následujících podmínek:
      - Zdrojová data obsahují sloupec, ve kterém je uložený název cílové entity.
      - Do zdroje aktivity kopírování jste přidali další sloupec, který definuje cílovou entitu.
   - Pokud jsou různé záznamy mapovány na různé cílové entity, ujistěte se, že zdrojová data obsahují sloupec, který ukládá odpovídající název cílové entity.

1. Namapujte hodnoty i sloupce odkazů na entity ze zdroje na jímku. Sloupec s odkazem na entitu musí být namapován na virtuální sloupec se speciálním vzorem pojmenování `{lookup_field_name}@EntityReference` . Sloupec ve skutečnosti v aplikaci Dynamics neexistuje. Slouží k označení tohoto sloupce jako sloupce metadat daného vyhledávacího pole s více cíli.

Předpokládejme například, že zdroj má tyto dva sloupce:

- **CustomerField** sloupec typu **GUID**, který je hodnotou primárního klíče cílové entity v aplikaci Dynamics.
- **Cílový** sloupec typu **String**, což je logický název entity cíle.

Předpokládejme také, že chcete zkopírovat taková data do pole entity Dynamics jímky **CustomerField** typu **Customer**.

V mapování sloupců kopírování a aktivita namapujte tyto dva sloupce následujícím způsobem:

- **CustomerField** na **CustomerField**. Toto mapování je normální mapování polí.
- **Cíl** pro **CustomerField \@ EntityReference** Sloupec jímky je virtuální sloupec reprezentující odkaz na entitu. Zadejte takové názvy polí v mapování, protože se nebudou zobrazovat importem schémat.

![Vyhledávání v Dynamics – mapování sloupce pole](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Pokud všechny zdrojové záznamy namapovaly na stejnou cílovou entitu a zdrojová data neobsahují cílovou entitu, je tady klávesová zkratka: ve zdroji aktivity kopírování přidejte další sloupec. Pojmenujte nový sloupec pomocí vzoru `{lookup_field_name}@EntityReference` , nastavte hodnotu na název cílové entity a pak pokračujte podle mapování sloupce jako obvykle. Pokud jsou vaše zdroje a názvy sloupců jímky stejné, můžete také přeskočit explicitní mapování sloupce, protože aktivita kopírování ve výchozím nastavení mapuje sloupce podle názvu.

![Vyhledávání v Dynamics – pole pro přidání sloupce odkaz na entitu](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Podrobné informace o vlastnostech najdete v tématu [aktivita vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam dat ukládá aktivitu kopírování v Data Factory podporuje jako zdroje a jímky, viz [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
