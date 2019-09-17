---
title: Kopírování dat do nebo z Azure Průzkumník dat pomocí Azure Data Factory
description: Naučte se, jak kopírovat data do a z Azure Průzkumník dat pomocí aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 08/01/2019
ms.author: orspodek
ms.openlocfilehash: 5cb08ddafe2075ae27ced6d70894696025df0a86
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010258"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Průzkumník dat pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data do a z [Azure Průzkumník dat](../data-explorer/data-explorer-overview.md). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Průzkumník dat se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do Azure Průzkumník dat. Data z Azure Průzkumník dat taky můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md) tabulky.

>[!NOTE]
>Kopírování dat do a z Azure Průzkumník dat z místního úložiště dat z nebo do místního úložiště dat pomocí Integration Runtime, který je v místním prostředí podporován od verze 3,14.

Konektor Azure Průzkumník dat vám umožní provést následující akce:

* Zkopírujte data pomocí ověřování tokenu aplikace Azure Active Directory (Azure AD) s **instančním objektem**.
* Jako zdroj načte data pomocí dotazu KQL (Kusto).
* Jako jímku přidejte data do cílové tabulky.

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Návod k používání služby Azure Průzkumník dat Connector najdete v tématu [kopírování dat do/z Azure Průzkumník dat pomocí Azure Data Factory](../data-explorer/data-factory-load-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Průzkumník dat Connector.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor služby Azure Průzkumník dat používá ověřování instančního objektu. Pomocí těchto kroků můžete získat instanční objekt a udělit oprávnění:

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění ve službě Azure Průzkumník dat. Přečtěte si téma [Správa oprávnění k databázi Azure Průzkumník dat](../data-explorer/manage-database-permissions.md) a podrobné informace o rolích a oprávněních a také o tom, jak spravovat oprávnění. Obecně je třeba

    - **Jako zdroj**udělte databázi aspoň roli **Database Viewer** .
    - **Jako jímky**udělte databázi aspoň roli ingestování **databáze** .

>[!NOTE]
>Když použijete uživatelské rozhraní ADF k vytvoření, účet přihlášení uživatele se používá k vypsání clusterů, databází a tabulek Azure Průzkumník dat. Pokud pro takovou operaci nemáte oprávnění, zadejte název ručně.

Pro propojenou službu Azure Průzkumník dat se podporují tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **AzureDataExplorer** . | Ano |
| endpoint | Adresa URL koncového bodu clusteru Azure Průzkumník dat s formátem jako `https://<clusterName>.<regionName>.kusto.windows.net`. | Ano |
| database | Název databáze | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. To je to, co normálně znáte jako**ID autority**v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. To je to, co normálně znáte jako "**ID klienta aplikace AAD**" v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. To je to, co normálně znáte jako "**klíč aplikace AAD**" v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou Azure Průzkumník dat.

Pokud chcete kopírovat data do Azure Průzkumník dat, nastavte vlastnost Type datové sady na **AzureDataExplorerTable**.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** musí být nastavená na **AzureDataExplorerTable** . | Ano |
| table | Název tabulky, na kterou odkazuje propojená služba. | Ano pro jímku; Ne pro zdroj |

**Příklad vlastností datové sady**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností, které služba Azure Průzkumník dat source a jímka podporuje.

### <a name="azure-data-explorer-as-source"></a>Azure Průzkumník dat jako zdroj

Pokud chcete kopírovat data z Azure Průzkumník dat, nastavte vlastnost **typ** ve zdroji aktivity kopírování na **AzureDataExplorerSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** zdroje aktivity kopírování musí být nastavená na: **AzureDataExplorerSource** | Ano |
| query | Požadavek jen pro čtení zadaný ve [formátu KQL](/azure/kusto/query/) Použijte vlastní dotaz KQL jako referenci. | Ano |
| queryTimeout | Doba čekání před vypršením časového limitu požadavku na dotaz Výchozí hodnota je 10 minut (00:10:00); maximální povolená hodnota je 1 hodina (01:00:00). | Ne |
| Zkrácení | Označuje, zda se má zkrátit vrácená sada výsledků. Ve výchozím nastavení se výsledek zkrátí po 500 000 záznamech nebo 64 MB. Zkrácení se důrazně doporučuje pro správné chování aktivity. |Ne |

>[!NOTE]
>Služba Azure Průzkumník dat source ve výchozím nastavení má omezení velikosti 500 000 záznamů nebo 64 MB. Chcete-li načíst všechny záznamy bez zkrácení, můžete zadat `set notruncation;` na začátku dotazu. Další podrobnosti najdete v tématu [omezení dotazů](https://docs.microsoft.com/azure/kusto/concepts/querylimits) .

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

Pokud chcete kopírovat data do Azure Průzkumník dat, nastavte vlastnost typ v jímky aktivity kopírování na **AzureDataExplorerSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **Type** jímky aktivity kopírování musí být nastavená na: **AzureDataExplorerSink** | Ano |
| ingestionMappingName | Název předem vytvořeného **[mapování](/azure/kusto/management/mappings#csv-mapping)** v tabulce Kusto Pokud chcete namapovat sloupce ze zdrojového do služby Azure Průzkumník dat – to platí pro **[všechna podporovaná zdrojová úložiště/formáty](copy-activity-overview.md#supported-data-stores-and-formats)** , včetně formátů CSV/JSON/Avro atd. můžete použít [mapování sloupce](copy-activity-schema-and-type-mapping.md) aktivita kopírování (implicitně podle názvu nebo explicitně podle konfigurace) a /or mapování Azure Průzkumník dat. | Ne |

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
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
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

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další postup

* Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).

* Přečtěte si další informace o [kopírování dat z Azure Data Factory do Azure Průzkumník dat](/azure/data-explorer/data-factory-load-data).