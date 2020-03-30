---
title: Kopírování dat do úložiště Azure Table a z nich
description: Zjistěte, jak kopírovat data z podporovaných zdrojových úložišť do úložiště tabulek Azure nebo z úložiště tabulek do podporovaných úložišť jímek pomocí Data Factory.
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
ms.openlocfilehash: cf79a670db4e2729c6e0a5fb7112cdc6114f465a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460701"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopírování dat do úložiště Azure Table a z nich pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-table-connector.md)
> * [Aktuální verze](connector-azure-table-storage.md)

Tento článek popisuje, jak používat aktivitu kopírování v Azure Data Factory ke kopírování dat do a z úložiště Azure Table. Vychází z článku [přehled aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor úložiště tabulky Azure je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)

Data z libovolného podporovaného zdrojového úložiště dat můžete zkopírovat do úložiště tabulky. Můžete také zkopírovat data z úložiště tabulky do libovolného úložiště dat podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivity kopírování, naleznete v tabulce [Podporovaná úložiště dat.](copy-activity-overview.md#supported-data-stores-and-formats)

Konkrétně tento konektor Azure Table podporuje kopírování dat pomocí klíče účtu a ověřování o sdílených přístupových podpisech služby.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Factory dat specifických pro úložiště tabulek.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

### <a name="use-an-account-key"></a>Použití klíče účtu

Propojenou službu Azure Storage můžete vytvořit pomocí klíče účtu. Poskytuje datové továrně globální přístup k úložišti. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureTableStorage**. |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost connectionString. <br/>Můžete také umístit klíč účtu v Azure `accountKey` Key Vault a vyžádat konfiguraci z připojovacího řetězce. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. |Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Self-hostované integrace Runtime (pokud vaše úložiště dat se nachází v privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali propojené služby typu AzureStorage, je stále podporována tak, jak je, zatímco se vám doporučuje používat tento nový typ propojené služby AzureTableStorage do budoucna.

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

**Příklad: klíč účtu úložiště v azure key vaultu**

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

### <a name="use-shared-access-signature-authentication"></a>Použití ověřování podpisu sdíleného přístupu

Můžete také vytvořit službu propojenou úložištěm pomocí sdíleného přístupového podpisu. Poskytuje datové továrny s omezeným/časově vázaný přístup ke všem/konkrétní prostředky v úložišti.

Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům v účtu úložiště. Můžete ji použít k udělení omezených oprávnění klienta objektům v účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Nemusíte sdílet přístupové klíče k účtu. Sdílený přístupový podpis je identifikátor URI, který ve svých parametrech dotazu zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště. Chcete-li získat přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, klient musí pouze předat sdílený přístupový podpis příslušnému konstruktoru nebo metodě. Další informace o sdílených přístupových podpisech naleznete v [tématu Sdílené přístupové podpisy: Principy modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory nyní podporuje jak **služby sdílené přístupové podpisy** a **podpisy sdíleného přístupu účtu**. Další informace o sdílených přístupových podpisech najdete v tématu [Udělení omezeného přístupu k prostředkům úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../storage/common/storage-sas-overview.md) 

> [!TIP]
> Chcete-li vygenerovat podpis sdíleného přístupu služby pro váš účet úložiště, můžete spustit následující příkazy prostředí PowerShell. Nahraďte zástupné symboly a udělte potřebná oprávnění.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Chcete-li použít ověřování podpisu sdíleného přístupu, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureTableStorage**. |Ano |
| sasUri | Zadejte identifikátor URI identifikátoru URI sdíleného přístupového podpisu do tabulky. <br/>Označte toto pole jako securestring bezpečně ukládat v datové továrně. Token SAS můžete také umístit do trezoru klíčů Azure Key Vault, abyste využili automatické střídání a odebrali část tokenu. Další podrobnosti najdete v následujících ukázkách a [přihlašovacích údajích úložiště v](store-credentials-in-key-vault.md) článku Azure Key Vault. | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Můžete použít Azure Integration Runtime nebo Runtime integrace s vlastním hostitelem (pokud vaše úložiště dat je umístěnv privátní síti). Pokud není zadán, používá výchozí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali propojené služby typu AzureStorage, je stále podporována tak, jak je, zatímco se vám doporučuje používat tento nový typ propojené služby AzureTableStorage do budoucna.

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

**Příklad: klíč účtu úložiště v azure key vaultu**

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

Při vytváření identifikátoru URI sdíleného přístupového podpisu zvažte následující body:

- Nastavte příslušná oprávnění pro čtení a zápis u objektů na základě toho, jak se propojená služba (čtení, zápis, čtení a zápis) používá ve vaší datové továrně.
- Nastavte **dobu expirace** odpovídajícím způsobem. Ujistěte se, že přístup k objektům úložiště nevyprší během aktivní období kanálu.
- Identifikátor URI by měl být vytvořen na správné úrovni tabulky na základě potřeby.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Azure Table.

Chcete-li zkopírovat data do a z Azure Table, nastavte vlastnost type datové sady na **AzureTable**. Podporovány jsou následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **AzureTable**. |Ano |
| tableName |Název tabulky v instanci databáze úložiště tabulky, na kterou propojená služba odkazuje. |Ano |

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

### <a name="schema-by-data-factory"></a>Schéma podle datové továrny

Pro úložiště dat bez schématu, jako je například Azure Table, Data Factory odvodí schéma jedním z následujících způsobů:

* Pokud zadáte mapování sloupců v aktivitě kopírování, data factory použít zdroj ový seznam sloupců straně načíst data. V tomto případě pokud řádek neobsahuje hodnotu pro sloupec, je pro něj k dispozici hodnota null.
* Pokud nezadáte mapování sloupců v aktivitě kopírování, Data Factory odvodí schéma pomocí prvního řádku v datech. V tomto případě pokud první řádek neobsahuje úplné schéma (například některé sloupce mají hodnotu null), některé sloupce jsou vynechány ve výsledku operace kopírování.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem tabulky Azure a jímkou.

### <a name="azure-table-as-a-source-type"></a>Azure Table jako zdrojový typ

Chcete-li zkopírovat data z Azure Table, nastavte typ zdroje v aktivitě kopírování na **AzureTableSource**. Následující vlastnosti jsou podporovány v části **zdroje aktivity** kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **AzureTableSource**. |Ano |
| azureTableSourceQuery |Ke čtení dat použijte vlastní dotaz na úložiště tabulky. Viz příklady v následující části. |Ne |
| azureTableSourceIgnoreTableNotFound |Označuje, zda má být výjimka tabulky neexistující.<br/>Povolené hodnoty jsou **True** a **False** (výchozí). |Ne |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery příklady

>[!NOTE]
>Vypršení limitu operace dotazu azure table za 30 sekund, jak [je vynuceno službou Azure Table](https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations). Přečtěte si, jak optimalizovat dotaz z [článku Návrh pro dotazování.](../storage/tables/table-storage-design-for-query.md)

Pokud chcete v Azure Data Factory filtrovat data podle sloupce typu datetime, podívejte se na tento příklad:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Pokud chcete filtrovat data podle sloupce typu řetězce, podívejte se na tento příklad:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Pokud použijete parametr pipeline, přetypujte hodnotu datetime do správného formátu podle předchozích ukázek.

### <a name="azure-table-as-a-sink-type"></a>Azure Table jako typ jímky

Chcete-li zkopírovat data do Azure Table, nastavte typ jímky v aktivitě kopírování do **AzureTableSink**. Následující vlastnosti jsou podporovány v části **jímky** aktivity kopírování.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **AzureTableSink**. |Ano |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, kterou může použít jímka. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jehož hodnoty se používají jako klíče oddílu. Pokud není zadán, "AzureTableDefaultPartitionKeyValue" se používá jako klíč oddílu. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jehož hodnoty sloupců se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Ne |
| azureTableInsertType |Režim pro vložení dat do Azure Table. Tato vlastnost určuje, zda existující řádky ve výstupní tabulce s odpovídajícími klíči oddílu a řádků mají své hodnoty nahrazeny nebo sloučeny. <br/><br/>Povolené hodnoty jsou **slučovány** (výchozí) a **nahrazují**. <br/><br> Toto nastavení platí na úrovni řádku, nikoli na úrovni tabulky. Ani jedna z možností neodstraní řádky ve výstupní tabulce, které ve vstupu neexistují. Informace o tom, jak funguje sloučení a nahrazení nastavení, naleznete v [tématu Vložení nebo sloučení entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a Vložení nebo nahrazení [entity](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Ne |
| writeBatchSize |Vloží data do Azure Table při zápisuBatchSize nebo writeBatchTimeout je přístupů.<br/>Povolené hodnoty jsou celé číslo (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout |Vloží data do Azure Table při zápisuBatchSize nebo writeBatchTimeout je přístupů.<br/>Povolené hodnoty jsou časový mj. Příkladem je "00:20:00" (20 minut). |Ne (výchozí hodnota je 90 sekund, výchozí časový limit klienta úložiště) |

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

Namapujte zdrojový sloupec na cílový sloupec pomocí vlastnosti **"translator"** předtím, než můžete použít cílový sloupec jako azureTablePartitionKeyName.

V následujícím příkladu je zdrojový sloupec DivisionID mapován na cílový sloupec DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

Jako klíč oddílu je zadáno "DivisionID".

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapování datových typů pro tabulku Azure

Když kopírujete data z a do Azure Table, následující mapování se používají z datových typů Azure Table do dočasných datových typů Data Factory. Informace o tom, jak aktivita kopírování mapuje zdrojové schéma a datový typ do jímky, naleznete v [tématu Schémata a mapování datových typů](copy-activity-schema-and-type-mapping.md).

Při přesunutí dat do a z Azure Table se následující [mapování definovaná Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) používají z typů Azure Table OData na typ .NET a naopak.

| Datový typ tabulky Azure | Dočasný datový typ datové továrny | Podrobnosti |
|:--- |:--- |:--- |
| Edm.Binary |bajt[] |Pole bajtů až 64 kB. |
| Edm.Boolean |bool |Logická hodnota |
| Edm.DateČas |DateTime |64bitová hodnota vyjádřená jako koordinovaný světový čas (UTC). Podporovaný rozsah DateTime začíná půlnoc, 1 leden 1601 AD. (C.E.), UTC. Rozsah končí 31.prosince 9999. |
| Edm.Double |double |Hodnota s plovoucí desetinnou hodnotou 64 bitů. |
| Edm.Guid |Identifikátor GUID |128bitový globálně jedinečný identifikátor. |
| Edm.Int32 |Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódovaná UTF-16. Řetězcové hodnoty mohou být až 64 kB. |

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
