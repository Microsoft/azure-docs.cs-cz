---
title: Kopírování dat do nebo z Azure Data Exploreru
description: Zjistěte, jak kopírovat data do nebo z Azure Data Explorer pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 5f0eada5184f2c8e36b0deb5a9d27a8a9230776d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382769"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Explorerpomocí Azure Data Factory

Tento článek popisuje, jak použít aktivitu kopírování v Azure Data Factory ke kopírování dat do nebo z [Azure Data Explorer](/azure/data-explorer/data-explorer-overview). Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který nabízí obecný přehled o aktivitě kopírování.

>[!TIP]
>Pro Azure Data Factory a Integrace Azure Data Explorer obecně, další informace z [integrace Azure Data Explorer s Azure Data Factory](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Průzkumníka dat Azure je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z libovolného úložiště dat podporovaného zdroje můžete zkopírovat do Průzkumníka dat Azure. Můžete také zkopírovat data z Průzkumníka dat Azure do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, které aktivita kopírování podporuje jako zdroje nebo jímky, naleznete v tabulce [Podporovaná data.](copy-activity-overview.md#supported-data-stores-and-formats)

>[!NOTE]
>Kopírování dat do nebo z Azure Data Explorer prostřednictvím místního úložiště dat pomocí runtime integrace s vlastním hostitelem je podporováno ve verzi 3.14 a novější.

Pomocí konektoru Průzkumníka dat Azure můžete provést následující kroky:

* Kopírování dat pomocí ověřování tokenu aplikace Azure Active Directory (Azure AD) s **instančním objektem**.
* Jako zdroj načtěte data pomocí dotazu KQL (Kusto).
* Jako jímku připojit data do cílové tabulky.

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Návod konektoru Azure Data Explorer najdete v [tématu Kopírování dat do/z Azure Data Explorer pomocí Azure Data Factory](/azure/data-explorer/data-factory-load-data) a hromadné kopírování z databáze do [Průzkumníka dat Azure](/azure/data-explorer/data-factory-template).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Datové továrny specifické pro konektor Průzkumníka dat Azure.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Konektor Průzkumníka dat Azure používá ověřování instančního objektu. Chcete-li získat instanční objekt a udělit oprávnění, postupujte takto:

1. Zaregistrujte entitu aplikace ve Službě Azure Active Directory podle kroků v [části Registrace aplikace u klienta Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správná oprávnění v Průzkumníku dat Azure. Podrobné informace o rolích a oprávněních a o správě oprávnění najdete v [tématu Správa oprávnění databáze Průzkumníka dat Azure.](/azure/data-explorer/manage-database-permissions) Obecně musíte:

    - **Jako zdroj**udělte databázi alespoň roli **prohlížeče databáze.**
    - **Jako jímky**udělte databázi alespoň roli **ingestestátoru** databáze.

>[!NOTE]
>Při použití uživatelského rozhraní Factory data k vytváření, váš přihlašovací uživatelský účet se používá k seznamu clusterů Azure Data Explorer, databází a tabulek. Pokud nemáte oprávnění k těmto operacím, zadejte název ručně.

Pro propojenou službu Azure Data Explorer jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **AzureDataExplorer**. | Ano |
| endpoint | Adresa URL koncového bodu clusteru Azure `https://<clusterName>.<regionName>.kusto.windows.net`Data Explorer s formátem jako . | Ano |
| database | Název databáze. | Ano |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Toto se v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)označuje jako "ID autority" . Načtěte ho najetím ukazatele myši v pravém horním rohu portálu Azure. | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. Toto je známé jako "ID klienta aplikace AAD" v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Tento kód se v [připojovacím řetězci Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)označuje jako "aplikační klíč AAD" . Označte toto pole jako **SecureString** pro bezpečné uložení v datové továrně nebo [odkazna zabezpečená data uložená v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |

**Vlastnosti propojených služeb příklad:**

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, najdete [v tématu Datové sady v Azure Data Factory](concepts-datasets-linked-services.md). V této části jsou uvedeny vlastnosti, které podporuje datová sada Průzkumníka dat Azure.

Chcete-li zkopírovat data do Průzkumníka dat Azure, nastavte vlastnost type datové sady na **AzureDataExplorerTable**.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** musí být nastavena na **AzureDataExplorerTable**. | Ano |
| tabulka | Název tabulky, na kterou odkazuje propojená služba. | Ano pro umyvadlo; Ne pro zdroj |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete [v tématu Kanály a aktivity v Azure Data Factory](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností, které azure data explorer zdroje a jímky podporu.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer jako zdroj

Pokud chcete zkopírovat data z Průzkumníka dat Azure, nastavte vlastnost **type** ve zdroji Kopírovat aktivitu do **AzureDataExplorerSource**. V části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** zdroje aktivity kopírování musí být nastavena na: **AzureDataExplorerSource** | Ano |
| query | Požadavek jen pro čtení daný ve [formátu KQL](/azure/kusto/query/). Jako referenci použijte vlastní dotaz KQL. | Ano |
| queryTimeout | Čekací doba před požadavek na dotaz časový limit. Výchozí hodnota je 10 min (00:10:00); maximální hodnota je 1 hodina (01:00:00). | Ne |
| noTruncation | Označuje, zda chcete zkrátit sadu vrácených výsledků. Ve výchozím nastavení je výsledek zkrácen po 500 000 záznamech nebo 64 megabajtech (MB). Zkrácení se důrazně doporučuje zajistit správné chování aktivity. |Ne |

>[!NOTE]
>Ve výchozím nastavení má zdroj Průzkumníka dat Azure limit velikosti 500 000 záznamů nebo 64 MB. Chcete-li načíst všechny záznamy bez `set notruncation;` zkrácení, můžete zadat na začátku dotazu. Další informace naleznete v [tématu Omezení dotazů](https://docs.microsoft.com/azure/kusto/concepts/querylimits).

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

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer jako jímky

Chcete-li zkopírovat data do Průzkumníka dat Azure, nastavte vlastnost type v jímce aktivity kopírování do **AzureDataExplorerSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost **type** jímky aktivity kopírování musí být nastavena na: **AzureDataExplorerSink**. | Ano |
| ingestionMappingName | Název předem vytvořeného [mapování](/azure/kusto/management/mappings#csv-mapping) v tabulce Kusto. Chcete-li mapovat sloupce ze zdroje na Azure Data Explorer (který se vztahuje na [všechna podporovaná zdrojová úložiště a formáty](copy-activity-overview.md#supported-data-stores-and-formats), včetně formátů CSV/JSON/Avro), můžete použít [mapování sloupců](copy-activity-schema-and-type-mapping.md) aktivity kopírování (implicitně podle názvu nebo explicitně podle konfigurace) a nebo mapování Průzkumníka dat Azure. | Ne |
| additionalProperties | Vak vlastností, který se dá použít k určení některé z vlastností ingestování, které nejsou nastaveny již jímky Průzkumníka dat Azure. Konkrétně může být užitečné pro určení ingestování značek. Další informace najdete v dokumentu o [prozkoumání dat](https://kusto.azurewebsites.net/docs/management/data-ingestion/index.html)azure data . | Ne |

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

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Další informace o vlastnostech naleznete v tématu [Aktivita vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky

* Seznam úložišť dat, která aktivita kopírování v Azure Data Factory podporuje jako zdroje a propady, najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

* Přečtěte si další informace o tom, jak [zkopírovat data z Azure Data Factory do Azure Data Exploreru](/azure/data-explorer/data-factory-load-data).
