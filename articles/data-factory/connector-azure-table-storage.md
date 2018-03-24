---
title: Kopírování dat do a z Azure Table storage pomocí služby Data Factory | Microsoft Docs
description: Zjistěte, jak zkopírovat data z podporované zdrojové úložiště Azure Table storage, nebo z tabulky úložiště do úložiště podporované podřízený pomocí služby Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: ef43037ff33b693256c82459eec2e4b3beab4d9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopírování dat do a z Azure Table storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – Obecně dostupná](v1/data-factory-azure-table-connector.md)
> * [Verze 2 – Preview](connector-azure-table-storage.md)

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory ke zkopírování dat do a z Azure Table storage. Vychází [aktivity kopírování přehled](copy-activity-overview.md) článek, který představuje obecný přehled aktivity kopírování.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 objektu pro vytváření dat, která je obecně k dispozici, najdete v části [konektor úložiště tabulky v verze 1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Data můžete zkopírovat z jakékoli úložiště podporované zdroje dat do úložiště tabulek. Také můžete zkopírovat data z úložiště tabulek do úložiště dat žádné podporované jímky. Seznam úložišť dat, které jsou podporovány jako zdroje nebo jímky aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor podporuje tabulky Azure kopírování dat pomocí klíč účtu a služby sdílet přístup k ověření podpisu.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnosti, které slouží k určení entit služby Data Factory konkrétní Table Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

### <a name="use-an-account-key"></a>Použít klíč účtu

Propojenou službu úložiště Azure můžete vytvořit pomocí klíč účtu. Poskytuje objekt pro vytváření dat s globálním přístupem k úložišti. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **azurestorage**. |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost connectionString. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít modul Runtime integrace Azure nebo Self-hosted integrace Runtime. Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-service-shared-access-signature-authentication"></a>Pomocí služby ověřování podpisu sdíleného přístupu

Propojenou službu úložiště můžete vytvořit také pomocí sdíleného přístupového podpisu. Poskytuje objekt pro vytváření dat omezený nebo časově vázaných přístup k prostředkům všechna nebo konkrétní v úložišti.

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Můžete ho udělit, že klient omezené oprávnění k objektům v účtu úložiště po určitou dobu a se zadanou sadou oprávnění. Nemáte sdílet klíče pro přístup k účtu. Sdílený přístupový podpis je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště s sdílený přístupový podpis, klient pouze musí předat sdílený přístupový podpis metodu, nebo odpovídající konstruktor. Další informace o sdílených přístupových podpisů najdete v tématu [sdílené přístupové podpisy: pochopení modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Objekt pro vytváření dat se teď podporuje pouze služby sdílené přístupové podpisy ale není účet sdílené přístupové podpisy. Další informace o těchto dvou typů a konstruování je najdete v tématu [druhy sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). Sdílený přístupový podpis adresa URL vygenerovat z portálu Azure nebo Azure Storage Explorer je sdílený přístupový podpis účet, který není podporován.

> [!TIP]
> Můžete spustit následující příkazy prostředí PowerShell vygenerovat sdílený přístupový podpis služby pro účet úložiště. Nahraďte zástupné symboly a udělení potřebných oprávnění.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Pokud chcete používat ověřování podpisu sdíleného přístupu služby, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu musí být nastavená na **azurestorage**. |Ano |
| sasUri | Zadejte sdílený přístupový podpis URI k prostředkům úložiště, například objekt blob, kontejneru nebo tabulky. Toto pole označit jako SecureString bezpečně uložit v datové továrně nebo [odkazovat tajného klíče uložené v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrace runtime](concepts-integration-runtime.md) který se má použít pro připojení k úložišti. Můžete použít modul Runtime integrace Azure nebo Runtime integrace Self-hosted (Pokud je vaše úložiště dat se nachází v privátní síti). Pokud není zadaný, použije výchozí Runtime integrace Azure. |Ne |

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Když vytvoříte sdílený přístupový podpis identifikátor URI, zvažte následující:

- Nastavit oprávnění vhodné pro čtení a zápis na objekty podle použití propojené služby (pro čtení, zápisu, čtení/zápis) ve službě data factory.
- Nastavit **čas vypršení platnosti** správně. Ujistěte se, že nemá přístup k objektům úložiště vyprší do aktivního období kanálu.
- Identifikátor URI by se vytvořit na úrovni pravé tabulky, podle potřeby.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datové sady](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure Table datovou sadu.

Ke zkopírování dat do a z Azure Table, nastavte vlastnost typu datové sady, která **AzureTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typu datové sady musí být nastavena na **AzureTable**. |Ano |
| tableName |Název tabulky instance databáze úložiště tabulky, který propojená služba odkazuje na. |Ano |

**Příklad:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Objekt pro vytváření dat pro úložiště dat bez schémat jako je například Azure Table, odvodí schéma v jednom z následujících způsobů:

* Pokud zadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, Data Factory ctí tato struktura jako schéma. V takovém případě Pokud řádek neobsahuje hodnotu pro sloupec, je pro něj zadat hodnotu null.
* Pokud nezadáte strukturu dat pomocí **struktura** vlastnost v definici datové sady, Data Factory odvodí, že schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplnou schéma, jsou vynechalo některé sloupce ve výsledku operace kopírování.

Pro zdroje dat bez schémat, osvědčeným postupem je určení strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure Table zdroj a jímka.

### <a name="azure-table-as-a-source-type"></a>Tabulky Azure jako typ zdroje

Ke zkopírování dat z Azure Table, nastavte typ zdroje v aktivitě kopírování do **AzureTableSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ zdroje kopie aktivity musí být nastavena na **AzureTableSource**. |Ano |
| azureTableSourceQuery |Použijte vlastní dotaz úložiště tabulky číst data. Příklady v následující části. |Ne |
| azureTableSourceIgnoreTableNotFound |Označuje, zda povolit výjimku tabulky na neexistuje.<br/>Povolené hodnoty jsou **True** a **False** (výchozí). |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery

Pokud je tabulka Azure sloupec typu datum a čas:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Pokud sloupec tabulky Azure je typu řetězec:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Pokud použijete parametr kanálu, přetypování hodnota data a času, která má správný formát podle předchozích ukázky.

### <a name="azure-table-as-a-sink-type"></a>Tabulky Azure jako typ jímky

Ke zkopírování dat do Azure Table, nastavte typ jímky v aktivitě kopírování do **AzureTableSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **podřízený** části.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ jímky aktivity kopírování musíte nastavit **AzureTableSink**. |Ano |
| azureTableDefaultPartitionKeyValue |Výchozí oddílu klíče hodnota, která mohou být využívána jímky. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílů. Pokud není zadaný, použije se jako klíč oddílu "AzureTableDefaultPartitionKeyValue". |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce jsou použity jako klíč řádku. Pokud není zadaný, použijte identifikátor GUID pro každý řádek. |Ne |
| azureTableInsertType |Režim vložení dat do tabulky Azure. Tato vlastnost určuje, jestli mají existující řádky v tabulce output s odpovídajícím klíče oddílu a řádku jejich hodnoty nahradit nebo sloučit. <br/><br/>Povolené hodnoty jsou **sloučení** (výchozí) a **nahradit**. <br/><br> Toto nastavení se vztahuje na úrovni řádků není úrovni tabulky. Žádná možnost odstraní řádků do výstupní tabulky, které nejsou k dispozici ve vstupu. Další informace o fungování sloučení a nahradit nastavení najdete v tématu [Insert nebo merge entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení entity](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Ne |
| writeBatchSize |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do Azure Table.<br/>Povolené hodnoty jsou celé číslo (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout |Když je dosaženo writeBatchSize nebo writeBatchTimeout vkládá data do Azure Table.<br/>Povolené hodnoty jsou časový interval. Je například "00:20:00" (20 minut). |Ne (výchozí hodnota je 90 sekund, výchozí hodnota časového limitu klienta úložiště) |

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

Zdrojový sloupec namapovat na cílový sloupec s použitím **"překladač"** vlastnost, abyste mohli používat jako azureTablePartitionKeyName cílový sloupec.

V následujícím příkladu je zdrojový sloupec DivisionID namapovaný na cílový sloupec DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID" je zadán jako klíč oddílu.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Mapování datového typu pro Azure Table

Při kopírování dat z a do tabulky Azure, se používají následující mapování z Azure Table datové typy k objektu pro vytváření dat dočasné datové typy. Další informace o aktivitě kopírování jak mapuje typ zdroje schéma a data na jímky najdete v tématu [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

Když přesun dat do a z Azure Table, následující [mapování definované Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) se používají ve typy OData tabulky Azure na typ .NET a naopak.

| Azure Table datový typ | Typ průběžných dat objektu pro vytváření dat | Podrobnosti |
|:--- |:--- |:--- |
| Edm.Binary |Byte |Pole bajtů až 64 KB. |
| Edm.Boolean |BOOL |Logická hodnota. |
| Edm.DateTime |DateTime |Hodnota 64-bit, vyjádřené jako koordinovaný světový čas (UTC). Podporovaný rozsah data a času zahájení půlnoc, 1 leden roku 1601. (C.E.), UTC. Rozsah končí 31. prosince 9999. |
| Edm.Double |Double |Hodnota 64-bit plovoucí bodu. |
| Edm.Guid |Guid |Globálně jedinečný identifikátor 128-bit. |
| Edm.Int32 |Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódování UTF-16. Řetězcové hodnoty může mít až 64 KB. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat jako zdroje a jímky nepodporuje aktivitu kopírování v objektu pro vytváření dat najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
