---
title: Kopírování dat z a do Dynamics CRM nebo Dynamics 365 (Common Data Service) pomocí služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z aplikace Microsoft Dynamics CRM nebo Microsoft Dynamics 365 (Common Data Service) a není podporován. úložiště dat jímky nebo z podporovaných úložišť dat zdroje k Dynamics CRM nebo Dynamics 365 s využitím aktivity kopírování v kanálu datové továrny.
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
ms.date: 09/26/2018
ms.author: jingwang
ms.openlocfilehash: ce3c494dc0b8c962c8dae0af38d3cb5476cdf48b
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406171"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopírování dat z a do Dynamics 365 (Common Data Service) nebo Dynamics CRM pomocí služby Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat z a do aplikace Microsoft Dynamics 365 nebo Microsoft Dynamics CRM. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Můžete kopírovat data z Dynamics 365 (Common Data Service) nebo Dynamics CRM do jakékoli podporovaného úložiště dat jímky. Můžete také můžete kopírovat data ze všech podporovaných zdrojů úložišť dat do Dynamics 365 (Common Data Service) nebo Dynamics CRM. Seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky, najdete v článku [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Tento konektor Dynamics podporuje následující verze Dynamics a typy ověřování. (IFD je zkratka pro nasazení přístupem k Internetu.)

| Verze Dynamics | Typy ověření | Propojená služba ukázky |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM Online | Office365 | [Dynamics online + ověřování Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 on-premises s IFD <br> Dynamics CRM 2016 on-premises s IFD <br> Dynamics CRM 2015 on-premises s IFD | IFD | [Dynamics místně pomocí Internetového + IFD vícefaktorového ověřování](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Pro Dynamics 365 konkrétně následující typy aplikací se podporují:

- Dynamics 365 for Sales
- Dynamics 365 pro služby zákazníkům
- Dynamics 365 for Field Service
- Dynamics 365 pro Project Service Automation
- Dynamics 365 pro uvedení na trh

Typy jiné aplikace, např. operace a financí, Talentů, nejsou podporovány atd.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Dynamics.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující vlastnosti jsou podporovány pro propojenou službu Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 a Dynamics CRM Online

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení Dynamics instance. Musí být **"Online"** Dynamics online. | Ano |
| serviceUri | Adresa URL služby vaší Dynamics instance, například `https://adfdynamics.crm.dynamics.com`. | Ano |
| authenticationType. | Typ ověřování pro připojení k serveru Dynamics. Zadejte **"Office 365"** Dynamics online. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku Pokud zdroj propojené služby nemá prostředí integration runtime |

>[!IMPORTANT]
>Při kopírování dat do Dynamics nelze provádět kopírování použít výchozí prostředí Azure Integration Runtime. Jinými slovy, pokud zdrojem propojená služba nemá zadaný integration runtime, explicitně [vytvořit prostředí Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) umístění blízko vaší instanci Dynamics. Přidružte ji v propojené službě Dynamics jako v následujícím příkladu.

>[!NOTE]
>Konektor Dynamics pro volitelné "název organizace" vlastnost použít k identifikaci vaší instance Dynamics 365 s/CRM Online. Zatímco pořád funguje, můžete se doporučujeme, abyste místo toho zadejte novou vlastnost "serviceuri:" získat lepší výkon pro instanci zjišťování.

**Příklad: Dynamics online s použitím ověřování Office365**

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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 a Dynamics CRM on-premises s IFD

*Další vlastnosti, které porovnat s Dynamics online jsou "hostName" a "portu".*

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **Dynamics**. | Ano |
| deploymentType | Typ nasazení Dynamics instance. Musí být **"OnPremisesWithIfd"** pro Dynamics místně pomocí internetového nasazení.| Ano |
| název hostitele | Název hostitele serveru Dynamics na místě. | Ano |
| port | Port serveru Dynamics na místě. | Ne, výchozí je 443 |
| Název organizace | Název organizace Dynamics instance. | Ano |
| authenticationType. | Typ ověřování pro připojení k serveru Dynamics. Zadejte **"Ifd"** pro Dynamics on-premises s IFD. | Ano |
| uživatelské jméno | Zadejte uživatelské jméno pro připojení k Dynamics. | Ano |
| heslo | Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. Můžete zvolit toto pole označení SecureString bezpečně uložit ve službě ADF nebo ukládání hesel ve službě Azure Key Vault a umožnit aktivity kopírování o přijetí změn z něj při kopírování dat – Další informace z [Store přihlašovacích údajů ve službě Key Vault](store-credentials-in-key-vault.md). | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne pro zdroj, Ano pro jímku |

>[!IMPORTANT]
>Můžete kopírovat data do Dynamics, explicitně [vytvořit prostředí Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) umístění blízko vaší instanci Dynamics. Přidružte ji v propojené službě jako v následujícím příkladu.

**Příklad: Dynamics místně pomocí Internetového pomocí Internetového ověřování**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Dynamics datové sady.

Pro kopírování dat z a do Dynamics, nastavte vlastnost typ datové sady na **DynamicsEntity**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **DynamicsEntity**. |Ano |
| EntityName | Logický název entity načíst. | Ne pro zdroj (Pokud je zadán "dotaz" ve zdroji aktivity), Ano pro jímku |

> [!IMPORTANT]
>- Při kopírování dat z Dynamics oddílu "struktura" je volitelné, ale recommanded v Dynamics datovou sadu, aby výsledkem deterministické kopírování. Definuje sloupce název a datový typ dat aplikace Dynamics, který chcete zkopírovat. Další informace najdete v tématu [struktury datové sady](concepts-datasets-linked-services.md#dataset-structure) a [mapování datového typu pro Dynamics](#data-type-mapping-for-dynamics).
>- Při importu schématu při vytváření uživatelského rozhraní, ADF odvodit schéma vzorkováním horní řádky z Dynamics výsledku dotazu inicializovat konstrukce struktury, ve kterém budou vypuštěny případu sloupce s žádné hodnoty. Můžete zkontrolovat a přidat další sloupce do Dynamics datovou sadu schémat/struktury podle potřeby, které budou zachované při kopírování modulu runtime.
>- Při kopírování dat do Dynamics "struktura" část je nepovinná v datové sadě Dynamics. Sloupce, které chcete zkopírovat do se určuje podle schématu datového zdroje. Pokud je zdrojem bez záhlaví souboru CSV ve vstupní sadě, zadejte "struktura" se název a datový typ sloupce. Jsou mapovány na pole v souboru CSV postupně v pořadí.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných typů Dynamics zdroje a jímky.

### <a name="dynamics-as-a-source-type"></a>Dynamics jako typ zdroje

Ke zkopírování dat z Dynamics, nastavte typ zdroje v aktivitě kopírování do **DynamicsSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **DynamicsSource**. | Ano |
| query | Dotazů FetchXML je proprietární dotazovací jazyk, který se používá v Dynamics (online i v místním prostředí). Prohlédněte si následující příklad. Další informace najdete v tématu [sestavování dotazů s FeachXML](https://msdn.microsoft.com/library/gg328332.aspx). | Ne (když je "entityName" v datové sadě zadán) |

>[!NOTE]
>Sloupec PK budou vždy zkopírovány i v případě, že sloupec projekce, které nakonfigurujete v dotazu FetchXML neobsahuje.

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

Ke zkopírování dat do Dynamics, nastavte typ jímky v aktivitě kopírování do **DynamicsSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **DynamicsSink**. | Ano |
| WriteBehavior | Chování zápisu operace.<br/>Povolená hodnota je **"Upsert"**. | Ano |
| WriteBatchSize | Počet řádků dat zapsaných do Dynamics v každé dávce. | Ne (výchozí hodnota je 10) |
| ignoreNullValues | Určuje, jestli se mají ignorovat během operace zápisu hodnot null ze vstupních dat (s výjimkou polí klíčů).<br/>Povolené hodnoty jsou **true** a **false**.<br>- **Hodnota TRUE**: ponechat data v cílové objektů beze změny, pokud tak učiníte, operace upsert nebo aktualizovat. Definovaná výchozí hodnota vložte, když provedete operaci vložení.<br/>- **False**: aktualizace dat v cílového objektu na hodnotu NULL, pokud tak učiníte, operace upsert nebo aktualizovat. Vložení hodnoty NULL, když provedete operaci vložení. | Ne (výchozí hodnota je false) |

>[!NOTE]
>Výchozí hodnota jímka "**writeBatchSize**"a aktivita kopírování"**[parallelCopies](copy-activity-performance.md#parallel-copy)**" Dynamics jímka jsou obě 10. Proto se 100 záznamů odesílají na Dynamics současně.

Pro Dynamics 365 online je stanovený limit [2 batch souběžných volání na organizaci](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Pokud dojde k překročení tohoto limitu, chybu "Zaneprázdněný Server", je vyvolána předtím, než je někdy spustí první požadavek. Udržování "writeBatchSize" menší než nebo rovno 10 by se vyhnout takové omezení souběžných volání.

Ideální kombinaci "**writeBatchSize**"a"**parallelCopies**" závisí na schéma vaší entity třeba číslo sloupce, velikost řádku, počet aktivit moduly plug-in a pracovních postupů a pracovních postupů připojili Tato volání atd. Ve výchozím nastavení 10 writeBatchSize * 10 parallelCopies je doporučení podle služby Dynamics, která bude fungovat pro většinu entit Dynamics ale nemusí být nejlepší výkon. Optimalizaci výkonu úpravou kombinaci v nastavení aktivity kopírování.

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

Při kopírování dat z Dynamics se používají následující mapování z datových typů Dynamics do služby Data Factory dočasné datových typů. Informace o tom, jak aktivity kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

Nakonfigurujte odpovídající typ dat Data Factory ve struktuře datové sady založené na zdroji Dynamics datový typ pomocí následující tabulky mapování.

| Datový typ Dynamics | Data Factory dočasné datový typ | Podporované jako zdroj | Podporované jako jímka |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Dlouhé | ✓ | ✓ |
| AttributeTypeCode.Boolean | Logická hodnota | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | | 
| AttributeType.DateTime | Datum a čas | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Řetězec | ✓ | ✓ |
| AttributeType.Integer | Datový typ Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (s jedním cílem přidružené) |
| AttributeType.ManagedProperty | Logická hodnota | ✓ | |
| AttributeType.Memo | Řetězec | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Datový typ Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | Řetězec | ✓ | ✓ |
| AttributeType.State | Datový typ Int32 | ✓ | ✓ |
| AttributeType.Status | Datový typ Int32 | ✓ | ✓ |


> [!NOTE]
> Datové typy Dynamics AttributeType.CalendarRules a AttributeType.PartyList nejsou podporovány.

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
