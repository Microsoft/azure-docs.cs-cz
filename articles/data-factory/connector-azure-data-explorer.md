---
title: Kopírování dat do nebo z Azure Průzkumník dat
description: Naučte se, jak kopírovat data do a z Azure Průzkumník dat pomocí aktivity kopírování v kanálu Azure Data Factory.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100379977"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Průzkumník dat pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do a z [Azure Průzkumník dat](/azure/data-explorer/data-explorer-overview). Vytvoří se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který nabízí obecný přehled aktivity kopírování.

>[!TIP]
>Azure Data Factory a Azure Průzkumník dat Integration obecně najdete další informace v [integraci služby azure Průzkumník dat s Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Průzkumník dat se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do Azure Průzkumník dat. Data z Azure Průzkumník dat taky můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako zdroje nebo jímky, najdete v tabulce [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats) .

>[!NOTE]
>Kopírování dat do a z Azure Průzkumník dat prostřednictvím místního úložiště dat pomocí prostředí Integration runtime v místním prostředí je podporované ve verzi 3,14 a novější.

Pomocí konektoru služby Azure Průzkumník dat můžete provádět následující akce:

* Zkopírujte data pomocí ověřování tokenu aplikace Azure Active Directory (Azure AD) s **instančním objektem**.
* Jako zdroj načte data pomocí dotazu KQL (Kusto).
* Jako jímku přidejte data do cílové tabulky.

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Návod ke službě Azure Průzkumník dat Connector najdete v tématu [kopírování dat do/z Azure Průzkumník dat použití Azure Data Factory](/azure/data-explorer/data-factory-load-data) a [hromadného kopírování z databáze do Azure Průzkumník dat](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Průzkumník dat Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor služby Azure Průzkumník dat používá ověřování instančního objektu. Pomocí těchto kroků můžete získat instanční objekt a udělit oprávnění:

1. Pomocí postupu v části [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory. Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správná oprávnění ve službě Azure Průzkumník dat. Podrobné informace o rolích a oprávněních a o správě oprávnění najdete v článku [Správa oprávnění k databázi Azure Průzkumník dat](/azure/data-explorer/manage-database-permissions) . Obecně platí, že:

    - **Jako zdroj** udělte databázi aspoň roli **prohlížeč databáze** .
    - **Jako jímky** udělte databázi aspoň roli ingestování **databáze** .

>[!NOTE]
>Když použijete uživatelské rozhraní Data Factory k vytvoření, použije se účet přihlášení uživatele k vypsání clusterů, databází a tabulek Azure Průzkumník dat. Pokud nemáte oprávnění k těmto operacím, zadejte název ručně.

Pro propojenou službu Azure Průzkumník dat se podporují následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureDataExplorer**. | Yes |
| endpoint | Adresa URL koncového bodu clusteru Azure Průzkumník dat s formátem jako `https://<clusterName>.<regionName>.kusto.windows.net` . | Yes |
| database | Název databáze | Yes |
| tenant | Zadejte informace o tenantovi (název domény nebo ID tenanta), pod kterým se vaše aplikace nachází. To se označuje jako ID autority v [připojovacím řetězci Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Načtěte ho tak, že najedete myší na ukazatel myši v pravém horním rohu Azure Portal. | Yes |
| servicePrincipalId | Zadejte ID klienta aplikace. To se označuje jako "ID klienta aplikace AAD" v [připojovacím řetězci Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Yes |
| servicePrincipalKey | Zadejte klíč aplikace. To se označuje jako "klávesa aplikace AAD" v [připojovacím řetězci Kusto](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Označte toto pole jako **SecureString** , abyste ho bezpečně ukládali do Data Factory nebo aby [odkazovala na zabezpečená Data uložená v Azure Key Vault](store-credentials-in-key-vault.md). | Yes |

**Příklad vlastností propojené služby:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu datové sady v Azure Data Factory](concepts-datasets-linked-services.md). V této části jsou uvedeny vlastnosti, které podporuje datová sada Azure Průzkumník dat.

Pokud chcete kopírovat data do Azure Průzkumník dat, nastavte vlastnost Type datové sady na **AzureDataExplorerTable**.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** musí být nastavená na **AzureDataExplorerTable**. | Yes |
| tabulka | Název tabulky, na kterou odkazuje propojená služba. | Ano pro jímku; Ne pro zdroj |

**Příklad vlastností datové sady:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md). V této části najdete seznam vlastností, které Azure Průzkumník dat zdroje a jímky podporují.

### <a name="azure-data-explorer-as-source"></a>Azure Průzkumník dat jako zdroj

Pokud chcete kopírovat data z Azure Průzkumník dat, nastavte vlastnost **typ** ve zdroji aktivity kopírování na **AzureDataExplorerSource**. V části **zdroj** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na: **AzureDataExplorerSource** . | Yes |
| query | Požadavek jen pro čtení zadaný ve [formátu KQL](/azure/kusto/query/) Použijte vlastní dotaz KQL jako referenci. | Yes |
| queryTimeout | Doba čekání před vypršením časového limitu požadavku na dotaz Výchozí hodnota je 10 minut (00:10:00); maximální povolená hodnota je 1 hodina (01:00:00). | No |
| Zkrácení | Označuje, zda se má zkrátit vrácená sada výsledků. Ve výchozím nastavení se výsledek zkrátí po 500 000 záznamech nebo 64 megabajtů (MB). Pro zajištění správného chování aktivity se důrazně doporučuje zkrátit. |No |

>[!NOTE]
>Ve výchozím nastavení má Azure Průzkumník dat source omezení velikosti 500 000 záznamů nebo 64 MB. Chcete-li načíst všechny záznamy bez zkrácení, můžete zadat `set notruncation;` na začátku dotazu. Další informace najdete v tématu [omezení dotazů](/azure/kusto/concepts/querylimits).

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Průzkumník dat jako jímka

Pokud chcete kopírovat data do Azure Průzkumník dat, nastavte vlastnost typ v jímky aktivity kopírování na **AzureDataExplorerSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na: **AzureDataExplorerSink**. | Yes |
| ingestionMappingName | Název předem vytvořeného [mapování](/azure/kusto/management/mappings#csv-mapping) v tabulce Kusto Pokud chcete namapovat sloupce ze zdrojového do služby Azure Průzkumník dat (to platí pro [všechna podporovaná zdrojová úložiště a formáty](copy-activity-overview.md#supported-data-stores-and-formats), včetně formátů CSV/JSON/Avro), můžete použít [mapování sloupce](copy-activity-schema-and-type-mapping.md) aktivita kopírování (implicitně podle názvu nebo explicitně jako nakonfigurovaná) a mapování Azure Průzkumník dat. | No |
| additionalProperties | Kontejner objektů A dat, který se dá použít k zadání jakékoli vlastnosti ingestování, které už nejsou nastavené jímkou Průzkumník dat služby Azure. Konkrétně to může být užitečné při zadávání značek přijímání. Další informace najdete v [dokumentu Azure Data prozkoumání příjmu dat](/azure/data-explorer/ingestion-properties). | No |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Další informace o vlastnostech najdete v tématu [aktivita vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

* Seznam úložišť dat, která aktivita kopírování v Azure Data Factory podporuje jako zdroje a jímky, najdete v tématu [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

* Přečtěte si další informace o tom, jak [Kopírovat data z Azure Data Factory do Azure Průzkumník dat](/azure/data-explorer/data-factory-load-data).