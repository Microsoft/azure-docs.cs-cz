---
title: Kopírování dat v dynamics (Common Data Service)
description: Zjistěte, jak kopírovat data z aplikace Microsoft Dynamics CRM nebo Microsoft Dynamics 365 (Common Data Service) do podporovaných úložišť dat jímky nebo z podporovaných zdrojových úložišť dat do aplikace Dynamics CRM nebo Dynamics 365 pomocí aktivity kopírování v kanálu datové továrny.
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
ms.date: 11/20/2019
ms.openlocfilehash: 9bbd2e3376f1da3fdf5b10d654a331ce258be5cf
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422092"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 (Common Data Service) nebo Dynamics CRM pomocí Azure Data Factory

Tento článek popisuje, jak pomocí kopírování aktivity v Azure Data Factory kopírovat data z a do Microsoft Dynamics 365 nebo Microsoft Dynamics CRM. Vychází z článku [přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor je podporován pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z Dynamics 365 (Common Data Service) nebo Dynamics CRM můžete zkopírovat do libovolného podporovaného úložiště dat jímky. Můžete také kopírovat data z libovolného podporovaného zdrojového úložiště dat do aplikace Dynamics 365 (Common Data Service) nebo Dynamics CRM. Seznam úložišť dat podporovaných jako zdroje nebo propady aktivitou kopírování naleznete v tabulce [Podporovaná data.](copy-activity-overview.md#supported-data-stores-and-formats)

Tento konektor Dynamics podporuje dynamics verze 7.x až 9.x pro online i místní. Přesněji řečeno,

- Mapa verze 7.x do aplikace Dynamics CRM 2015
- Verze 8.x mapuje dynamics CRM 2016 a dřívější verzi Dynamics 365
- Verze 9.x mapuje na novější verzi Dynamics 365

V následující tabulce najdete podporované typy a konfigurace ověřování pro příslušné verze/produkty dynamics. (IFD je zkratka pro nasazení pro připojení k internetu.)

| Verze dynamics | Typy ověřování | Vzorky propojených služeb |
|:--- |:--- |:--- |
| Common Data Service <br> Dynamics 365 online <br> Dynamics CRM Online | Instanční objekt Služby AAD <br> Office365 | [Dynamics online + AAD service instanční objekt nebo office365 auth](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 v místním prostředí s IFD <br> Dynamics CRM 2016 v místním prostředí s IFD <br> Dynamics CRM 2015 v místním prostředí s IFD | IFD | [Dynamika v místním prostředí s IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Pro Dynamics 365 konkrétně jsou podporovány následující typy aplikací:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Jiné typy aplikací, například Finance a provoz, Talent atd., nejsou tímto konektorem podporovány.

Tento konektor Dynamics je postaven na nástrojech [Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Chcete-li kopírovat data z **aplikace Dynamics 365 Finance and Operations**, můžete použít konektor Dynamics [AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Dynamics jsou podporovány následující vlastnosti.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 a Dynamics CRM Online

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **Dynamics**, **DynamicsCrm**nebo **CommonDataServiceForApps**. | Ano |
| deploymentType | Typ nasazení instance Dynamics. Musí být **"Online"** pro Dynamics online. | Ano |
| serviceUri | Adresa URL služby instance Dynamics, `https://adfdynamics.crm.dynamics.com`např. | Ano |
| authenticationType | Typ ověřování pro připojení k serveru Dynamics. Povolené hodnoty jsou: **AADServicePrincipal** nebo **"Office365"**. | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace Azure Active Directory. | Ano při `AADServicePrincipal` použití ověřování |
| servicePrincipalCredentialType | Zadejte typ pověření, který se má použít pro ověřování instančního objektu. Povolené hodnoty jsou: **ServicePrincipalKey** nebo **ServicePrincipalCert**. | Ano při `AADServicePrincipal` použití ověřování |
| servicePrincipalCredential | Zadejte pověření instančního objektu. <br>Při `ServicePrincipalKey` použití jako `servicePrincipalCredential` typ pověření, může být řetězec (ADF bude šifrovat při nasazení propojené služby) nebo odkaz na tajný klíč v AKV. <br>Při `ServicePrincipalCert` použití jako `servicePrincipalCredential` pověření, by měl být odkaz na certifikát v AKV. | Ano při `AADServicePrincipal` použití ověřování | 
| uživatelské jméno | Zadejte uživatelské jméno, které se má připojit k aplikaci Dynamics. | Ano při `Office365` použití ověřování |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano při `Office365` použití ověřování |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku, pokud zdrojová propojená služba nemá integrační runtime |

>[!NOTE]
>Konektor Dynamics používá k identifikaci instance Dynamics CRM/365 Online volitelnou vlastnost "organizationName". Zatímco bude fungovat, doporučujeme zadat novou vlastnost "serviceUri" místo toho, abyste získali lepší výkon například zjišťování.

**Příklad: Dynamics online pomocí instančního objektu Služby AAD + ověřování pomocí klíče**

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
**Příklad: Dynamics online pomocí instančního objektu AAD + ověřování certifikátů**

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

**Příklad: Dynamics online pomocí ověřování Office365**

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

*Další vlastnosti, které jsou v porovnání s dynamics online jsou "hostName" a "port".*

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **Dynamics**, **DynamicsCrm**nebo **CommonDataServiceForApps**. | Ano |
| deploymentType | Typ nasazení instance Dynamics. Musí být **"OnPremisesWithIfd"** pro Dynamics místně s IFD.| Ano |
| Hostname | Název hostitele místního serveru Dynamics. | Ano |
| port | Port místního serveru Dynamics. | Ne, výchozí hodnota je 443 |
| Názevorganizace | Název organizace instance Dynamics. | Ano |
| authenticationType | Typ ověřování pro připojení k serveru Dynamics. Zadejte **"Ifd"** pro dynamics místně pomocí IFD. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno, které se má připojit k aplikaci Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole můžete označit jako securestring pro bezpečné uložení v adf nebo ukládat heslo v trezoru klíčů Azure a nechat kopírovat aktivitu vytáhnout odtud při provádění kopírování dat - další informace z [přihlašovacích údajů úložiště v trezoru klíčů](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne pro zdroj, Ano pro umyvadlo |

**Příklad: Dynamika místně s IFD pomocí ověřování IFD**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Dynamics.

Chcete-li kopírovat data z aplikace Dynamics a do aplikace Dynamics, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **DynamicsEntity**, **DynamicsCrmEntity**nebo **CommonDataServiceForAppsEntity**. |Ano |
| název_entity | Logický název entity, kterou chcete načíst. | Ne pro zdroj (pokud je zadán "dotaz" ve zdroji aktivity), Ano pro jímku |

**Příklad:**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných typy zdrojů dynamics a jímky.

### <a name="dynamics-as-a-source-type"></a>Dynamika jako typ zdroje

Chcete-li kopírovat data z dynamics, jsou v části **zdroje aktivity** kopírování podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **DynamicsSource**, **DynamicsCrmSource**nebo **CommonDataServiceForAppsSource**. | Ano |
| query | FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics (online a místně). Prohlédněte si následující příklad. Další informace najdete v [tématu Vytváření dotazů pomocí fetchxml](https://msdn.microsoft.com/library/gg328332.aspx). | Ne (pokud je v datové sadě zadáno "entityName") |

>[!NOTE]
>Sloupec PK bude vždy zkopírován i v případě, že projekce sloupce, kterou nakonfigurujete v dotazu FetchXML, ji neobsahuje.

> [!IMPORTANT]
>- Při kopírování dat z dynamics je explicitní mapování sloupců z dynamics do jímky volitelné, ale vysoce doporučuje, aby byl zajištěn výsledek deterministické kopie.
>- Při importu schématu ve vytváření ui, ADF odvodí schéma vzorkováním horní řádky z výsledku dotazu Dynamics inicializovat zdrojový sloupec seznamu, v takovém případě sloupce bez hodnot v hornířádky budou vynechány. Stejné chování platí pro spuštění kopírování, pokud neexistuje žádné explicitní mapování. Můžete zkontrolovat a přidat další sloupce do mapování, které budou respektovány během kopírování runtime.

**Příklad:**

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

Chcete-li kopírovat data do dynamics, jsou podporovány následující vlastnosti v části **jímky** aktivity kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **DynamicsSink**, **DynamicsCrmSink**nebo **CommonDataServiceForAppsSink**. | Ano |
| writeBehavior | Chování zápisu operace.<br/>Povolená hodnota je **"Upsert"**. | Ano |
| alternateKeyName | Zadejte název alternativního klíče definovaný na entitě k provedení "Upsert". | Ne |
| writeBatchSize | Počet řádků dat zapsaných do dynamics v každé dávce. | Ne (výchozí hodnota je 10) |
| ignoreNullValues | Označuje, zda se mají během operace zápisu ignorovat hodnoty null ze vstupních dat (kromě klíčových polí).<br/>Povolené hodnoty jsou **pravdivé** a **nepravdivé**.<br>- **Pravda**: Ponechte data v cílovém objektu beze změny, když proveďte operaci upsert/update. Při operaci vložení vložte definovanou výchozí hodnotu.<br/>- **False**: Aktualizace dat v cílovém objektu na hodnotu NULL při operaci upsert/update. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je nepravdivá) |

>[!NOTE]
>Výchozí hodnota jímky "**writeBatchSize**" a aktivity kopírování "**[parallelCopies](copy-activity-performance-features.md#parallel-copy)**" pro jímku Dynamics jsou obě 10. Proto 100 záznamů jsou odeslány dynamics současně.

Pro dynamics 365 online je limit [2 souběžných dávkových volání na organizaci](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Pokud je tento limit překročen, je vyvolána chyba "Server Busy" před provedením prvního požadavku. Udržování "writeBatchSize" menší nebo rovno 10 by se zabránilo takové omezení souběžných volání.

Optimální kombinace **"writeBatchSize**" a "**parallelCopies**" závisí na schématu vaší entity, například na počtu sloupců, velikosti řádku, počtu pluginů/pracovních postupů/aktivit pracovních postupů připojených k těmto voláním atd. Výchozí nastavení 10 writeBatchSize * 10 parallelCopies je doporučení podle služby Dynamics, která by fungovala pro většinu entit Dynamics, i když nemusí být nejlepší výkon. Výkon můžete vyladit úpravou kombinace v nastavení aktivity kopírování.

**Příklad:**

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

## <a name="data-type-mapping-for-dynamics"></a>Mapování datového typu pro Dynamics

Při kopírování dat z dynamics se používají následující mapování z datových typů Dynamics na dočasné datové typy Datové továrny. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající datový typ datové továrny ve struktuře datové sady na základě zdrojového datového typu Dynamics pomocí následující tabulky mapování.

| Datový typ dynamics | Dočasný datový typ datové továrny | Podporováno jako zdroj | Podporováno jako umyvadlo |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Dlouhé | ✓ | ✓ |
| AttributeTypeCode.Logická hodnota | Logická hodnota | ✓ | ✓ |
| AttributeType.Zákazník | Identifikátor GUID | ✓ | |
| AttributeType.DateTime | Datum a čas | ✓ | ✓ |
| AttributeType.Decimal | Desetinné číslo | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Řetězec | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Identifikátor GUID | ✓ | ✓ (s jediným cílem) |
| AttributeType.ManagedProperty | Logická hodnota | ✓ | |
| AttributeType.Memo | Řetězec | ✓ | ✓ |
| AttributeType.Money | Desetinné číslo | ✓ | ✓ |
| AttributeType.Owner | Identifikátor GUID | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Identifikátor GUID | ✓ | ✓ |
| AttributeType.String | Řetězec | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Datové typy Dynamics AttributeType.CalendarRules, AttributeType.MultiSelectPicklist a AttributeType.PartyList nejsou podporovány.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
