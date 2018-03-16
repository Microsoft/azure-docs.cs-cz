---
title: "Kopírování dat z a do Dynamics CRM nebo Dynamics 365 pomocí Azure Data Factory | Microsoft Docs"
description: "Zjistěte, jak ke zkopírování dat z aplikace Microsoft Dynamics CRM nebo Microsoft Dynamics 365 na podporované jímky úložiště dat nebo z podporovanými úložišti dat zdroje k Dynamics CRM nebo Dynamics 365 pomocí aktivity kopírování v kanálu data factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: dc0b01e23ebb2695fd0365f054b3cacd2573f3c6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="copy-data-from-and-to-dynamics-365-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 nebo Dynamics CRM pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat z a do společnosti Microsoft Dynamics 365 nebo Microsoft Dynamics CRM. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 objektu pro vytváření dat, která je obecně k dispozici, najdete v části [aktivitu kopírování v verze 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Z Dynamics 365 nebo Dynamics CRM můžete zkopírovat data do úložiště dat žádné podporované jímky. Také můžete zkopírovat data z úložiště dat žádné podporované zdrojové Dynamics 365 nebo Dynamics CRM. Seznam úložišť dat jako zdroje nebo jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Tento konektor Dynamics podporuje následující verze Dynamics a typy ověřování. (IFD je zkratka pro nasazení aplikace.)

| Dynamics verze | Typy ověření | Ukázky propojené služby |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + ověřování Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 místně s IFD <br> Dynamics CRM 2016 místně s IFD <br> Dynamics CRM 2015 místně s IFD | IFD | [Dynamics místně s IFD + IFD ověřování](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Dynamics 365 konkrétně následující typy aplikací jsou podporované:

- Dynamics 365 pro prodej
- Dynamics 365 zákaznický servis
- Dynamics 365 služby pole
- Dynamics 365 for Project Service Automation
- Dynamics 365 pro Marketing

Jiná aplikace typy, např. operace a Finance, talentu, nejsou podporována atd.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Následující části obsahují podrobnosti o vlastnosti, které se používají k definování entit služby Data Factory, které jsou specifické pro Dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro Dynamics propojené služby.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 and Dynamics CRM Online

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení Dynamics instance. Musí být **"Online"** pro Dynamics online. | Ano |
| Název organizace | Název organizace Dynamics instance. | Ne, by měl určovat po více než jedna instance Dynamics přidružené k uživateli |
| authenticationType. | Typ ověřování pro připojení k serveru Dynamics. Zadejte **"Office 365"** pro Dynamics online. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k aplikaci Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Žádné zdroje, Ano pro sink Pokud zdroj propojené služby nemá modulu runtime integrace |

>[!IMPORTANT]
>Při kopírování dat do Dynamics výchozí Runtime integrace Azure nelze použít ke spuštění kopírování. Jinými slovy, pokud vaše zdrojová propojené služby nemá zadaný integrace běhu, explicitně [vytvoření modulu Runtime integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním téměř Dynamics instanci. Přidružte v propojené službě Dynamics jako v následujícím příkladu.

**Příklad: Dynamics online pomocí ověřování Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 a Dynamics CRM místně s IFD

*Další vlastnosti, které porovnávají Dynamics online jsou "název hostitele" a "port".*

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení Dynamics instance. Musí být **"OnPremisesWithIfd"** pro Dynamics místně s IFD.| Ano |
| hostName | Název hostitele serveru Dynamics místně. | Ano |
| port | Port serveru Dynamics místně. | Ne, výchozí hodnota je 443 |
| Název organizace | Název organizace Dynamics instance. | Ano |
| authenticationType. | Typ ověřování pro připojení k serveru Dynamics. Zadejte **"Ifd"** pro Dynamics místně s IFD. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k aplikaci Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete zvolit označit toto pole jako SecureString bezpečně uložit v ADF nebo uložení hesla v Azure Key Vault a nechat aktivitě kopírování načítat z ní při kopírování dat – Další informace z [ukládat přihlašovací údaje v Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Pokud není zadaný, použije výchozí Runtime integrace Azure. | Ne Ano pro sink zdroje |

>[!IMPORTANT]
>Můžete kopírovat data do Dynamics, explicitně [vytvoření modulu Runtime integrace Azure](create-azure-integration-runtime.md#create-azure-ir) s umístěním téměř Dynamics instanci. Přidružte v propojené službě jako v následujícím příkladu.

**Příklad: Dynamics místně s IFD pomocí IFD ověřování**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností nepodporuje Dynamics datovou sadu.

Ke zkopírování dat z a do Dynamics, nastavte vlastnost typu datové sady, která **DynamicsEntity**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na **DynamicsEntity**. |Ano |
| entityName | Logický název entity načíst. | Ne pro zdroj (Pokud je zadán "dotaz" ve zdroji aktivity), Ano pro sink |

> [!IMPORTANT]
>- Při kopírování dat z Dynamics v datové sadě Dynamics je vyžadován v části "struktura". Definuje název a data pro typ sloupce Dynamics data, která chcete zkopírovat přes. Další informace najdete v tématu [strukturu datové sady](concepts-datasets-linked-services.md#dataset-structure) a [mapování datového typu pro Dynamics](#data-type-mapping-for-dynamics).
>- Při kopírování dat do Dynamics, v části "struktura" je volitelné v datové sadě Dynamics. Sloupce, které chcete zkopírovat do je určen podle schématu datového zdroje. Pokud je zdrojem soubor CSV bez hlavičky, ve vstupní datové sady, zadejte "struktura" s název a datový typ sloupce. Mapují na pole v souboru CSV po jednom v pořadí.

**Příklad:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností nepodporuje Dynamics zdroj a jímka typy.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ zdroje

Ke zkopírování dat z Dynamics, nastavte typ zdroje v aktivitě kopírování do **DynamicsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **DynamicsSource**. | Ano |
| query | FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics (online a místně). Prohlédněte si následující příklad. Další informace najdete v tématu [vytvořit dotazy s FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Ne (když je určena "entityName" v datové sadě) |

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics jako typ jímky

Ke zkopírování dat do Dynamics, nastavte typ jímky v aktivitě kopírování do **DynamicsSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musíte nastavit **DynamicsSink**. | Ano |
| WriteBehavior | Chování zápisu operace.<br/>Povolená hodnota je **"Upsert"**. | Ano |
| writeBatchSize | Počet řádků dat zapsaných na Dynamics v každé dávce. | Ne (výchozí hodnota je 10) |
| ignoreNullValues | Označuje, zda Ignorovat hodnoty null ze vstupních dat (s výjimkou polí s klíči) během operace zápisu.<br/>Povolené hodnoty jsou **true** a **false**.<br>- **Hodnota TRUE,**: ponechejte data v cílovém objektu beze změny, když provedete upsert nebo aktualizovat operace. Při operaci vložení, vložte definované výchozí hodnotu.<br/>- **False**: aktualizovat data v cílovém objektu na hodnotu NULL, když provedete upsert nebo aktualizovat operace. Při operaci vložení, vložte hodnotu NULL. | Ne (výchozí hodnota je false) |

>[!NOTE]
>Výchozí hodnota writeBatchSize jímka a aktivitě kopírování [parallelCopies](copy-activity-performance.md#parallel-copy) pro Dynamics sink jsou obě 10. Proto 100 záznamů se odešlou do Dynamics současně.

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

## <a name="data-type-mapping-for-dynamics"></a>Datový typ mapování pro Dynamics

Při kopírování dat z Dynamics se používají následující mapování Dynamics datových typů k objektu pro vytváření dat dočasné datové typy. Informace, jak mapuje typ zdroje schéma a data na jímky aktivitě kopírování najdete v tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající datový typ objektu pro vytváření dat ve struktuře datové sady, na základě vaší zdroje Dynamics datový typ pomocí následující tabulky mapování.

| Dynamics datový typ | Typ průběžných dat objektu pro vytváření dat | Podporované jako zdroj | Podporované jako jímku |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Dlouhé | ✓ | ✓ |
| AttributeTypeCode.Boolean | Logická hodnota | ✓ | ✓ |
| AttributeType.DateTime | Datum a čas | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Dvojitý | ✓ | ✓ |
| AttributeType.EntityName | Řetězec | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | |
| AttributeType.ManagedProperty | Logická hodnota | ✓ | |
| AttributeType.Memo | Řetězec | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Řetězec | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Datové typy Dynamics AttributeType.CalendarRules a AttributeType.PartyList nejsou podporovány.

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
