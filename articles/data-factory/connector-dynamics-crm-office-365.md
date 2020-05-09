---
title: Kopírovat data v aplikaci Dynamics (Common Data Service)
description: Naučte se, jak kopírovat data z Microsoft Dynamics CRM nebo Microsoft Dynamics 365 (Common Data Service) do podporovaných úložišť dat jímky, nebo z podporovaných zdrojových úložišť dat do Dynamics CRM nebo Dynamics 365 pomocí aktivity kopírování v kanálu služby Data Factory.
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
ms.date: 05/06/2020
ms.openlocfilehash: 255c39eac2285a23403da2db893d9de8835f7d2c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891528"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 (Common Data Service) nebo Dynamics CRM pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Microsoft Dynamics 365 nebo Microsoft Dynamics CRM. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data z Dynamics 365 (Common Data Service) nebo Dynamics CRM můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do Dynamics 365 (Common Data Service) nebo Dynamics CRM. Seznam úložišť dat podporovaných jako zdroje nebo jímky aktivitou kopírování najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Tento Dynamics Connector podporuje Dynamics verze 7. x až 9. x pro online i místní prostředí. Konkrétně

- Verze 7. x se mapuje na Dynamics CRM 2015
- Verze 8. x se mapuje na Dynamics CRM 2016 a dřívější verzi Dynamics 365.
- Verze 9. x se mapuje na novější verzi Dynamics 365

V následující tabulce najdete informace o podporovaných typech a konfiguracích ověřování pro příslušné verze a produkty Dynamics. (IFD je pro internetové nasazení krátké.)

| Verze Dynamics | Typy ověřování | Ukázky propojených služeb |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | Objekt služby AAD <br> Office 365 | [Dynamics Online + služba AAD nebo ověřování Office 365](#dynamics-365-and-dynamics-crm-online) |
| Místní prostředí Dynamics 365 s IFD <br> Dynamics CRM 2016 v místním prostředí s IFD <br> Dynamics CRM 2015 v místním prostředí s IFD | IFD | [Místní řešení s IFD a ověřováním pomocí internetového nasazení](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Pro Dynamics 365 jsou podporovány následující typy aplikací:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Tento konektor nepodporuje jiné typy aplikací, např. finance a operace, talentů atd.

Tento konektor Dynamics je postaven nad [nástroji Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Pokud chcete kopírovat data z **dynamics 365 finance a operací**, můžete použít [konektor Dynamics AX](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít tento konektor s ověřováním instančního objektu AAD, musíte v Common Data Service nebo Dynamics nastavit ověřování S2S (Server-to-Server). Podrobný postup najdete v [tomto článku](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) .

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro aplikaci Dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 a Dynamics CRM Online

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na hodnotu **Dynamics**, **DynamicsCrm**nebo **CommonDataServiceForApps**. | Ano |
| Typ nasazení | Typ nasazení instance Dynamics Musí být **online** pro Dynamics Online. | Ano |
| serviceUri | Adresa URL služby vaší instance Dynamics, např `https://adfdynamics.crm.dynamics.com`. | Ano |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Povolené hodnoty jsou: **AADServicePrincipal** nebo **"Office 365"**. | Ano |
| servicePrincipalId | Zadejte ID klienta Azure Active Directory aplikace. | Ano Při použití `AADServicePrincipal` ověřování |
| servicePrincipalCredentialType | Zadejte typ přihlašovacích údajů, který se použije pro ověřování instančního objektu. Povolené hodnoty jsou: **ServicePrincipalKey** nebo **ServicePrincipalCert**. | Ano Při použití `AADServicePrincipal` ověřování |
| servicePrincipalCredential | Zadejte přihlašovací údaje instančního objektu. <br>Když použijete `ServicePrincipalKey` jako typ přihlašovacích údajů, `servicePrincipalCredential` může to být řetězec (ADF ho bude šifrovat při nasazení propojené služby) nebo odkaz na tajný kód v integrace. <br>Pokud používáte `ServicePrincipalCert` jako přihlašovací údaje `servicePrincipalCredential` , měl by se jednat o odkaz na certifikát v integrace. | Ano Při použití `AADServicePrincipal` ověřování | 
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano Při použití `Office365` ověřování |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano Při použití `Office365` ověřování |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku, pokud zdrojová propojená služba nemá modul runtime integrace |

>[!NOTE]
>Konektor Dynamics, pomocí kterého se k identifikaci online instance Dynamics CRM/365 používá volitelná vlastnost NázevOrganizace I když stále funguje, navrhnete místo toho zadání nové vlastnosti "serviceUri", abyste získali lepší výkon pro zjišťování instancí.

**Příklad: Dynamics Online s použitím instančního objektu AAD + ověřování pomocí klíče**

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://adfdynamics.crm.dynamics.com",  
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
**Příklad: Dynamics Online s použitím instančního objektu AAD + ověřování certifikátem**

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://adfdynamics.crm.dynamics.com", 
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

**Příklad: Dynamics Online pomocí ověřování Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
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

*Další vlastnosti, které jsou porovnány s Dynamics Online, jsou "hostName" a "port".*

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavena na hodnotu **Dynamics**, **DynamicsCrm**nebo **CommonDataServiceForApps**. | Ano |
| Typ nasazení | Typ nasazení instance Dynamics Musí se jednat o **"OnPremisesWithIfd"** pro místní prostředí Dynamics pomocí internetového nasazení.| Ano |
| Název hostitele | Název hostitele místního Dynamics serveru. | Ano |
| port | Port místního Dynamics serveru. | Ne, výchozí hodnota je 443 |
| Organizace | Název organizace instance Dynamics | Ano |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Zadejte **"IFD"** pro místní prostředí Dynamics pomocí internetového nasazení. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat aktivitu kopírování, která se při provádění kopírování dat vyžádá – další informace najdete v části [přihlašovací údaje úložiště v Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud není zadaný, použije se výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku |

**Příklad: místní prostředí Dynamics s IFD pomocí ověřování IFD**

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

Chcete-li kopírovat data z a do Dynamics, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **DynamicsEntity**, **DynamicsCrmEntity**nebo **CommonDataServiceForAppsEntity**. |Ano |
| entityName | Logický název entity, která se má načíst | Ne pro zdroj (Pokud je zadána možnost dotaz ve zdroji aktivity), Ano pro jímku |

**Případě**

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

Chcete-li kopírovat data z Dynamics, v části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DynamicsSource**, **DynamicsCrmSource**nebo **CommonDataServiceForAppsSource**. | Ano |
| query | FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics (online a v místním prostředí). Prohlédněte si následující příklad. Další informace najdete v tématu [sestavování dotazů pomocí FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Ne (Pokud je zadána "EntityName" v sadě dat.) |

>[!NOTE]
>Sloupec PK bude vždy zkopírován i v případě, že projekce sloupce, kterou nakonfigurujete v dotazu FetchXML, ji neobsahuje.

> [!IMPORTANT]
>- Při kopírování dat z Dynamics je explicitní mapování sloupce z Dynamics na jímku volitelné, ale s vysokou dostupností, aby se zajistila deterministické výsledek kopírování.
>- Při importu schématu v uživatelském rozhraní pro vytváření obsahu ADF odvodí schéma pomocí vzorkování horních řádků z výsledku dotazu Dynamics k inicializaci seznamu zdrojového sloupce. v takovém případě budou vynechány sloupce bez hodnot v horních řádcích. Stejné chování platí pro spuštění kopírování, pokud neexistuje explicitní mapování. Můžete zkontrolovat a přidat do mapování více sloupců, které budou během kopírování za běhu zaměněny.

**Případě**

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

Chcete-li kopírovat data do Dynamics, v části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **DynamicsSink**, **DynamicsCrmSink**nebo **CommonDataServiceForAppsSink**. | Ano |
| writeBehavior | Chování operace zápisu.<br/>Povolená hodnota je **"Upsert"**. | Ano |
| alternateKeyName | Zadejte název alternativního klíče definovaný pro vaši entitu a proveďte "Upsert". | No |
| writeBatchSize | Počet řádků dat zapsaných do Dynamics v každé dávce. | Ne (výchozí hodnota je 10) |
| ignoreNullValues | Určuje, zda se při operaci zápisu mají ignorovat hodnoty null ze vstupních dat (s výjimkou polí klíčů).<br/>Povolené hodnoty jsou **true** a **false**.<br>- **True**: když provedete operaci Upsert/Update, ponechte data v cílovém objektu beze změny. Při operaci INSERT vložte definovanou výchozí hodnotu.<br/>- **False**: při operaci Upsert/Update aktualizujte data v cílovém objektu na hodnotu null. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je false) |

>[!NOTE]
>Výchozí hodnota jímky "**writeBatchSize**" a aktivita kopírování "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" pro jímky v aplikaci Dynamics je 10. Proto jsou záznamy 100 odesílány současně do aplikace Dynamics.

Pro Dynamics 365 online existuje limit [2 souběžných volání dávky na jednu organizaci](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Pokud dojde k překročení tohoto limitu, vyvolá se chyba "Server je zaneprázdněn" před tím, než se první požadavek spustí. Udržováním "writeBatchSize" menší nebo rovno 10 se vyhnete omezení souběžných volání.

Optimální kombinace "**writeBatchSize**" a "**parallelCopies**" závisí na schématu vaší entity, např. počtu sloupců, velikosti řádku, počtu modulů plug-in, pracovních postupů/aktivit pracovních postupů, které jsou připojeny k těmto voláním atd. Výchozím nastavením 10 writeBatchSize * 10 parallelCopies je doporučení podle služby Dynamics, které by fungovalo pro většinu entit Dynamics, i když ale nemusí být nejlepší výkon. Výkon můžete vyladit úpravou kombinace v nastavení aktivity kopírování.

**Případě**

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

Při kopírování dat z aplikace Dynamics jsou následující mapování použita z datových typů Dynamics k Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající Data Factory datový typ ve struktuře datové sady na základě vašeho zdrojového datového typu Dynamics pomocí následující tabulky mapování.

| Datový typ Dynamics | Data Factory pomocný datový typ | Podporováno jako zdroj | Podporováno jako jímka |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Dlouhou | ✓ | ✓ |
| AttributeTypeCode. Boolean | Logická hodnota | ✓ | ✓ |
| Zaatributu. Customer | Identifikátor GUID | ✓ | |
| Typ atributu. DateTime | Datum a čas | ✓ | ✓ |
| Typ atributu. Decimal | Desetinné číslo | ✓ | ✓ |
| Typ atributu. Double | Double | ✓ | ✓ |
| Atribut atributu. EntityName | Řetězec | ✓ | ✓ |
| Typ atributu. Integer | Int32 | ✓ | ✓ |
| Vymezit. Lookup | Identifikátor GUID | ✓ | ✓ (s přidruženým jedním cílem) |
| NeManagedProperty. | Logická hodnota | ✓ | |
| Vydaný zápis | Řetězec | ✓ | ✓ |
| . Peníze | Desetinné číslo | ✓ | ✓ |
| Atribut atributu. Owner | Identifikátor GUID | ✓ | |
| Atribut atributu. picklist | Int32 | ✓ | ✓ |
| Typ atributu. uniqueidentifier | Identifikátor GUID | ✓ | ✓ |
| Řetězec atributu. String | Řetězec | ✓ | ✓ |
| Atribut atributu. State | Int32 | ✓ | ✓ |
| Stav atributu. | Int32 | ✓ | ✓ |

> [!NOTE]
> Datové typy Dynamics mají typ atributu. CalendarRules, atributu atributu. MultiSelectPicklist a typ atributu. PartyList nejsou podporovány.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
