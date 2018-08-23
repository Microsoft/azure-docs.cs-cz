---
title: Kopírování dat do a z Azure Table storage pomocí služby Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z podporované zdrojové úložiště Azure Table storage nebo Table storage do jímky podporované úložišť pomocí služby Data Factory.
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
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 0399836191050996ac3eaf0fbe59496e10e2b426
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42056842"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Kopírování dat do a z Azure Table storage pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](v1/data-factory-azure-table-connector.md)
> * [Aktuální verze](connector-azure-table-storage.md)

Tento článek popisuje, jak pomocí aktivity kopírování ve službě Azure Data Factory ke zkopírování dat do a z Azure Table storage. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Kopírovat data ze všech podporovaných zdrojů úložišť dat do Table storage. Také můžete zkopírovat data z Table storage pro všechny podporovaného úložiště dat jímky. Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats) tabulky.

Konkrétně tento konektor podporuje Azure Table kopírování dat pomocí klíče účtu a služby sdílet přístup k ověření podpisu.

## <a name="get-started"></a>Začínáme

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní Table Storage.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

### <a name="use-an-account-key"></a>Použít klíč účtu

Můžete vytvořit propojenou službu Azure Storage s použitím klíče účtu. Poskytuje objekt pro vytváření dat s globálním přístupem k úložišti. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureTableStorage**. |Ano |
| připojovací řetězec | Zadejte informace potřebné pro připojení k úložišti pro vlastnost připojovací řetězec. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali "AzureStorage" typu propojené služby, je stále podporovány jako-se, když se doporučují používat tento nový "AzureTableStorage" propojené služby typu od dané chvíle.

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

### <a name="use-shared-access-signature-authentication"></a>Ověřování pomocí sdíleného přístupového podpisu

Propojenou službu Storage, můžete vytvořit také pomocí sdíleného přístupového podpisu. Poskytuje objekt pro vytváření dat s přístupem s omezením pomocí specifikátoru/časově vázaná na konkrétní/všechny prostředky ve službě storage.

Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště. Můžete ho poskytnout že klientovi omezená oprávnění k objektům v účtu úložiště pro určitou dobu a s konkrétní sadou oprávnění. Nemusíte sdílet přístupové klíče vašeho účtu. Sdílený přístupový podpis je identifikátor URI, který zahrnuje všechny informace potřebné pro ověřený přístup k prostředku úložiště v jeho parametry dotazu. Pro přístup k prostředkům úložiště pomocí sdíleného přístupového podpisu, musí klient pouze a zajistěte tak předání sdílený přístupový podpis odpovídajícího konstruktoru nebo metody. Další informace o sdílených přístupových podpisů najdete v tématu [sdílené přístupové podpisy: vysvětlení modelu sdíleného přístupového podpisu](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory teď podporuje obě **služby sdílené přístupové podpisy** a **účet sdílené přístupové podpisy**. Další informace o těchto dvou typů a jejich vytváření najdete v tématu [druhy sdílených přístupových podpisů](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). 

> [!TIP]
> Vygenerovat sdílený přístupový podpis služby pro účet úložiště, spusťte následující příkazy Powershellu. Nahraďte zástupné symboly a udělit příslušná oprávnění.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Použít sdílený přístupový podpis ověřování, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureTableStorage**. |Ano |
| sasUri | Zadejte URI sdíleného přístupového podpisu pro prostředky úložiště, jako je například objektů blob, kontejnerů nebo tabulek. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. (Pokud je vaše úložiště dat se nachází v privátní síti), můžete použít prostředí Azure Integration Runtime nebo modul Integration Runtime. Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. |Ne |

>[!NOTE]
>Pokud jste používali "AzureStorage" typu propojené služby, je stále podporovány jako-se, když se doporučují používat tento nový "AzureTableStorage" propojené služby typu od dané chvíle.

**Příklad:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
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

Při vytváření identifikátor URI sdíleného přístupového podpisu, zvažte následující body:

- Nastavit oprávnění odpovídající čtení a zápisu u objektů podle použití propojené služby (čtení, zápisu, čtení a zápis) ve službě data factory.
- Nastavte **čas vypršení platnosti** odpovídajícím způsobem. Ujistěte se, že přístup k objektům úložiště platnost pasu nevyprší do aktivního období kanálu.
- Identifikátor URI musí být vytvořené na úrovni pravé tabulky podle potřeby.

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datová sada tabulky Azure.

Pro kopírování dat do a z Azure Table, nastavte vlastnost typ datové sady na **AzureTable**. Jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **AzureTable**. |Ano |
| tableName |Název tabulky instance databáze úložiště tabulky, která odkazuje propojenou službu. |Ano |

**Příklad:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Schéma službou Data Factory

Data Factory pro úložišť dat bez schématu, jako je Azure Table, odvodí schéma v jednom z následujících způsobů:

* Pokud určíte strukturu dat pomocí **struktura** vlastnost v definici datové sady Data Factory respektuje tato struktura jako schéma. V takovém případě pokud řádku neobsahuje hodnotu pro sloupec, hodnoty null se poskytuje pro ho.
* Pokud nezadáte strukturu dat s použitím **struktura** vlastnost v definici datové sady Data Factory odvodí schéma pomocí prvního řádku v datech. V takovém případě pokud první řádek neobsahuje úplného schématu, některé sloupce promeškají ve výsledku operace kopírování.

Pro zdroje dat bez schématu, nejlepším postupem je určení strukturu dat pomocí **struktura** vlastnost.

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností, které podporuje Azure Table zdroje a jímky.

### <a name="azure-table-as-a-source-type"></a>Tabulek v Azure jako typ zdroje

Ke zkopírování dat z Azure Table, nastavte typ zdroje v aktivitě kopírování do **AzureTableSource**. Následující vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **AzureTableSource**. |Ano |
| azureTableSourceQuery |Použijte vlastní dotaz úložiště Table číst data. Příklady naleznete v následující části. |Ne |
| azureTableSourceIgnoreTableNotFound |Určuje, jestli se má povolit výjimku tabulka neexistuje.<br/>Povolené hodnoty jsou **True** a **False** (výchozí). |Ne |

### <a name="azuretablesourcequery-examples"></a>Příklady azureTableSourceQuery

Pokud je tabulka Azure sloupec typu datum a čas:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Je-li sloupec tabulky Azure typu řetězec:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Pokud použijete parametr kanálu, přetypujte hodnotu Datum a čas na správném formátu podle předchozí ukázky.

### <a name="azure-table-as-a-sink-type"></a>Tabulek v Azure jako typ jímky

Ke zkopírování dat do tabulky Azure, nastavte typ jímky v aktivitě kopírování do **AzureTableSink**. Následující vlastnosti jsou podporovány v aktivitě kopírování **jímky** oddílu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **AzureTableSink**. |Ano |
| azureTableDefaultPartitionKeyValue |Výchozí hodnota klíče oddílu, který je možné jímkou. |Ne |
| azureTablePartitionKeyName |Zadejte název sloupce, jejichž hodnoty se používají jako klíče oddílu. Pokud není zadán, "AzureTableDefaultPartitionKeyValue" slouží jako klíč oddílu. |Ne |
| azureTableRowKeyName |Zadejte název sloupce, jejichž hodnoty sloupce se používají jako klíč řádku. Pokud není zadán, použijte identifikátor GUID pro každý řádek. |Ne |
| azureTableInsertType |Režim, který chcete vložit data do tabulky Azure. Tato vlastnost určuje, zda existující řádky v tabulce výstup s odpovídajícími klíče oddílu a řádku mají jejich hodnoty, nahrazení nebo sloučení. <br/><br/>Povolené hodnoty jsou **sloučení** (výchozí) a **nahradit**. <br/><br> Toto nastavení se vztahuje na úrovni řádků nejsou úrovni tabulky. Žádná možnost odstraní řádky ve výstupní tabulce, které neexistují ve vstupu. Další informace o fungování sloučení a nahradit nastavení najdete v tématu [entity Insert nebo merge](https://msdn.microsoft.com/library/azure/hh452241.aspx) a [vložení nebo nahrazení entity](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Ne |
| WriteBatchSize |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek Azure.<br/>Povolené hodnoty jsou integer (počet řádků). |Ne (výchozí hodnota je 10 000) |
| writeBatchTimeout |Při dosažení writeBatchSize nebo writeBatchTimeout vloží data do tabulek Azure.<br/>Povolené jsou hodnoty timespan. Příkladem je "00:20:00" (20 minut). |Ne (výchozí hodnota je 90 sekund, výchozí hodnota časového limitu klienta úložiště) |

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

Zdrojový sloupec namapovat na cílový sloupec s použitím **"translator"** vlastnost, abyste mohli používat jako azureTablePartitionKeyName cílového sloupce.

V následujícím příkladu je zdrojový sloupec DivisionID namapována na cílový sloupec DivisionID:

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

Při kopírování dat z a do tabulky Azure, se používají následující mapování z Azure Table datových typů do služby Data Factory dočasné datových typů. Další informace o jak aktivita kopírování mapuje typ zdroje schéma a data jímky, najdete v článku [schéma a data zadejte mapování](copy-activity-schema-and-type-mapping.md).

Při přesunu dat do a z Azure Table, následující [mapování definovaná v tabulce Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) se používají z Azure Table OData typů na typ .NET a naopak.

| Azure typ pro tabulková data | Data Factory dočasné datový typ | Podrobnosti |
|:--- |:--- |:--- |
| Edm.Binary |Byte |Pole bajtů až 64 KB. |
| Edm.Boolean |BOOL |Logická hodnota. |
| Edm.DateTime |DateTime |Hodnota 64-bit, vyjádřené jako koordinovaný univerzální čas (UTC). V podporovaném rozsahu datum a čas zahájení půlnocí, 1, 1601. ledna (C.E.), UTC. Rozsah končí 31. prosince 9999. |
| Edm.Double |double |Bod hodnotu s plovoucí desetinnou čárkou 64bitové. |
| Edm.Guid |Guid |128bitové globálně jedinečný identifikátor. |
| Edm.Int32 |Datový typ Int32 |32bitové celé číslo. |
| Edm.Int64 |Int64 |64bitové celé číslo. |
| Edm.String |Řetězec |Hodnota kódování UTF-16. Řetězcové hodnoty můžete mít až 64 KB. |

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
