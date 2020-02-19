---
title: Kopírování a transformace dat v Azure Data Lake Storage Gen2
description: Naučte se, jak kopírovat data do a z Azure Data Lake Storage Gen2 a transformovat data v Azure Data Lake Storage Gen2 pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: 2f147890887d5eb9dd1b2681bd09c662c14c74ff
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77431074"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Data Lake Storage Gen2 pomocí Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) je sada funkcí vyhrazených pro analýzy velkých objemů dat integrované do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít k rozhraní s daty pomocí paradigma systému souborů i úložiště objektů.

V tomto článku se naučíte, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z a do Azure Data Lake Storage Gen2 a jak transformovat data v Azure Data Lake Storage Gen2 pomocí toku dat. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

Tento konektor Azure Data Lake Storage Gen2 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Pro aktivitu kopírování můžete pomocí tohoto konektoru:

- Kopírování dat z/do Azure Data Lake Storage Gen2 pomocí klíče účtu, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Zkopírujte soubory tak, jak jsou, nebo Analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Při kopírování zachovat metadata souboru](#preserve-metadata-during-copy).
- Při kopírování z Azure Data Lake Storage Gen1 [zachovat seznamy ACL](#preserve-metadata-during-copy) .

>[!IMPORTANT]
>Pokud povolíte možnost **Povolit důvěryhodným službám Microsoftu přístup k tomuto účtu úložiště** při Azure Storage nastavení brány firewall a chcete použít prostředí Azure Integration runtime pro připojení k Data Lake Storage Gen2, musíte pro adls Gen2 použít [spravované ověřování identity](#managed-identity) .

>[!TIP]
>Pokud povolíte hierarchický obor názvů, v současné době nedochází k interoperabilitě operací mezi objekty BLOB a Data Lake Storage Gen2mi rozhraními API. Pokud jste narazili na chybu "ErrorCode = FilesystemNotFound" se zprávou "zadaný systém souborů neexistuje", je to způsobeno zadaným systémem souborů jímky, který byl vytvořen prostřednictvím rozhraní BLOB API namísto Data Lake Storage Gen2 rozhraní API jinde. Problém vyřešíte tak, že zadáte nový systém souborů s názvem, který neexistuje jako název kontejneru objektů BLOB. Pak Data Factory automaticky vytvoří tento systém souborů při kopírování dat.

## <a name="get-started"></a>Začínáme

>[!TIP]
>Návod, jak používat konektor Data Lake Storage Gen2, najdete v tématu [načtení dat do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Konektor Azure Data Lake Storage Gen2 podporuje následující typy ověřování. Podrobnosti najdete v odpovídajících částech:

- [Ověřování klíčů účtu](#account-key-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Když použijete základnu k načtení dat do SQL Data Warehouse, pokud je vaše zdrojová Data Lake Storage Gen2 nakonfigurovaná pomocí Virtual Networkho koncového bodu, musíte použít spravované ověřování identity podle požadavků základu. Viz část [spravované ověřování identity](#managed-identity) s dalšími požadavky na konfiguraci.

### <a name="account-key-authentication"></a>Ověření pomocí klíče účtu

Pokud chcete použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| accountKey | Klíč účtu pro Data Lake Storage Gen2. Označte toto pole jako SecureString, abyste ho bezpečně ukládali do Data Factory nebo [odkazovali na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tato vlastnost není zadaná, použije se výchozí prostředí Azure Integration runtime. |Ne |

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

2. Udělte instančnímu objektu správné oprávnění. Podívejte se na příklady, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) .

    - **Jako zdroj**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním **ke čtení** pro kopírování souborů. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Používáte-li k vytváření Data Factory uživatelské rozhraní a instanční objekt není nastaven s rolí "čtecí modul dat objektů BLOB a Přispěvatel" v nástroji IAM, při provádění testovacího připojení nebo procházení/procházení složek zvolte možnost "testovat připojení k cestě k souboru" nebo "Procházet ze zadané cesty" a zadejte cestu s oprávněním **číst + spustit** , aby bylo možné pokračovat.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` pro bezpečné uložení v Data Factory. Nebo můžete [odkazovat na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi (domény ID tenanta nebo název) v rámci které se nachází vaše aplikace. Načtěte ho tak, že najedete myší v pravém horním rohu Azure Portal. | Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ne |

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

### <a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě prostředků Azure](data-factory-service-identity.md), která představuje tento konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Storage Gen2, podobně jako při používání vlastního instančního objektu. Umožňuje této určené továrně přístup k datům a jejich kopírování z Data Lake Storage Gen2.

Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků.

1. [Načtěte data Factory informace o spravované identitě](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty **ID spravovaného objektu identity** generovaného společně s vaší továrnou.

2. Udělte spravované identitě správné oprávnění. Podívejte se na příklady, jak oprávnění funguje v Data Lake Storage Gen2 ze [seznamů řízení přístupu u souborů a adresářů](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako zdroj**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním **ke čtení** pro kopírování souborů. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **čtečky dat objektů BLOB úložiště** .
    - **Jako jímka**: v Průzkumník služby Storage udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám a systému souborů společně s oprávněním k **zápisu** do složky jímky. Případně můžete v řízení přístupu (IAM) udělit alespoň roli **Přispěvatel dat objektu BLOB úložiště** .

>[!NOTE]
>Pokud použijete Data Factory uživatelské rozhraní k vytváření a spravovaná identita není nastavena pomocí role čtenář/Přispěvatel dat objektů BLOB úložiště v nástroji IAM, při provádění testovacího připojení nebo procházení/procházení složek zvolte možnost Test připojení k cestě k souboru nebo procházet ze zadané cesty a zadejte cestu s oprávněním **číst + spustit** , aby bylo možné pokračovat.

>[!IMPORTANT]
>Pokud použijete základ k načtení dat z Data Lake Storage Gen2 do SQL Data Warehouse při použití spravovaného ověřování identity pro Data Lake Storage Gen2, nezapomeňte také postupovat podle kroků 1 a 2 v [těchto pokynech](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) k 1) registraci serveru SQL Database pomocí Azure Active Directory (Azure AD) a 2) přiřazení role Přispěvatel dat objektů BLOB úložiště k vašemu SQL Database serveru; zbytek se zpracovává pomocí Data Factory. Pokud je váš Data Lake Storage Gen2 nakonfigurovaný s koncovým bodem Azure Virtual Network, aby se k načtení dat z něj používala základna, musíte použít spravované ověřování identity podle požadavků základu.

Tyto vlastnosti jsou pro propojenou službu podporované:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type musí být nastavená na **AzureBlobFS**. |Ano |
| url | Koncový bod pro Data Lake Storage Gen2 se vzorem `https://<accountname>.dfs.core.windows.net`. | Ano |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud je vaše úložiště dat v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tento parametr nezadáte, použije se výchozí prostředí Azure Integration runtime. |Ne |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **AzureBlobFSLocation**. | Ano      |
| Systému souborů | Název systému souborů Data Lake Storage Gen2.                              | Ne       |
| folderPath | Cesta ke složce v daném systému souborů. Pokud chcete použít zástupný znak pro filtrování složek, toto nastavení nechte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným systémem souborů + folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho do nastavení zdroje aktivity. | Ne       |

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

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [Konfigurace aktivit kopírování](copy-activity-overview.md#configuration) a [kanály a aktivity](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných službou Data Lake Storage Gen2 zdroje a jímky.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části nastavení `storeSettings` ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureBlobFSReadSettings**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky v rámci daného systému souborů nakonfigurovaného v sadě dat pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud vlastní název složky obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v daném systému souborů + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud vlastní název složky obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v DataSet není zadána `fileName` |
| modifiedDatetimeStart    | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit žádný filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 jako typ jímky

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Data Lake Storage Gen2 v části `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureBlobFSWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| blockSizeInMB | Určete velikost bloku v MB používaného k zápisu dat do ADLS Gen2. Přečtěte si další informace o objektech [blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Povolená hodnota je **mezi 4 a 100 MB**. <br/>Ve výchozím nastavení ADF automaticky určí velikost bloku na základě typu zdrojového úložiště a dat. Pro nebinární kopírování do ADLS Gen2 je výchozí velikost bloku 100 MB, takže se vejde do maximálně 4,95 TB dat. Nemusí být optimální, pokud vaše data nejsou velká, zejména pokud používáte Integration Runtime v místním prostředí s nízkou sítí, která má za následek časový limit operace nebo problémy s výkonem. Můžete explicitně zadat velikost bloku a zajistit tak, aby blockSizeInMB * 50000 bylo dostatečně velké pro ukládání dat, jinak se spuštění aktivity kopírování nezdaří. | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdné, použít výchozí) | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Prázdné, použít výchozí) | true (pravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true (pravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true (pravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro File5 |
| true (pravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor1 + soubor2 + file3 + file4 + obsah File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |flattenHierarchy | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |
| false (nepravda) |mergeFiles | Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Složku1<br/>&nbsp;&nbsp;&nbsp;&nbsp;obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaným názvem File1<br/><br/>Subfolder1 s file3, file4 a File5 se nezvednuty. |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Při kopírování souborů z Amazon S3/Azure blob/Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2/Azure Blob se můžete rozhodnout zachovat metadata souboru spolu s daty. Další informace o [zachování metadat](copy-activity-preserve-metadata.md#preserve-metadata)

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat seznamy ACL z Data Lake Storage Gen1

>[!TIP]
>Pokud chcete kopírovat data z Azure Data Lake Storage Gen1 do Gen2 obecně, přečtěte si téma [kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pro návod a osvědčené postupy.

Když kopírujete soubory z Azure Data Lake Storage Gen1 do Gen2, můžete se rozhodnout zachovat seznamy řízení přístupu (ACL) POSIX spolu s daty. Přečtěte si další informace o [zachování seznamů ACL z Data Lake Storage Gen1 do Gen2](copy-activity-preserve-metadata.md#preserve-acls).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku mapování dat můžete číst a zapisovat soubory z Azure Data Lake Storage Gen2 ve formátu JSON, Avro, text s oddělovači nebo Parquet. Další informace naleznete v tématu [transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v funkci toku dat mapování.

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v Azure Data Lake Storage Gen2. Na kartě **Možnosti zdrojového kódu** můžete spravovat, jak se soubory čtou. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesta zástupného znaku:** Pomocí vzoru se zástupnými znaky nastavíte ADF, aby prochází každou shodnou složku a soubor v jediné zdrojové transformaci. Toto je efektivní způsob, jak zpracovat více souborů v rámci jednoho toku. Přidejte více vzorů pro porovnávání se zástupnými znaky s symbolem +, který se zobrazí při najetí myší na stávající zástupný vzor.

Ze zdrojového kontejneru vyberte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta ke zástupným znakům proto musí taky obsahovat cestu ke složce z kořenové složky.

Příklady zástupných znaků:

* ```*``` představuje libovolnou sadu znaků.
* ```**``` představuje rekurzivní vnořování adresářů.
* ```?``` nahrazuje jeden znak.
* ```[]``` odpovídá jednomu nebo více znakům v závorkách.

* ```/data/sales/**/*.csv``` získá všechny soubory CSV pod/data/Sales
* ```/data/sales/20??/**``` získá všechny soubory ve dvacátém století.
* ```/data/sales/2004/*/12/[XY]1?.csv``` získá všechny soubory CSV v 2004 v prosinci začínající znakem X nebo Y a číslem se dvěma číslicemi.

**Kořenová cesta oddílu:** Pokud jste ve zdroji souborů nastavili dělené složky s formátem ```key=value``` (například Year = 2019), můžete přiřadit nejvyšší úroveň stromu složek oddílu k názvu sloupce v datovém proudu toku dat.

Nejdřív nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělené do oddílů, a soubory listů, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Nastavení kořenové cesty oddílu použijte k definování toho, co je nejvyšší úroveň struktury složek. Když zobrazíte obsah vašich dat prostřednictvím náhledu dat, uvidíte, že tento ADF bude přidávat vyřešené oddíly, které se nacházejí v jednotlivých úrovních vaší složky.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Zobrazit kořenovou cestu oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cest ke zpracování. Najeďte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat vyberte, že nechcete nic dělat se zdrojovým souborem, odstraňte zdrojový soubor nebo přesuňte zdrojový soubor. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do následujícího následného zpracování, vyberte nejprve možnost přesunout pro operaci soubor. Potom nastavte adresář "z". Pokud pro cestu nepoužíváte žádné zástupné znaky, pak bude mít nastavení "od" stejnou složku jako vaše zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, vaše syntaxe bude vypadat následovně:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "to" jako

```/backup/priorSales```

V tomto případě se všechny soubory, které se nacházely v/data/Sales, přesunuly do/backup/priorSales.

> [!NOTE]
> Operace se soubory běží jenom při spuštění toku dat ze spuštění kanálu (ladění kanálu nebo spuštění spuštění), které používá aktivitu spustit tok dat v kanálu. Operace *se* soubory neběží v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Můžete filtrovat, které soubory se mají zpracovat, zadáním rozsahu data při jejich poslední úpravě. Všechna data jsou v čase UTC. 

### <a name="sink-properties"></a>Vlastnosti jímky

V transformaci jímky můžete zapisovat do kontejneru nebo složky v Azure Data Lake Storage Gen2. Karta **Nastavení** vám umožní spravovat způsob zápisu souborů.

![možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymažte složku:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou:
   * **Výchozí**: umožňuje Sparku pojmenovat soubory založené na výchozím nastavení části.
   * **Vzor**: zadejte vzor, který vytvoří výčet výstupních souborů na oddíl. Například **výpůjčky [n]. csv** vytvoří loans1. csv, loans2. csv a tak dále.
   * **Na oddíl**: zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem k kontejneru DataSet, nikoli k cílové složce.
   * **Výstup do jednoho souboru**: sloučí výstupní soubory rozdělené do jednoho pojmenovaného souboru. Cesta je relativní vzhledem ke složce DataSet. Počítejte s tím, že operace te Merge může být na základě velikosti uzlu neúspěšná. Tato možnost se pro velké datové sady nedoporučuje.

**Všechna citace:** Určuje, jestli se mají všechny hodnoty uzavřít do uvozovek.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Navrhnete použití nového modelu uvedeného výše v předchozích částech a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **AzureBlobFSFile**. |Ano |
| folderPath | Cesta ke složce v Data Lake Storage Gen2. Pokud není zadán, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud má váš vlastní název složky zástupný znak nebo je tento řídicí znak uvnitř, použijte `^` k Escape. <br/><br/>Příklady: systém souborů/složka/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>V případě filtru jsou povolené zástupné znaky `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Pokud má váš skutečný název souboru zástupný znak nebo je tento řídicí znak uvnitř, použijte `^`.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadaný **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Pokud zkopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime.| Ne |
| format | Pokud chcete zkopírovat soubory, jako je mezi souborové úložiště (binární kopie), přejděte v části formát v definici vstupní a výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs-legacy.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) . |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze **FolderPath** .<br>Chcete-li zkopírovat jeden soubor se zadaným názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

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

### <a name="legacy-copy-activity-source-model"></a>Starší zdrojový model aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **AzureBlobFSSource**. |Ano |
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

### <a name="legacy-copy-activity-sink-model"></a>Starší model jímky aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type jímky aktivity kopírování musí být nastavená na **AzureBlobFSSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne |
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

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
