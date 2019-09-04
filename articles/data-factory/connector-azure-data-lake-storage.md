---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Data Factory | Microsoft Docs
description: Naučte se, jak kopírovat data do Azure Data Lake Storage Gen2 a z nich pomocí Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: e21ae2f8eda4521effa5b7db686fe72241aa4cdb
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276285"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen2 pomocí Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) je sada funkcí vyhrazených pro analýzy velkých objemů dat integrované do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít k rozhraní s daty pomocí paradigma systému souborů i úložiště objektů.

Tento článek popisuje, jak kopírovat data do a z Azure Data Lake Storage Gen2. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Data Lake Storage Gen2 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovaným zdrojem nebo maticí jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)

Konkrétně pomocí tohoto konektoru můžete:

- Zkopírujte data pomocí klíče účtu, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Zkopírujte soubory tak, jak jsou, nebo Analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

>[!TIP]
>Pokud povolíte hierarchický obor názvů, v současné době nedochází k interoperabilitě operací mezi objekty BLOB a Data Lake Storage Gen2mi rozhraními API. Pokud jste narazili na chybu "ErrorCode = FilesystemNotFound" se zprávou "zadaný systém souborů neexistuje", je to způsobeno zadaným systémem souborů jímky, který byl vytvořen prostřednictvím rozhraní BLOB API namísto Data Lake Storage Gen2 rozhraní API jinde. Problém vyřešíte tak, že zadáte nový systém souborů s názvem, který neexistuje jako název kontejneru objektů BLOB. Pak Data Factory automaticky vytvoří tento systém souborů při kopírování dat.

>[!NOTE]
>Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** v Azure Storage nastavení brány firewall, prostředí Azure Integration runtime se nepřipojí k Data Lake Storage Gen2 a zobrazí se chyba zakázáno. Chybová zpráva se zobrazí, protože Data Factory není považována za důvěryhodnou službu Microsoftu. Místo toho se připojte pomocí místního prostředí Integration runtime.

## <a name="get-started"></a>Začínáme

>[!TIP]
>Návod, jak používat konektor Data Lake Storage Gen2, najdete v tématu [načtení dat do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor Azure Data Lake Storage Gen2 podporuje následující typy ověřování. Podrobnosti najdete v odpovídajících částech:

- [Ověření pomocí klíče účtu](#account-key-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravovaných identit pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Když použijete základnu k načtení dat do SQL Data Warehouse, pokud je vaše zdrojová Data Lake Storage Gen2 nakonfigurovaná pomocí Virtual Networkho koncového bodu, musíte použít spravované ověřování identity podle požadavků základu. Viz část [spravované ověřování identity](#managed-identity) s dalšími požadavky na konfiguraci.

### <a name="account-key-authentication"></a>Ověření pomocí klíče účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| accountKey | Klíč účtu pro Data Lake Storage Gen2. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory nebo [odkazovat tajného klíče do služby Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tato vlastnost není zadaná, použije se výchozí prostředí Azure Integration runtime. |Ne |

>[!NOTE]
>Sekundární koncový bod ADLS systému souborů není při ověřování pomocí klíče účtu podporován. Můžete použít jiné typy ověřování.

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Ověřování instančních objektů

Chcete-li použít ověřování instančního objektu, postupujte podle těchto kroků.

1. Pomocí postupu v části [Registrace aplikace v Tenantovi Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)Zaregistrujte entitu aplikace v Azure Active Directory (Azure AD). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění. Další informace o tom, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako zdroj**: V Průzkumník služby Storage Udělte alespoň oprávnění ke **spuštění** ze zdrojového systému souborů společně s oprávněním **ke čtení** pro soubory ke zkopírování. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: V Průzkumník služby Storage udělte aspoň oprávnění **Execute** počínaje systémem souborů jímky společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Chcete-li vypsat složky od úrovně účtu nebo test připojení, musíte nastavit oprávnění instančního objektu, který je udělen **účtu úložiště, pomocí oprávnění "čtečka dat objektů BLOB úložiště" v systému IAM**. To platí v případě, že použijete:
>- **Nástroj pro kopírování dat** umožňuje vytvořit kanál pro kopírování.
>- **Data Factory uživatelské rozhraní** pro otestování připojení a procházení složek během vytváření obsahu. 
>Pokud máte obavy o udělení oprávnění na úrovni účtu, během vytváření, vynechání testování připojení a zadání nadřazené cesty s uděleným oprávněním a pak zvolte možnost Procházet z této zadané cesty. Aktivita kopírování funguje, pokud je instančnímu objektu uděleno správné oprávnění v kopírovaných souborech.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` pro bezpečné uložení v Data Factory. Nebo můžete [odkazovat na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ne |

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Spravovaných identit pro ověřování prostředků Azure

Objekt pro vytváření dat můžou být spojené s [spravované identity pro prostředky Azure](data-factory-service-identity.md), která představuje této konkrétní datové továrně. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Storage Gen2, podobně jako při používání vlastního instančního objektu. Umožňuje této určené továrně přístup k datům a jejich kopírování z Data Lake Storage Gen2.

Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků.

1. [Načtěte data Factory informace o spravované identitě](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty **ID aplikace identity služby** generované společně s vaší továrnou.

2. Udělte spravované identitě správné oprávnění. Další informace o tom, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako zdroj**: V Průzkumník služby Storage Udělte alespoň oprávnění ke **spuštění** ze zdrojového systému souborů společně s oprávněním **ke čtení** pro soubory ke zkopírování. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: V Průzkumník služby Storage udělte aspoň oprávnění **Execute** počínaje systémem souborů jímky společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Chcete-li vypsat složky od úrovně účtu nebo test připojení, musíte nastavit oprávnění spravované identity **účtu úložiště s oprávněním "čtečka dat objektů BLOB úložiště" v systému IAM**. To platí v případě, že použijete:
>- **Nástroj pro kopírování dat** umožňuje vytvořit kanál pro kopírování.
>- **Data Factory uživatelské rozhraní** pro otestování připojení a procházení složek během vytváření obsahu. 
>Pokud máte obavy o udělení oprávnění na úrovni účtu, během vytváření, vynechání testování připojení a zadání nadřazené cesty s uděleným oprávněním a pak zvolte možnost Procházet z této zadané cesty. Aktivita kopírování funguje, pokud je instančnímu objektu uděleno správné oprávnění v kopírovaných souborech.

>[!IMPORTANT]
>Pokud použijete základ k načtení dat z Data Lake Storage Gen2 do SQL Data Warehouse při použití spravovaného ověřování identity pro Data Lake Storage Gen2, nezapomeňte také postupovat podle kroků 1 a 2 v [tomto návodu](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) na 1) zaregistrovat SQL Database Server s Azure Active Directory (Azure AD) a 2) přiřaďte roli Přispěvatel dat objektů BLOB úložiště k vašemu serveru SQL Database; zbytek se zpracovává pomocí Data Factory. Pokud je váš Data Lake Storage Gen2 nakonfigurovaný s koncovým bodem Azure Virtual Network, aby se k načtení dat z něj používala základna, musíte použít spravované ověřování identity podle požadavků základu.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ne |

**Příklad:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v tématu [datové sady](concepts-datasets-linked-services.md).

- Pro **Parquet, oddělený text, Avro a binární formát**, přečtěte si část [Parquet, text a datovou sadu binárního formátu s oddělovači](#format-based-dataset) .
- Pro jiné formáty, jako je **Formát ORC/JSON**, se podívejte na [jiný oddíl formátu DataSet](#other-format-dataset) .

### <a name="format-based-dataset"></a>Datová sada Parquet, oddělený text, Avro a binární formát

Chcete-li kopírovat data do a z **Parquet, oddělený text, Avro nebo binární formát**, přečtěte si článek [Parquet Format](format-parquet.md), formated [Text Format](format-delimited-text.md), [Avro Format](format-avro.md) a [Binary Format](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. Následující vlastnosti jsou podporovány pro data Lake Storage Gen2 v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci `location` datové sady musí být nastavená na **AzureBlobFSLocation**. | Ano      |
| Systému souborů | Název systému souborů Data Lake Storage Gen2.                              | Ne       |
| folderPath | Cesta ke složce v daném systému souborů. Pokud chcete použít zástupný znak pro filtrování složek, toto nastavení nechte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným systémem souborů + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho do nastavení zdroje aktivity. | Ne       |

> [!NOTE]
> Datová sada typu **AzureBlobFSFile** s Parquet nebo textem formátu uvedená v následující části je stále podporovaná tak, jak je to pro účely zpětné kompatibility kopírování, vyhledávání nebo GetMetadata. Ale nefunguje s funkcí toku dat mapování. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Jiná Formátová datová sada

Chcete-li kopírovat data do a z Data Lake Storage Gen2 ve **formátu ORC/JSON**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady, musí být nastavená na **AzureBlobFSFile**. |Ano |
| folderPath | Cesta ke složce v Data Lake Storage Gen2. Pokud není zadán, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Pokud `^` má váš vlastní název složky zástupný znak nebo je tento řídicí znak uvnitř, použijte k tomu řídicí znak. <br/><br/>Příklady: systém souborů/složka/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>V případě filtru je povolených `*` zástupných znaků (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použijte `^` k ukončení, jestli má skutečný název souboru zástupný znak nebo že se tento řídicí znak nachází uvnitř.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadané **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*Data. [identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Pokud zkopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| Ne |
| format | Pokud chcete zkopírovat soubory, jako je mezi souborové úložiště (binární kopie), přejděte v části formát v definici vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte **typ** vlastnosti v části **formátu** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně **Optimal** a **nejrychlejší**. |Ne |

>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

**Příklad:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [Konfigurace aktivit kopírování](copy-activity-overview.md#configuration) a [kanály a aktivity](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných službou Data Lake Storage Gen2 zdroje a jímky.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ zdroje

- Pokud chcete kopírovat z **Parquet, oddělený text, Avro a binární formát**, přečtěte si oddíl [Parquet, text s oddělovači a zdrojový soubor binárního formátu](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je **Formát ORC/JSON**, informace naleznete v části [Další zdrojový formát](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, oddělený text, Avro a zdroj binárního formátu

Chcete-li kopírovat data z **Parquet, oddělený text, Avro nebo binární formát**, přečtěte si článek [Formát Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), [Formát Avro](format-avro.md) a [binární formát](format-binary.md) pro zdroj aktivity kopírování založené na formátu a podporovaná nastavení. . Následující vlastnosti jsou podporovány pro data Lake Storage Gen2 v části `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureBlobFSReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v rámci daného systému souborů nakonfigurovaného v sadě dat pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Použijte `^` k ukončení, jestli vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v daném systému souborů + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Použijte `^` k ukončení, jestli vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, `fileName` Pokud není v datové sadě určeno |
| modifiedDatetimeStart    | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` má hodnotu null, znamená, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Když `modifiedDatetimeEnd` má hodnotu DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než hodnota DateTime. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro formát Parquet nebo oddělený text je zdroj aktivity kopírování typu **AzureBlobFSSource** uvedený v následující části stále podporován, jak je z důvodu zpětné kompatibility. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Jiný zdroj formátu

Chcete-li kopírovat data z Data Lake Storage Gen2 ve **formátu ORC/JSON**, v části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavená na **AzureBlobFSSource**. |Ano |
| recursive | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 jako typ jímky

- Pokud chcete kopírovat do **Parquet, oddělený text, Avro nebo binární formát**, přečtěte si oddíl [Parquet, text s oddělovači a binární formát](#format-based-sink) .
- Chcete-li kopírovat do jiných formátů, jako je například **Formát ORC/JSON**, přečtěte si [Další část formátování jímky](#other-format-sink) .

#### <a name="format-based-sink"></a>Jímka Parquet, s oddělovači textu, Avro a binárního formátu

Pokud chcete kopírovat data na **Parquet, oddělený text, Avro nebo binární formát**, přečtěte si článek [Parquet Format](format-parquet.md), formated [Text Format](format-delimited-text.md), [Avro Format](format-avro.md) a [Binary Format](format-binary.md) pro jímku aktivity kopírování založené na formátu a podporovaná nastavení. Následující vlastnosti jsou podporovány pro data Lake Storage Gen2 v části `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureBlobFSWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

> [!NOTE]
> Pro formát Parquet nebo oddělený text je jímka aktivity kopírování typu **AzureBlobFSSink** uvedená v následující části stále podporovaná, jak je z důvodu zpětné kompatibility. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Další jímka formátu

Chcete-li kopírovat data do Data Lake Storage Gen2 ve **formátu ORC/JSON**, v oddílu **jímky** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Nastavte vlastnost typ jímky aktivity kopírování **AzureBlobFSSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdné, použít výchozí) | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Prázdné, použít výchozí) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 File2 + soubor3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 obsah jsou sloučeny do jednoho souboru s názvem automaticky vygenerovaný soubor. automaticky generovaným názvem File1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat seznamy ACL z Data Lake Storage Gen1

>[!TIP]
>Pokud chcete kopírovat data z Azure Data Lake Storage Gen1 do Gen2 obecně, přečtěte si téma [kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pro návod a osvědčené postupy.

Když kopírujete soubory z Azure Data Lake Storage Gen1 do Gen2, můžete se rozhodnout zachovat seznamy řízení přístupu (ACL) POSIX spolu s daty. Další informace o řízení přístupu najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) a [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Následující typy seznamů ACL lze zachovávají pomocí aktivity kopírování Azure Data Factory. Můžete vybrat jeden nebo více typů:

- **SEZNAM ACL**: Zkopírujte a zachovejte seznamy řízení přístupu POSIX pro soubory a adresáře. Zkopíruje všechny existující seznamy ACL ze zdroje do jímky. 
- **Vlastník**: Zkopírujte a zachovejte vlastnícího uživatele souborů a adresářů. Vyžaduje se přístup super-uživatel k Data Lake Storage Gen2 jímky.
- **Skupina**: Zkopírujte a zachovejte vlastnící skupinu souborů a adresářů. Je vyžadován přístup super uživatele k jímky Data Lake Storage Gen2 nebo vlastnícímu uživateli (Pokud je vlastnící uživatel také členem cílové skupiny).

Pokud určíte, že se má kopírovat ze složky, Data Factory replikuje seznamy ACL pro danou složku a soubory a adresáře pod ním, pokud `recursive` je nastaven na hodnotu true. Pokud určíte, že se mají kopírovat z jednoho souboru, seznamy ACL tohoto souboru se zkopírují.

>[!IMPORTANT]
>Pokud se rozhodnete zachovat seznamy řízení přístupu (ACL), ujistěte se, že udělíte dostatečná oprávnění, aby Data Factory fungovala s Data Lake Storage Gen2m účtem jímky. Použijte například ověřování pomocí klíče účtu nebo přiřaďte roli vlastníka dat objektu BLOB úložiště k instančnímu objektu nebo spravované identitě.

Když nakonfigurujete zdroj jako Data Lake Storage Gen1 pomocí možnosti binárního kopírování nebo binárního formátu a jímky jako Data Lake Storage Gen2 možnosti binárního kopírování nebo binárního formátu, můžete najít možnost **zachovat** na stránce **nastavení nástroje kopírování dat** nebo na Karta**Nastavení** **aktivity** > kopírování pro vytváření aktivit

![Data Lake Storage Gen1 Gen2 zachovat seznam ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Tady je příklad konfigurace JSON (viz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si další informace o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v funkci toku dat mapování.

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
