---
title: Kopírování dat z a do úložiště tabulek v Azure
description: Naučte se, jak kopírovat data z podporovaných úložišť zdrojů do úložiště tabulek Azure nebo z tabulkového úložiště do podporovaných úložišť jímky pomocí Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/27/2019
ms.openlocfilehash: b64b0f32b7e8d94115facf43646a5a030697d80f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444413"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopírování dat z a do úložiště tabulek v Azure pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-table-connector.md)
> * [Aktuální verze](connector-azure-table-storage.md)

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do úložiště tabulek Azure. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Table Storage se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)

Data můžete kopírovat z libovolného podporovaného zdrojového úložiště dat do tabulkového úložiště. Data z úložiště tabulek také můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tato konektorová tabulka Azure podporuje kopírování dat pomocí ověření klíče účtu a sdíleného přístupového podpisu služby.

## <a name="get-started"></a>Začít

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobné informace o vlastnostech, které slouží k definování Data Factory entit specifických pro úložiště tabulek.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

### <a name="use-an-account-key"></a>Použití klíče účtu

Propojenou službu Azure Storage můžete vytvořit pomocí klíče účtu. Poskytuje datovou továrnu s globálním přístupem k úložišti. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureTableStorage**. |Ano |
| connectionString | Zadejte informace potřebné pro připojení k úložišti pro vlastnost připojovací řetězec. <br/>Můžete také umístit klíč účtu do Azure Key Vault a načíst `accountKey` konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Můžete použít Azure Integration Runtime nebo místní Integration Runtime (Pokud se vaše úložiště dat nachází v privátní síti). Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste použili propojenou službu typu "AzureStorage", je stále podporovaná tak, jak je, a když jste se rozhodli použít tento nový typ propojené služby "AzureTableStorage", který se bude přesměrovat.

**Příklad:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: klíč účtu úložiště v Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Použít ověřování pomocí sdíleného přístupového podpisu

Propojenou službu úložiště můžete také vytvořit pomocí sdíleného přístupového podpisu. Poskytuje datovou továrnu s omezeným/časově vázaným přístupem ke všem/konkrétním prostředkům v úložišti.

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Můžete ji použít k udělení omezeného oprávnění klienta k objektům v účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Nemusíte sdílet přístupové klíče vašeho účtu. Sdílený přístupový podpis je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, musí klient pouze a zajistěte tak předání sdílený přístupový podpis odpovídajícího konstruktoru nebo metody. Další informace o sdílených přístupových podpisů najdete v tématu [sdílené přístupové podpisy: vysvětlení modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory teď podporuje obě **služby sdílené přístupové podpisy** a **účet sdílené přístupové podpisy**. Další informace o sdílených přístupových podpisech najdete v článku [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Vygenerovat sdílený přístupový podpis služby pro účet úložiště, spusťte následující příkazy Powershellu. Nahraďte zástupné symboly a udělit příslušná oprávnění.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Chcete-li použít ověřování pomocí sdíleného přístupového podpisu, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureTableStorage**. |Ano |
| sasUri | Zadejte identifikátor URI SAS identifikátoru URI sdíleného přístupového podpisu k tabulce. <br/>Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. Do Azure Key Vault můžete také vložit token SAS a využít tak automatické otočení a odebrat část tokenu. Další podrobnosti najdete v následujících ukázkách a [přihlašovací údaje úložiště v Azure Key Vault](store-credentials-in-key-vault.md) článku. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste použili propojenou službu typu "AzureStorage", je stále podporovaná tak, jak je, a když jste se rozhodli použít tento nový typ propojené služby "AzureTableStorage", který se bude přesměrovat.

**Příklad:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Příklad: klíč účtu úložiště v Azure Key Vault**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Při vytváření identifikátor URI sdíleného přístupového podpisu, zvažte následující body:

- Nastavit oprávnění odpovídající čtení a zápisu u objektů podle použití propojené služby (čtení, zápisu, čtení a zápis) ve službě data factory.
- Nastavte **čas vypršení platnosti** odpovídajícím způsobem. Ujistěte se, že přístup k objektům úložiště platnost pasu nevyprší do aktivního období kanálu.
- Identifikátor URI by měl být vytvořen na pravé úrovni tabulky podle potřeby.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. V této části najdete seznam vlastností podporovaných datovou sadou tabulky Azure.

Pokud chcete kopírovat data z tabulky Azure a z ní, nastavte vlastnost Type datové sady na **Azure**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **Azure**. |Ano |
| tableName |Název tabulky instance databáze úložiště tabulky, na kterou odkazuje propojená služba. |Ano |

**Příklad:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Pro úložiště dat bez schémat, jako je například tabulka Azure, Data Factory odvodí schéma jedním z následujících způsobů:

* Pokud zadáte mapování sloupců v aktivitě kopírování, Data Factory k načtení dat použít seznam na straně zdroje. V takovém případě, pokud řádek neobsahuje hodnotu pro sloupec, je pro něj k dispozici hodnota null.
* Pokud nezadáte mapování sloupců v aktivitě kopírování, Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě, pokud první řádek neobsahuje úplné schéma (například některé sloupce mají hodnotu null), v důsledku operace kopírování chybí některé sloupce.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. V této části najdete seznam vlastností, které podporuje zdroj a jímka tabulky Azure.

### <a name="azure-table-as-a-source-type"></a>Tabulka Azure jako typ zdroje

Pokud chcete kopírovat data z tabulky Azure, nastavte typ zdroje v aktivitě kopírování na **AzureTableSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AzureTableSource**. |Ano |
| azureTableSourceQuery |Pro čtení dat použijte dotaz vlastního tabulkového úložiště. Podívejte se na příklady v následující části. |Ne |
| azureTableSourceIgnoreTableNotFound |Určuje, zda má být povolena výjimka tabulky neexistuje.<br/>Povolené hodnoty jsou **True** a **False** (výchozí). |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery

Pokud je sloupec tabulky Azure typu DateTime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Pokud je sloupec tabulky Azure v typu řetězce:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Použijete-li parametr kanálu, přetypování hodnoty DateTime ke správnému formátu podle předchozích vzorků.

### <a name="azure-table-as-a-sink-type"></a>Tabulka Azure jako typ jímky

Pokud chcete kopírovat data do tabulky Azure, nastavte typ jímky v aktivitě kopírování na **AzureTableSink**. V části **jímka** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **AzureTableSink**. |Ano |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může jímka použít. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se jako klíč oddílu "AzureTableDefaultPartitionKeyValue". |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupce slouží jako klíč řádku. Není-li tento parametr zadán, použijte pro každý řádek identifikátor GUID. |Ne |
| azureTableInsertType |Režim pro vložení dat do tabulky Azure. Tato vlastnost určuje, zda mají být existující řádky ve výstupní tabulce se shodnými klíči oddílů a řádky nahrazeny nebo sloučeny. <br/><br/>Povolené hodnoty jsou **Sloučit** (výchozí) a **nahradit**. <br/><br> Toto nastavení platí na úrovni řádku, nikoli na úrovni tabulky. Možnost ani neodstraní řádky ve výstupní tabulce, které neexistují ve vstupu. Další informace o tom, jak nastavení sloučit a nahradit funguje, najdete v tématu věnovaném [vložení nebo sloučení entit](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení entity](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Ne |
| writeBatchSize |Když se writeBatchSize nebo writeBatchTimeout, vloží data do tabulky Azure.<br/>Povolené hodnoty jsou celé číslo (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout |Když se writeBatchSize nebo writeBatchTimeout, vloží data do tabulky Azure.<br/>Povolené hodnoty jsou TimeSpan. Příkladem je "00:20:00" (20 minut). |Ne (výchozí hodnota je 90 sekund, výchozí časový limit klienta úložiště) |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Než budete moci použít cílový sloupec jako azureTablePartitionKeyName, namapujte zdrojový sloupec na cílový sloupec pomocí vlastnosti **Translator** .

V následujícím příkladu je zdrojový sloupec DivisionID namapován na cílový sloupec DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" je zadáno jako klíč oddílu.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapování datových typů pro tabulku Azure

Když kopírujete data z a do tabulky Azure, používají se následující mapování z datových typů tabulky Azure k Data Factory dočasných datových typů. Další informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ k jímky, najdete v tématu [mapování schémat a datových typů](copy-activity-schema-and-type-mapping.md).

Když přesunete data do a z tabulky Azure, použijí se následující [mapování definovaná tabulkou Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) z tabulky Azure Table OData do typu .NET a naopak.

| Datový typ tabulky Azure | Data Factory dočasné datový typ | Podrobnosti |
|:--- |:--- |:--- |
| Edm.Binary |Byte |Pole bajtů až do 64 KB. |
| Edm.Boolean |bool |Logická hodnota |
| Edm.DateTime |Datum a čas |64 hodnota vyjádřená jako koordinovaný světový čas (UTC). Podporovaný rozsah DateTime začíná půlnocí 1. ledna 1601 (C.E.), UTC. Rozsah končí 31. prosince 9999. |
| Edm.Double |double |Hodnota 64-bit s plovoucí desetinnou čárkou. |
| Edm.Guid |Guid |Globálně jedinečný identifikátor 128. |
| Edm.Int32 |Datový typ Int32 |32 celé číslo. |
| Edm.Int64 |Int64 |64 celé číslo. |
| Edm.String |Řetězec |Hodnota kódovaná v kódování UTF-16. Hodnoty řetězce můžou být až 64 KB. |

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
