---
title: Kopírování dat z a do Dynamics CRM nebo Dynamics 365 (Common Data Service) pomocí Azure Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data z Microsoft Dynamics CRM nebo Microsoft Dynamics 365 (Common Data Service) do podporovaných úložišť dat jímky, nebo z podporovaných zdrojových úložišť dat do Dynamics CRM nebo Dynamics 365 pomocí aktivity kopírování v kanálu služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 18fdb14430eee97ff2780d963abf3e5ceafe1126
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009400"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 (Common Data Service) nebo Dynamics CRM pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Microsoft Dynamics 365 nebo Microsoft Dynamics CRM. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data z Dynamics 365 (Common Data Service) nebo Dynamics CRM můžete kopírovat do libovolného podporovaného úložiště dat jímky. Data můžete také kopírovat z libovolného podporovaného zdrojového úložiště dat do Dynamics 365 (Common Data Service) nebo Dynamics CRM. Seznam úložišť dat podporovaných jako zdroje nebo jímky aktivitou kopírování najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

Tento Dynamics Connector podporuje Dynamics verze 7. x až 9. x pro online i místní prostředí. Konkrétně

- Verze 7. x se mapuje na Dynamics CRM 2015
- Verze 8. x se mapuje na Dynamics CRM 2016 a dřívější verzi Dynamics 365.
- Verze 9. x se mapuje na novější verzi Dynamics 365

V následující tabulce najdete informace o podporovaných typech a konfiguracích ověřování pro příslušné verze a produkty Dynamics. (IFD je pro internetové nasazení krátké.)

| Verze Dynamics | Typy ověřování | Ukázky propojených služeb |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics Online + ověřování Office 365](#dynamics-365-and-dynamics-crm-online) |
| Místní prostředí Dynamics 365 s IFD <br> Dynamics CRM 2016 v místním prostředí s IFD <br> Dynamics CRM 2015 v místním prostředí s IFD | SLUŽBU | [Místní řešení s IFD a ověřováním pomocí internetového nasazení](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Pro Dynamics 365 jsou podporovány následující typy aplikací:

- Dynamics 365 pro prodej
- Dynamics 365 pro zákaznické služby
- Dynamics 365 for Field Service
- Dynamics 365 pro automatizaci služby Project Service
- Dynamics 365 pro marketing

Tento konektor nepodporuje jiné typy aplikací, např. finance a operace, talentů atd.

Tento konektor Dynamics je postaven nad [nástroji Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Pokud chcete kopírovat data z **dynamics 365 finance a operací**, můžete použít [konektor Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro aplikaci Dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 a Dynamics CRM Online

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení instance Dynamics Musí být **online** pro Dynamics Online. | Ano |
| serviceUri | Adresa URL služby vaší instance Dynamics, např `https://adfdynamics.crm.dynamics.com`. | Ano |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Zadejte **"Office 365"** pro Dynamics Online. | Ano |
| username | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku, pokud zdrojová propojená služba nemá modul runtime integrace |

>[!NOTE]
>Konektor Dynamics, pomocí kterého se k identifikaci online instance Dynamics CRM/365 používá volitelná vlastnost NázevOrganizace I když stále funguje, navrhnete místo toho zadání nové vlastnosti "serviceUri", abyste získali lepší výkon pro zjišťování instancí.

**Příklad: Dynamics Online s použitím ověřování Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
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
| type | Vlastnost Type musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení instance Dynamics Musí se jednat o **"OnPremisesWithIfd"** pro místní prostředí Dynamics pomocí internetového nasazení.| Ano |
| hostName | Název hostitele místního Dynamics serveru. | Ano |
| port | Port místního Dynamics serveru. | Ne, výchozí hodnota je 443 |
| organizationName | Název organizace instance Dynamics | Ano |
| authenticationType | Typ ověřování pro připojení k Dynamics serveru. Zadejte **"IFD"** pro místní prostředí Dynamics pomocí internetového nasazení. | Ano |
| username | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano |
| password | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Toto pole můžete označit jako SecureString, abyste ho bezpečně ukládali do podavače ADF, nebo Uložit heslo v Azure Key Vault a nechat aktivitu kopírování, která se při provádění kopírování dat vyžádá – další informace najdete v části [přihlašovací údaje úložiště v Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku |

**Příklad: Místní Dynamics s IFD pomocí ověřování IFD**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných sadou Dynamics DataSet.

Chcete-li kopírovat data z a do Dynamics, nastavte vlastnost typ datové sady na **DynamicsEntity**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **DynamicsEntity**. |Ano |
| entityName | Logický název entity, která se má načíst | Ne pro zdroj (Pokud je zadána možnost dotaz ve zdroji aktivity), Ano pro jímku |

> [!IMPORTANT]
>- Při kopírování dat z Dynamics je oddíl "Structure" volitelný, ale s vysokou dostupností v sadě dat dynamiky k zajištění deterministického výsledku kopírování. Definuje název sloupce a datový typ pro data Dynamics, která chcete zkopírovat. Další informace najdete v tématu [Struktura datové sady](concepts-datasets-linked-services.md#dataset-structure-or-schema) a [mapování datových typů pro Dynamics](#data-type-mapping-for-dynamics).
>- Při importu schématu v uživatelském rozhraní pro vytváření obsahu ADF odvodí schéma pomocí vzorkování horních řádků z výsledku dotazu Dynamics pro inicializaci konstrukce struktury. v takovém případě budou vynechány sloupce bez hodnot. Stejné chování platí pro spuštění kopírování, pokud není k dispozici explicitní definice struktury. Podle potřeby můžete zkontrolovat a přidat do schématu nebo struktury sady Dynamics DataSet více sloupců, které budou během kopírování za běhu zaměněny.
>- Když kopírujete data do Dynamics, část "struktura" je v sadě dat Dynamics volitelná. Sloupce, do kterých se zkopírují, jsou určeny schématem zdrojových dat. Pokud je váš zdroj souborem CSV bez hlavičky, zadejte ve vstupní datové sadě strukturu s názvem sloupce a typem dat. Namapují na pole v souboru CSV jednu po jedné v uvedeném pořadí.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností podporovaných zdrojem a typy jímky Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ zdroje

Chcete-li kopírovat data z Dynamics, nastavte typ zdroje v aktivitě kopírování na **DynamicsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **DynamicsSource**. | Ano |
| query | FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics (online a v místním prostředí). Prohlédněte si následující příklad. Další informace najdete v tématu [sestavování dotazů pomocí FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | Ne (Pokud je zadána "EntityName" v sadě dat.) |

>[!NOTE]
>Sloupec PK bude vždy zkopírován i v případě, že projekce sloupce, kterou nakonfigurujete v dotazu FetchXML, ji neobsahuje.

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

Chcete-li kopírovat data do Dynamics, nastavte typ jímky v aktivitě kopírování na **DynamicsSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **DynamicsSink**. | Ano |
| writeBehavior | Chování operace zápisu.<br/>Povolená hodnota je **"Upsert"** . | Ano |
| writeBatchSize | Počet řádků dat zapsaných do Dynamics v každé dávce. | Ne (výchozí hodnota je 10) |
| ignoreNullValues | Určuje, zda se při operaci zápisu mají ignorovat hodnoty null ze vstupních dat (s výjimkou polí klíčů).<br/>Povolené hodnoty jsou **true** a **false**.<br>- **True**: Když provedete operaci Upsert/Update, ponechte data v cílovém objektu beze změny. Při operaci INSERT vložte definovanou výchozí hodnotu.<br/>- **NEPRAVDA**: Aktualizujte data v cílovém objektu na hodnotu NULL, když provedete operaci Upsert/Update. Při operaci vložení vložte hodnotu NULL. | Ne (výchozí hodnota je false) |

>[!NOTE]
>Výchozí hodnota jímky "**writeBatchSize**" a aktivita kopírování " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " pro jímky v aplikaci Dynamics je 10. Proto jsou záznamy 100 odesílány současně do aplikace Dynamics.

Pro Dynamics 365 online existuje limit [2 souběžných volání dávky na jednu organizaci](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Pokud dojde k překročení tohoto limitu, vyvolá se chyba "Server je zaneprázdněn" před tím, než se první požadavek spustí. Udržováním "writeBatchSize" menší nebo rovno 10 se vyhnete omezení souběžných volání.

Optimální kombinace "**writeBatchSize**" a "**parallelCopies**" závisí na schématu vaší entity, např. počtu sloupců, velikosti řádku, počtu modulů plug-in, pracovních postupů/aktivit pracovních postupů, které jsou připojeny k těmto voláním atd. Výchozím nastavením 10 writeBatchSize * 10 parallelCopies je doporučení podle služby Dynamics, které by fungovalo pro většinu entit Dynamics, i když ale nemusí být nejlepší výkon. Výkon můžete vyladit úpravou kombinace v nastavení aktivity kopírování.

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

## <a name="data-type-mapping-for-dynamics"></a>Mapování datových typů pro Dynamics

Při kopírování dat z aplikace Dynamics jsou následující mapování použita z datových typů Dynamics k Data Factory dočasných datových typů. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající Data Factory datový typ ve struktuře datové sady na základě vašeho zdrojového datového typu Dynamics pomocí následující tabulky mapování.

| Datový typ Dynamics | Data Factory dočasné datový typ | Podporováno jako zdroj | Podporováno jako jímka |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Dlouhé | ✓ | ✓ |
| AttributeTypeCode. Boolean | Logická hodnota | ✓ | ✓ |
| Zaatributu. Customer | Guid | ✓ | |
| Typ atributu. DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| Typ atributu. Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Řetězec | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| Vymezit. Lookup | Guid | ✓ | ✓ (s přidruženým jedním cílem) |
| AttributeType.ManagedProperty | Logická hodnota | ✓ | |
| AttributeType.Memo | Řetězec | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| Atribut atributu. picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| Řetězec atributu. String | Řetězec | ✓ | ✓ |
| Atribut atributu. State | Int32 | ✓ | ✓ |
| Stav atributu. | Int32 | ✓ | ✓ |

> [!NOTE]
> Datové typy Dynamics mají typ atributu. CalendarRules, atributu atributu. MultiSelectPicklist a typ atributu. PartyList nejsou podporovány.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
