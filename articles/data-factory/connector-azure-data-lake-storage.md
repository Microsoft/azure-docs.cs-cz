---
title: Kopírování a transformace dat v Azure Data Lake Storage Gen2
description: Zjistěte, jak kopírovat data do a z Azure Data Lake Storage Gen2 a transformovat data v Azure Data Lake Storage Gen2 pomocí Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: 929b9548661fe48fe90a55f0cacef234c3078cda
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887261"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopírování a transformace dat v Azure Data Lake Storage Gen2 pomocí Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) je sada funkcí určených pro analýzu velkých objemů dat integrovanou do [úložiště objektů blob Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít k rozhraní s daty pomocí paradigmat systému souborů a úložiště objektů.

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory kopírovat data z a do Azure Data Lake Storage Gen2 a použít Tok dat k transformaci dat v Azure Data Lake Storage Gen2. Další informace o Azure Data Factory najdete v [úvodním článku](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Data Lake Storage Gen2 je podporovaný pro následující aktivity:

- [Kopírování aktivity](copy-activity-overview.md) s [podporovanou maticí zdrojového/jímky](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

U aktivity Kopírování můžete pomocí tohoto konektoru:

- Kopírování dat z/do Azure Data Lake Storage Gen2 pomocí klíče účtu, instančního objektu nebo spravovaných identit pro ověřování prostředků Azure.
- Kopírujte soubory tak, jak jsou, nebo analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).
- [Zachovat metadata souboru během kopírování](#preserve-metadata-during-copy).
- [Zachovat akl při](#preserve-acls) kopírování z Azure Data Lake Storage Gen1/Gen2.

>[!IMPORTANT]
>Pokud povolíte **možnost Povolit důvěryhodným službám Microsoftu přístup k této možnosti účtu úložiště** v nastavení brány firewall Azure Storage a chcete použít runtime integrace Azure pro připojení k vašemu gen2 úložiště datového [jezera,](#managed-identity) musíte použít ověřování spravované identity pro ADLS Gen2.


## <a name="get-started"></a>Začínáme

>[!TIP]
>Návod, jak používat konektor Data Lake Storage Gen2, najdete v tématu [načítání dat do Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které se používají k definování entit Data Factory specifických pro Data Lake Storage Gen2.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Konektor Azure Data Lake Storage Gen2 podporuje následující typy ověřování. Podrobnosti naleznete v odpovídajících částech:

- [Ověřování pomocí klíče účtu](#account-key-authentication)
- [Ověřování instančních objektů](#service-principal-authentication)
- [Spravované identity pro ověřování prostředků Azure](#managed-identity)

>[!NOTE]
>Při použití PolyBase k načtení dat do datového skladu SQL, pokud je zdrojového úložiště datového jezera Gen2 nakonfigurováno s koncovým bodem virtuální sítě, musíte použít ověřování spravované identity podle požadavků PolyBase. Podívejte se na část [ověřování spravované identity](#managed-identity) s dalšími požadavky na konfiguraci.

### <a name="account-key-authentication"></a>Ověřování pomocí klíče účtu

Chcete-li použít ověřování pomocí klíče účtu úložiště, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobFS**. |Ano |
| url | Koncový bod pro úložiště datových jezer `https://<accountname>.dfs.core.windows.net`Gen2 se vzorem . | Ano |
| accountKey | Klíč účtu pro úložiště datového jezera Gen2. Označte toto pole jako SecureString bezpečně ukládat v datové továrně nebo [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). |Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud je úložiště dat v privátní síti, můžete použít runtime integrace Azure nebo runtime integrace s vlastním hostitelem. Pokud tato vlastnost není zadána, použije se výchozí doba runtime integrace Azure. |Ne |

>[!NOTE]
>Sekundární koncový bod systému souborů ADLS není při použití ověřování pomocí klíče účtu podporován. Můžete použít jiné typy ověřování.

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

Chcete-li použít ověřování instančního objektu, postupujte takto.

1. Zaregistrujte entitu aplikace ve službě Azure Active Directory (Azure AD) podle kroků v [části Registrace aplikace u klienta Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Poznamenejte si následující hodnoty, které používáte k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte příslušnému objektu služby správné oprávnění. Podívejte se na příklady fungování oprávnění v data Lake Storage Gen2 ze [seznamů řízení přístupu v souborech a adresářích](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

    - **Jako zdroj**: V Průzkumníku úložiště udělte alespoň oprávnění **Spustit** pro všechny upstream složky a systém souborů, spolu s **oprávněním ke čtení** pro soubory ke kopírování. Případně v řízení přístupu (IAM) udělte alespoň roli **čtečky dat objektů blob úložiště.**
    - **Jako jímka**: V Průzkumníku úložiště udělte alespoň **oprávnění provést** pro všechny upstream složky a systém souborů, spolu s **oprávněním k zápisu** pro složku jímky. Případně v řízení přístupu (IAM) udělte alespoň roli **přispěvatele dat objektů blob úložiště.**

>[!NOTE]
>Pokud používáte ui Factory data author a instanční objekt není nastaven a "Úložiště Čtečka dat blob/přispěvatel" role v IAM, při provádění testovací připojení nebo procházení nebo navigaci složky, zvolte "Test připojení k cestě k souboru" nebo "Procházet ze zadané cesty" a zadejte cestu s **oprávněním Ke čtení + spuštění** pokračovat.

Pro propojenou službu jsou podporovány tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobFS**. |Ano |
| url | Koncový bod pro úložiště datových jezer `https://<accountname>.dfs.core.windows.net`Gen2 se vzorem . | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako a `SecureString` bezpečně jej uložte v datové továrně. Nebo můžete [odkazovat na tajný klíč uložený v trezoru klíčů Azure](store-credentials-in-key-vault.md). | Ano |
| Nájemce | Zadejte informace o klientovi (název domény nebo ID klienta), pod kterým se aplikace nachází. Načtěte ji najet myší v pravém horním rohu portálu Azure. | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud je úložiště dat v privátní síti, můžete použít runtime integrace Azure nebo runtime integrace s vlastním hostitelem. Pokud není zadán, použije se výchozí doba runtime integrace Azure. |Ne |

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Spravované identity pro ověřování prostředků Azure

Továrna dat může být přidružena ke [spravované identitě pro prostředky Azure](data-factory-service-identity.md), která představuje tuto konkrétní datovou továrnu. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Storage Gen2, podobně jako při použití vlastního instančního objektu. Umožňuje této určené továrně přístup a kopírování dat do nebo z vašeho data Lake Storage Gen2.

Pokud chcete pro ověřování prostředků Azure používat spravované identity, postupujte takto.

1. [Načtěte informace o spravované identitě datové továrny](data-factory-service-identity.md#retrieve-managed-identity) zkopírováním hodnoty **ID spravovaného objektu identity** generovaného spolu s továrnou.

2. Udělte vlastní oprávnění spravované identity. Podívejte se na příklady fungování oprávnění v data Lake Storage Gen2 ze [seznamů řízení přístupu v souborech a adresářích](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Jako zdroj**: V Průzkumníku úložiště udělte alespoň oprávnění **Spustit** pro všechny upstream složky a systém souborů, spolu s **oprávněním ke čtení** pro soubory ke kopírování. Případně v řízení přístupu (IAM) udělte alespoň roli **čtečky dat objektů blob úložiště.**
    - **Jako jímka**: V Průzkumníku úložiště udělte alespoň **oprávnění provést** pro všechny upstream složky a systém souborů, spolu s **oprávněním k zápisu** pro složku jímky. Případně v řízení přístupu (IAM) udělte alespoň roli **přispěvatele dat objektů blob úložiště.**

>[!NOTE]
>Pokud používáte data Factory UI k autorovi a spravovaná identita není nastavena s rolí "Čtečka dat/přispěvatel objektů blob úložiště" v IAM, při provádění testovacího připojení nebo procházení/navigace složek zvolte "Testovat připojení k cestě k souboru" nebo "Procházet ze zadané cesty" a zadejte cestu s oprávněním **Ke čtení + Spuštění** pokračovat.

>[!IMPORTANT]
>Pokud používáte PolyBase k načtení dat z Data Lake Storage Gen2 do SQL Data Warehouse, při použití spravovaného ověřování identity pro Data Lake Storage Gen2, ujistěte se, že jste také postupujte podle kroků 1 a 2 v [tomto návodu](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) k 1) zaregistrovat sql databázový server s Azure Active Directory (Azure AD) a 2) přiřadit roli přispěvatele dat blob úložiště na váš databázový server SQL; zbytek zpracovává Data Factory. Pokud je vaše úložiště datového jezera Gen2 nakonfigurované s koncovým bodem virtuální sítě Azure, chcete-li použít PolyBase k načtení dat z něj, musíte použít spravované ověřování identity podle požadavků PolyBase.

Pro propojenou službu jsou podporovány tyto vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavena na **AzureBlobFS**. |Ano |
| url | Koncový bod pro úložiště datových jezer `https://<accountname>.dfs.core.windows.net`Gen2 se vzorem . | Ano |
| connectVia | [Integrační runtime,](concepts-integration-runtime.md) který se má použít k připojení k úložišti dat. Pokud je úložiště dat v privátní síti, můžete použít runtime integrace Azure nebo runtime integrace s vlastním hostitelem. Pokud není zadán, použije se výchozí doba runtime integrace Azure. |Ne |

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

Úplný seznam oddílů a vlastností dostupných pro definování datových sad naleznete v [tématu Datové sady](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro `location` Data Lake Storage Gen2 v rámci nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost type `location` v části v datové sadě musí být nastavena na **AzureBlobFSLocation**. | Ano      |
| Souborový systém | Název souborového systému Data Lake Storage Gen2.                              | Ne       |
| folderPath | Cesta ke složce pod daným systémem souborů. Chcete-li k filtrování složek filtrovat zástupný znak, přeskočte toto nastavení a zadejte jej v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným souboremSystem + folderPath. Chcete-li k filtrování souborů použít zástupný znak, přeskočte toto nastavení a zadejte jej v nastavení zdroje aktivity. | Ne       |

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v [tématu Kopírování konfigurací aktivit](copy-activity-overview.md#configuration) a [kanálů a aktivit](concepts-pipelines-activities.md). Tato část obsahuje seznam vlastností podporovaných zdrojem a jímkou úložiště datového jezera.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 jako typ zdroje

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro `storeSettings` Data Lake Storage Gen2 v rámci nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **AzureBlobFSReadSettings**. | Ano                                           |
| Rekurzivní                | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| program se zástupnými_znakem       | Cesta ke složce se zástupnými znaky pod daným systémem souborů nakonfigurovaná v datové sadě pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. <br>Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ne                                            |
| název souboru se zástupnými_znakové_         | Název souboru se zástupnými znaky pod daným systémem souborů + folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char uvnitř. Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). | Ano, `fileName` pokud není zadán v datové sadě |
| modifiedDatetimeStart    | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime. | Ne                                            |
| modifiedDatetimeEnd      | Stejně jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti úložiště současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

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

Následující vlastnosti jsou podporovány pro `storeSettings` Data Lake Storage Gen2 v rámci nastavení v jímce na základě formátu kopírování:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost type `storeSettings` under musí být nastavena na **AzureBlobFSWriteSettings**. | Ano      |
| copyBehavior             | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne       |
| blockSizeInMB | Zadejte velikost bloku v MB, která slouží k zápisu dat do adls gen2. Další informace [o objektech BLOB bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Povolená hodnota je **mezi 4 a 100 MB**. <br/>Ve výchozím nastavení adf automaticky určuje velikost bloku na základě typu úložiště zdrojového obchodu a dat. Pro nebinární kopírování do ADLS Gen2 je výchozí velikost bloku 100 MB, aby se vešla maximálně do dat 4.95 TB. Nemusí být optimální, pokud vaše data nejsou velká, zejména při použití prostředí Runtime integrace s vlastním hostitelem se špatnou sítí, což vede k výpadku operace nebo problému s výkonem. Můžete explicitně zadat velikost bloku, zatímco ujistěte se, že blockSizeInMB*50000 je dostatečně velký pro uložení dat, jinak se spustí operace kopírování. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

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

### <a name="folder-and-file-filter-examples"></a>Příklady filtrů složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru pomocí filtrů se zástupnými kódy.

| folderPath | fileName | Rekurzivní | Struktura zdrojové složky a výsledek filtru (soubory **tučně** jsou načteny)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdné, použít výchozí) | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | (Prázdné, použít výchozí) | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | false (nepravda) | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5.csv<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |
| `Folder*` | `*.csv` | true | SložkaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Soubor5.csv**<br/>Jiný FolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Některé příklady rekurzivní a copyBehavior

Tato část popisuje výsledné chování operace kopírování pro různé kombinace rekurzivních a copyBehavior hodnot.

| Rekurzivní | copyBehavior | Struktura zdrojových složek | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena se stejnou strukturou jako zdroj:<br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 |
| true |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru5 |
| true |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5 obsah jsou sloučeny do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |zachovat hierarchii | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |sloučení hierarchie | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název souboru1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky vygenerovaný název pro Soubor2<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |
| false (nepravda) |mergeFiles | Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Podsložka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor5 | Cílová složka Folder1 je vytvořena s následující strukturou: <br/><br/>Složka1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah File1 + File2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název souboru1<br/><br/>Podsložka1 s File3, File4 a File5 není zvedl. |

## <a name="preserve-metadata-during-copy"></a>Zachovat metadata během kopírování

Když kopírujete soubory z Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 do Azure Data Lake Storage Gen2/Azure Blob, můžete se rozhodnout zachovat metadata souboru spolu s daty. Další informace najdete v [souboru Zachovat metadata](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a>Zachovat akly z úložiště datového jezera Gen1/Gen2

Když kopírujete soubory z Azure Data Lake Storage Gen1/Gen2 do Gen2, můžete zachovat seznamy řízení přístupu POSIX (ACL) spolu s daty. Další informace najdete v [odkazech Zachovat seznamy ACod od data lake storage Gen1/Gen2 po Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Pokud chcete zkopírovat data z Azure Data Lake Storage Gen1 do Gen2 obecně, najdete v [tématu Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 s Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md) pro návod a osvědčené postupy.

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Při transformaci dat v toku dat mapování můžete číst a zapisovat soubory z Azure Data Lake Storage Gen2 ve formátu JSON, Avro, Oddělený text nebo parkety. Další informace naleznete v [tématu transformace zdroje](data-flow-source.md) a [transformace jímky](data-flow-sink.md) v prvku tok dat mapování.

### <a name="source-transformation"></a>Transformace zdroje

Ve zdrojové transformaci můžete číst z kontejneru, složky nebo jednotlivého souboru v Azure Data Lake Storage Gen2. Karta **Možnosti zdroje** umožňuje spravovat způsob čtení souborů. 

![Možnosti zdroje](media/data-flow/sourceOptions1.png "Možnosti zdroje")

**Cesta se zástupnými symboly:** Pomocí zástupný vzor pokyn ADF smyčku přes každou odpovídající složku a soubor v jednom zdroj transformace. Jedná se o efektivní způsob zpracování více souborů v rámci jednoho toku. Přidejte více vzorů porovnávání se zástupnými symboly se znaménkem +, které se zobrazí při najetí nad existujícím vzorkem se zástupnými symboly.

Ze zdrojového kontejneru zvolte řadu souborů, které odpovídají vzoru. V datové sadě lze zadat pouze kontejner. Cesta se zástupnými symboly proto musí obsahovat také cestu ke složce z kořenové složky.

Příklady zástupných symbolů:

* ```*```Představuje libovolnou sadu znaků.
* ```**```Představuje vnoření rekurzivního adresáře.
* ```?```Nahradí jeden znak
* ```[]```Shoduje se s jedním z více znaků v závorkách.

* ```/data/sales/**/*.csv```Získá všechny soubory csv pod /data/sales.
* ```/data/sales/20??/**/```Získá všechny soubory ve 20.
* ```/data/sales/*/*/*.csv```Získá soubory csv dvě úrovně pod /data/sales
* ```/data/sales/2004/*/12/[XY]1?.csv```Získá všechny soubory CSV v roce 2004 v prosinci počínaje X nebo Y předponou dvoumístné číslo

**Kořenová cesta oddílu:** Pokud máte rozdělené složky ve ```key=value``` zdroji souborů ve formátu (například year=2019), můžete přiřadit nejvyšší úroveň tohoto stromu složek oddílů názvu sloupce v datovém toku dat.

Nejprve nastavte zástupný znak tak, aby zahrnoval všechny cesty, které jsou rozdělenými složkami a soubory listu, které chcete číst.

![Nastavení zdrojového souboru oddílu](media/data-flow/partfile2.png "Nastavení souboru oddílu")

Pomocí nastavení Kořenová cesta oddílu definujte, jaká je nejvyšší úroveň struktury složek. Když si obsah dat zobrazíte v náhledu dat, uvidíte, že adf přidá vyřešené oddíly nalezené v každé z úrovní složek.

![Kořenová cesta oddílu](media/data-flow/partfile1.png "Náhled kořenové cesty oddílu")

**Seznam souborů:** Toto je sada souborů. Vytvořte textový soubor, který obsahuje seznam relativních souborů cesty ke zpracování. Přejděte na tento textový soubor.

**Sloupec pro uložení názvu souboru:** Uložte název zdrojového souboru do sloupce v datech. Sem zadejte nový název sloupce pro uložení řetězce názvu souboru.

**Po dokončení:** Po spuštění toku dat zvolte, zda se zdrojovým souborem nedělat nic, odstraňte zdrojový soubor nebo zdrojový soubor přesuňte. Cesty pro přesun jsou relativní.

Chcete-li přesunout zdrojové soubory do jiného umístění po zpracování, nejprve vyberte možnost Přesunout pro operaci souboru. Potom nastavte adresář "od". Pokud pro cestu nepoužíváte žádné zástupné znaky, bude nastavení "od" stejné jako zdrojová složka.

Pokud máte zdrojovou cestu se zástupným znakem, syntaxe bude vypadat takto:

```/data/sales/20??/**/*.csv```

Můžete zadat "od" jako

```/data/sales```

A "do" jako

```/backup/priorSales```

V tomto případě jsou všechny soubory, které byly získány pod /data/sales jsou přesunuty do /backup/priorSales.

> [!NOTE]
> Operace se soubory spustit pouze při spuštění toku dat z kanálu spustit (kanál ladění nebo spuštění spustit), který používá spustit tok dat aktivity v kanálu. Operace se soubory *nespouštějí* v režimu ladění toku dat.

**Filtrovat podle poslední změny:** Soubory, které zpracováváte, můžete filtrovat zadáním rozsahu dat, kdy byly naposledy změněny. Všechny časy data jsou v UTC. 

### <a name="sink-properties"></a>Vlastnosti jímky

V transformaci jímky můžete zapisovat do kontejneru nebo složky v Azure Data Lake Storage Gen2. Karta **Nastavení** umožňuje spravovat způsob zápisu souborů.

![možnosti jímky](media/data-flow/file-sink-settings.png "možnosti jímky")

**Vymazání složky:** Určuje, zda se cílová složka před zápisem dat vymaže.

**Možnost názvu souboru:** Určuje, jak jsou cílové soubory pojmenovány v cílové složce. Možnosti názvu souboru jsou následující:
   * **Výchozí**: Povolit Spark pojmenovat soubory na základě výchozích hodnot ČÁSTI.
   * **Vzorek**: Zadejte vzorek, který vyjmenovává výstupní soubory na oddíl. Například **půjčky[n].csv** vytvoří loans1.csv, loans2.csv a tak dále.
   * **Na oddíl**: Zadejte jeden název souboru na oddíl.
   * **Jako data ve sloupci**: Nastavte výstupní soubor na hodnotu sloupce. Cesta je relativní vzhledem ke kontejneru datové sady, nikoli k cílové složce. Pokud máte cestu ke složce v datové sadě, bude přepsána.
   * **Výstup do jednoho souboru**: Zkombinujte rozdělené výstupní soubory do jednoho pojmenovaného souboru. Cesta je relativní vzhledem ke složce datové sady. Uvědomte si, že operace te sloučení může případně selhat na základě velikosti uzlu. Tato možnost se nedoporučuje pro velké datové sady.

**Citace vše:** Určuje, zda mají být všechny hodnoty uzavřeny v uvozovkách.

## <a name="lookup-activity-properties"></a>Vlastnosti vyhledávací aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Chcete-li se dozvědět podrobnosti o vlastnostech, zkontrolujte [aktivitu GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Chcete-li se dozvědět podrobnosti o vlastnostech, zaškrtněte [políčko Odstranit aktivitu.](delete-activity.md)

## <a name="legacy-models"></a>Starší modely

>[!NOTE]
>Následující modely jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model uvedený ve výše uvedených oddílech do budoucna a vývojové uontové pole ADF přešlo na generování nového modelu.

### <a name="legacy-dataset-model"></a>Starší model datové sady

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na **AzureBlobFSFile**. |Ano |
| folderPath | Cesta ke složce v datovém úložišti jezera Gen2. Pokud není zadán, odkazuje na kořen. <br/><br/>Je podporován filtr se zástupnými symboly. Povolené zástupné `*` znaky jsou (odpovídá `?` nula nebo více znaků) a (odpovídá nulový nebo jeden znak). Použijte `^` k útěku, pokud váš skutečný název složky má zástupný znak nebo tento escape char je uvnitř. <br/><br/>Příklady: filesystem/folder/. Další příklady naleznete v [příkladech filtrů složek a souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Filtr názvu nebo zástupných symbolů pro soubory pod zadanou "ssložkouCesta". Pokud pro tuto vlastnost nezadáte hodnotu, datová sada odkazuje na všechny soubory ve složce. <br/><br/>Pro filtr jsou `*` povolené zástupné znaky (odpovídá `?` nula nebo více znaků) a (odpovídá nule nebo jeden znak).<br/>- Příklad 1:`"fileName": "*.csv"`<br/>- Příklad 2:`"fileName": "???20180427.txt"`<br/>Použijte `^` k útěku, pokud váš skutečný název souboru má zástupný znak nebo tento escape char je uvnitř.<br/><br/>Když fileName není zadán pro výstupní datovou sadu a **preserveHierarchy** není zadán v jímce aktivity, aktivita kopírování automaticky generuje název souboru s následujícím vzorem: "*Data.[ aktivita spustit Identifikátor GUID]. [GUID pokud sloučení hierarchie]. [formát, pokud je nakonfigurován]. [komprese, pokud je nakonfigurována]*", například "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". Pokud kopírujete z tabulkového zdroje pomocí názvu tabulky místo dotazu, bude se vzor názvu "*[název tabulky].[ formátu]. [komprese, pokud je nakonfigurována]*", například "MyTable.csv". |Ne |
| modifiedDatetimeStart | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je null, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| modifiedDatetimeEnd | Filtr souborů založený na atributu Poslední změna. Soubory jsou vybrány, pokud je jejich čas `modifiedDatetimeStart` `modifiedDatetimeEnd`poslední změny v časovém rozmezí mezi a . Čas je použit pro časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn povolením tohoto nastavení, pokud chcete provést filtrování souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou být NULL, což znamená, že na datovou sadu není použit žádný filtr atributů souboru. Pokud `modifiedDatetimeStart` má hodnotu `modifiedDatetimeEnd` datetime, ale je NULL, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je větší nebo roven hodnotě datetime. Pokud `modifiedDatetimeEnd` má hodnotu `modifiedDatetimeStart` datetime, ale je null, znamená to, že jsou vybrány soubory, jejichž poslední změněný atribut je menší než hodnota datetime.| Ne |
| formát | Pokud chcete kopírovat soubory tak, jak jsou mezi obchody založenými na souborech (binární kopie), přeskočte oddíl formátu v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory v určitém formátu, jsou podporovány následující typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte vlastnost **type** ve **formátu** na jednu z těchto hodnot. Další informace naleznete v části [Text ,](supported-file-formats-and-compression-codecs-legacy.md#text-format) [Formát JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formát ORC](supported-file-formats-and-compression-codecs-legacy.md#orc-format)a [Formát parket.](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) |Ne (pouze pro binární scénář kopírování) |
| komprese | Určete typ a úroveň komprese dat. Další informace naleznete [v tématu Podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory pod složku, zadejte pouze **složkuCesta.**<br>Chcete-li zkopírovat jeden soubor s daným názvem, zadejte **složkuCesta** s částí složky a **název_souboru** s názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů pod složku, zadejte **složkuCesta** s částí složky a **název_souboru** se zástupným filtrem. 

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

### <a name="legacy-copy-activity-source-model"></a>Starší model zdroje aktivity kopírování

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivity kopírování musí být nastavena na **AzureBlobFSSource**. |Ano |
| Rekurzivní | Označuje, zda jsou data čtena rekurzivně z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavena na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není zkopírována nebo vytvořena v jímce.<br/>Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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
| type | Vlastnost type jímky aktivity kopírování musí být nastavena na **AzureBlobFSSink**. |Ano |
| copyBehavior | Definuje chování kopírování, když zdroj jsou soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou následující:<br/><b>- PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta zdrojového souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>- Sloučení :</b>Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>- MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, je zadaným názvem sloučený název souboru. V opačném případě se jedná o název automaticky generovaného souboru. | Ne |
| maxConcurrentConnections | Počet připojení pro připojení k úložišti dat současně. Zadejte pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

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

Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v datové továrně naleznete v [tématu Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
