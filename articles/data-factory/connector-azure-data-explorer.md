---
title: Kopírování dat do nebo z Průzkumníku dat Azure pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data do nebo z Průzkumníku dat Azure s využitím aktivity kopírování v kanálu Azure Data Factory.
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
ms.date: 03/06/2019
ms.author: orspod
ms.openlocfilehash: 6138fadd060051c1b4264cd844ca2a4b8c28116a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880028"
---
# <a name="copy-data-to-or-from-azure-data-explorer-using-azure-data-factory"></a>Kopírování dat do nebo z Průzkumníku dat Azure pomocí Azure Data Factory

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory pro kopírování dat do nebo z [Průzkumník dat Azure](../data-explorer/data-explorer-overview.md). Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze všech podporovaných zdrojů úložišť dat do Průzkumníku dat Azure. Můžete také kopírování dat z Průzkumníka služby Azure Data do jakékoli podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md) tabulky.

>[!NOTE]
>Kopírování dat do a z Azure Průzkumníka dat z/a na místní úložiště pomocí modul Integration Runtime se podporuje od verze 3.14.

Průzkumník dat Azure konektor umožňuje postupujte takto:

* Kopírování dat pomocí ověřování tokenu aplikací Azure Active Directory (Azure AD) s **instanční objekt služby**.
* Jako zdroj načtení dat pomocí dotazů KQL (Kusto).
* Jako jímka připojte data do cílové tabulky.

## <a name="getting-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Průzkumník dat Azure.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Průzkumník dat Azure konektor využívá ověřování instančních objektů. Následujícím postupem získejte objekt služby a udělení oprávnění:

1. Zaregistrovat aplikaci entity ve službě Azure Active Directory (Azure AD) pomocí následujících [registrace aplikace pomocí tenanta služby Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte hlavní správné oprávnění služby v Průzkumníku dat Azure. Odkazovat na [oprávnění spravovat Průzkumník dat Azure databáze](../data-explorer/manage-database-permissions.md) s podrobnými informacemi o role a oprávnění, stejně jako návod na správu oprávnění. Obecně platí je potřeba

    - **Jako zdroj**, přidělit nejméně **databáze prohlížeč** role k vaší databázi.
    - **Jako jímku**, přidělit nejméně **databáze přijímač** role k vaší databázi.

>[!NOTE]
>Při použití ADF uživatelského rozhraní k vytváření, může operace výpis databáze na propojenou službu nebo výpis tabulek v datové sadě vyžadují vyšší privileged oprávněním pro instanční objekt. Alternativně můžete ručně zadat název databáze a název tabulky. Zkopírujte aktivity spuštění funguje, tak dlouho, dokud instanční objekt se udělují s správná oprávnění ke čtení a zápisu dat.

Průzkumník dat Azure, propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureDataExplorer** | Ano |
| endpoint | Adresa URL koncového bodu clusteru Průzkumník dat Azure, ve formátu jako `https://<clusterName>.<regionName>.kusto.windows.net`. | Ano |
| databáze | Název databáze. | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. To je, co je obvykle říká "**ID úřadu**" v [Kusto připojovací řetězec](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Načtení ukázáním pomocí myši v pravém horním rohu webu Azure portal. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. To je, co je obvykle říká "**ID klienta aplikace AAD**" v [Kusto připojovací řetězec](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. To je, co je obvykle říká "**klíč aplikace AAD**" v [Kusto připojovací řetězec](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Označte toto pole jako **SecureString** bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). | Ano |

**Příklad vlastnosti propojené služby:**

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které jsou podporovány v objektu DataSet Průzkumník dat Azure.

Pro kopírování dat do Průzkumníku dat Azure, nastavte vlastnost typ datové sady na **AzureDataExplorerTable**.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** musí být vlastnost nastavena na **AzureDataExplorerTable** | Ano |
| tabulka | Název tabulky, která odkazuje propojenou službu. | Ano pro jímku; Ne pro zdroj |

**Příklad vlastnosti datové sady**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "table": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Průzkumník služby Azure Data zdroje a jímky.

### <a name="azure-data-explorer-as-source"></a>Průzkumník služby Azure Data jako zdroj

Ke zkopírování dat z Průzkumníka služby Azure Data, nastavte **typ** vlastnosti ve zdroji aktivitu kopírování k **AzureDataExplorerSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost zdroje aktivity kopírování musí být nastavena na: **AzureDataExplorerSource** | Ano |
| query | Podle požadavku jen pro čtení [KQL formátu](/azure/kusto/query/). Použijte vlastní dotaz KQL jako odkaz. | Ano |
| queryTimeout | Doba čekání před požadavkem dotazu vyprší časový limit. Výchozí hodnota je 10 minut (00:10:00); povolené maximum je 1 hodina (01: 00:00). | Ne |

>[!NOTE]
>Azure zdroj Průzkumník dat ve výchozím nastavení má omezení velikosti 500 000 záznamů nebo 64 MB. Pokud chcete načíst všechny záznamy nezkrácený, můžete zadat `set notruncation;` na začátku dotazu. Odkazovat na [dotazování omezení](https://docs.microsoft.com/azure/kusto/concepts/querylimits) na další podrobnosti.

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

### <a name="azure-data-explorer-as-sink"></a>Průzkumník služby Azure Data jako jímka

Pro kopírování dat do Průzkumníku dat Azure, nastavte vlastnost typ v jímky aktivity kopírování k **AzureDataExplorerSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** části:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** vlastnost jímky aktivity kopírování musí být nastavena na: **AzureDataExplorerSink** | Ano |
| ingestionMappingName | Název předem vytvořené **[mapování](/azure/kusto/management/mappings#csv-mapping)** v tabulce Kusto. Chcete-li namapovat sloupce ze zdroje do Průzkumníku dat Azure – které se vztahuje na **[všechny podporované zdrojové úložiště/formáty](copy-activity-overview.md#supported-data-stores-and-formats)** včetně CSV nebo JSON nebo Avro formáty atd, můžete použít aktivitu kopírování [sloupec mapování](copy-activity-schema-and-type-mapping.md) (implicitně podle názvu nebo explicitně nakonfigurovat) a/nebo Průzkumníka služby Azure Data mapování. | Ne |

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

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).