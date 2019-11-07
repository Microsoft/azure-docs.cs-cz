---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Data Factory
description: Naučte se, jak kopírovat data z podporovaných úložišť zdrojů dat do Azure Data Lake Store nebo z Data Lake Store na podporovaná úložiště jímky pomocí Data Factory.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 2aef04c4fe4713b107abe53fe459b7859a9c714e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681278"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuální verze](connector-azure-data-lake-store.md)

Tento článek popisuje, jak kopírovat data do a z Azure Data Lake Storage Gen1. Pokud se chcete dozvědět o Azure Data Factory, přečtěte si [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Tento konektor Azure Data Lake Storage Gen1 se podporuje pro následující činnosti:

- [Aktivita kopírování](copy-activity-overview.md) s [podporovanou maticí zdroje/jímky](copy-activity-overview.md) 
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Odstranit aktivitu](delete-activity.md)

Konkrétně pomocí tohoto konektoru můžete:

- Zkopírujte soubory pomocí jedné z následujících metod ověřování: instanční objekt nebo spravované identity pro prostředky Azure.
- Zkopírujte soubory tak, jak jsou, nebo Analyzujte nebo generujte soubory s [podporovanými formáty souborů a kompresními kodeky](supported-file-formats-and-compression-codecs.md).

> [!IMPORTANT]
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte podnikovou bránu firewall tak, aby umožňovala odchozí přenosy `<ADLS account name>.azuredatalakestore.net` a `login.microsoftonline.com/<tenant>/oauth2/token` na portu 443. Druhá je služba tokenů zabezpečení Azure, kterou modul runtime integrace potřebuje ke komunikaci s získáním přístupového tokenu.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Návod, jak používat konektor Azure Data Lake Store, najdete v tématu [načtení dat do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Data Lake Store jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost `type` musí být nastavena na hodnotu **AzureDataLakeStore**. | Ano |
| dataLakeStoreUri | Informace o účtu Azure Data Lake Store. Tyto informace přebírají jeden z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžadováno pro jímku |
| resourceGroupName | Název skupiny prostředků Azure, do které patří účet Data Lake Store. | Vyžadováno pro jímku |
| connectVia | [Prostředí Integration runtime](concepts-integration-runtime.md) , které se má použít pro připojení k úložišti dat. Pokud se vaše úložiště dat nachází v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tato vlastnost není zadaná, použije se výchozí prostředí Azure Integration runtime. |Ne |

### <a name="use-service-principal-authentication"></a>Použít ověřování instančního objektu

Chcete-li použít ověřování instančního objektu, postupujte podle těchto kroků.

1. Zaregistrujte entitu aplikace v Azure Active Directory a udělte jí přístup k Data Lake Store. Podrobný postup najdete v tématu [ověřování služba-služba](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které použijete k definování propojené služby:

    - ID aplikace
    - Klíč aplikace
    - ID tenanta

2. Udělte instančnímu objektu správné oprávnění. Podívejte se na příklady toho, jak oprávnění funguje v Data Lake Storage Gen1 z [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **As source**: v **Průzkumníku dat** > **přístup**udělte aspoň oprávnění **Execute** všem nadřazeným složkám včetně kořene spolu s oprávněním **ke čtení** pro soubory, které se mají zkopírovat. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Řízení přístupu na úrovni účtu (IAM) není nutné.
    - **Jako jímka**: v **Průzkumníku dat** > **přístup**udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám včetně kořene a oprávnění k **zápisu** do složky jímky. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Pokud používáte prostředí Azure Integration runtime ke kopírování (zdroj i jímka jsou v cloudu), udělte v IAM aspoň roli **Čtenář** , aby data Factory rozpoznat oblast pro data Lake Store. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte prostředí Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) s umístěním Data Lake Store. Například pokud je vaše Data Lake Store v Západní Evropa, vytvořte prostředí Azure Integration runtime s umístěním nastaveným na "Západní Evropa". Přidružte je v propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` pro bezpečné uložení v Data Factory nebo [odkazování na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Můžete ho načíst tak, že najedete myší v pravém horním rohu Azure Portal. | Ano |

**Příklad:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Použití spravovaných identit pro ověřování prostředků Azure

Datová továrna může být přidružená ke [spravované identitě prostředků Azure](data-factory-service-identity.md), která představuje tento konkrétní objekt pro vytváření dat. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Store, podobně jako při používání vlastního instančního objektu. Umožňuje této určené továrně přístup k datům a jejich kopírování z Data Lake Store.

Pokud chcete používat spravované identity pro ověřování prostředků Azure, postupujte podle těchto kroků.

1. [Načtěte informace o identitě spravované identity datovou továrnou](data-factory-service-identity.md#retrieve-managed-identity) , a to tak, že zkopírujete hodnotu ID aplikace identity služby generované společně s vaší továrnou.

2. Udělte spravované identitě přístup k Data Lake Store. Podívejte se na příklady toho, jak oprávnění funguje v Data Lake Storage Gen1 z [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions).

    - **As source**: v **Průzkumníku dat** > **přístup**udělte aspoň oprávnění **Execute** všem nadřazeným složkám včetně kořene spolu s oprávněním **ke čtení** pro soubory, které se mají zkopírovat. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Řízení přístupu na úrovni účtu (IAM) není nutné.
    - **Jako jímka**: v **Průzkumníku dat** > **přístup**udělte aspoň oprávnění ke **spuštění** všem nadřazeným složkám včetně kořene a oprávnění k **zápisu** do složky jímky. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Pokud používáte prostředí Azure Integration runtime ke kopírování (zdroj i jímka jsou v cloudu), udělte v IAM aspoň roli **Čtenář** , aby data Factory rozpoznat oblast pro data Lake Store. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte prostředí Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) s umístěním Data Lake Store. Přidružte je v propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

V Azure Data Factory nemusíte kromě obecných Data Lake Store informací v propojené službě zadávat žádné vlastnosti.

**Příklad:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci objektu DataSet `location` musí být nastavena na hodnotu **AzureDataLakeStoreLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak pro filtrování složek, toto nastavení nechte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho do nastavení zdroje aktivity. | Ne       |

**Příklad:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>Model zastaralé sady dat

>[!NOTE]
>Následující model DataSet je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu uvedeného výše v oddílu dál a uživatelské rozhraní pro vytváření ADF bylo přepnuto na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **AzureDataLakeStoreFile**. |Ano |
| folderPath | Cesta ke složce v Data Lake Store. Pokud není zadaný, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud vlastní název složky obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. <br/><br/>Příklad: RootFolder/podsložce/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada bude ukazovat na všechny soubory ve složce. <br/><br/>V případě filtru jsou povolené zástupné znaky `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>-Příklad 1: `"fileName": "*.csv"`<br/>-Příklad 2: `"fileName": "???20180427.txt"`<br/>Pokud má váš skutečný název souboru zástupný znak nebo znak escape uvnitř, použijte `^`.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadaný **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*data. [ identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Pokud kopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime.| Ne |
| formátovat | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. V části **Formát** nastavte vlastnost **typ** na jednu z těchto hodnot. Další informace najdete v oddílech [Formát textu](supported-file-formats-and-compression-codecs.md#text-format), [formát JSON](supported-file-formats-and-compression-codecs.md#json-format), [Formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), formát [ORC](supported-file-formats-and-compression-codecs.md#orc-format)a formát [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format) . |Ne (jenom pro binární scénář kopírování) |
| komprese | Zadejte typ a úroveň komprese dat. Další informace najdete v tématu [podporované formáty souborů a kompresní kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **gzip**, **Deflate**, **bzip2**a **ZipDeflate**.<br/>Podporované úrovně jsou **optimální** a **nejrychlejší**. |Ne |

>[!TIP]
>Chcete-li zkopírovat všechny soubory ve složce, zadejte pouze **FolderPath** .<br>Chcete-li zkopírovat jeden soubor s určitým názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

**Příklad:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
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

Úplný seznam oddílů a vlastností dostupných pro definování aktivit najdete v tématu [kanály](concepts-pipelines-activities.md). V této části najdete seznam vlastností podporovaných Azure Data Lake Store zdroj a jímka.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store jako zdroj

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureDataLakeStoreReadSetting**. | Ano                                           |
| zahrnout                | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud vlastní název složky obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné znaky jsou `*` (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku). Pokud vlastní název složky obsahuje zástupný znak nebo řídicí znak v rámci, použijte `^`. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, pokud v DataSet není zadána `fileName` |
| modifiedDatetimeStart    | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a `modifiedDatetimeEnd`. Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Pokud má `modifiedDatetimeStart` hodnotu DateTime, ale `modifiedDatetimeEnd` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud má `modifiedDatetimeEnd` hodnotu DateTime, ale `modifiedDatetimeStart` je NULL, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší než hodnota DateTime. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
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
                    "type": "AzureDataLakeStoreReadSetting",
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

#### <a name="legacy-source-model"></a>Starší zdrojový model

>[!NOTE]
>Následující zdrojový model kopírování je stále podporován tak, jak je kvůli zpětné kompatibilitě. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost `type` zdroje aktivity kopírování musí být nastavena na hodnotu **AzureDataLakeStoreSource**. |Ano |
| zahrnout | Určuje, zda mají být data rekurzivně čtena z podsložek nebo pouze ze zadané složky. Pokud je `recursive` nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store jako jímka

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v rámci `storeSettings` musí být nastavená na **AzureDataLakeStoreWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, Název sloučeného souboru je zadaný název. V opačném případě se jedná o automaticky vygenerovaný název souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
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
                    "type": "AzureDataLakeStoreWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Starší model jímky

>[!NOTE]
>Následující model kopie jímky je stále podporovaný, protože je z důvodu zpětné kompatibility. Navrhnete použití nového modelu, který je uvedený výše, a uživatelské rozhraní pro vytváření ADF se přepnulo na generování nového modelu.

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost `type` jímky aktivity kopírování musí být nastavená na **AzureDataLakeStoreSink**. |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdrojem soubory z úložiště dat založeného na souborech.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: zachovává hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: sloučí všechny soubory ze zdrojové složky do jednoho souboru. Je-li zadán název souboru, Název sloučeného souboru je zadaný název. V opačném případě se název souboru automaticky vygeneruje. | Ne |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne |

**Příklad:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Příklady filtru složek a souborů

Tato část popisuje výsledné chování cesty ke složce a názvu souboru s filtry zástupných znaků.

| folderPath | fileName | zahrnout | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdné, použít výchozí) | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Prázdné, použít výchozí) | true (pravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false (nepravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true (pravda) | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Soubor1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**file3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Příklady chování operace kopírování

Tato část popisuje výsledné chování operace kopírování pro různé kombinace `recursive` a `copyBehavior` hodnot.

| zahrnout | copyBehavior | Struktura zdrojové složky | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true (pravda) |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro File5 |
| true (pravda) |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor1 + soubor2 + file3 + file4 + obsah File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false (nepravda) |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |
| false (nepravda) |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaný název pro soubor2<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |
| false (nepravda) |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Soubor1<br/>&nbsp;&nbsp;&nbsp;&nbsp;soubor2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 je vytvořen s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky vygenerovaný název pro Soubor1<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Zachování seznamů ACL Data Lake Storage Gen2

Pokud chcete replikovat seznamy řízení přístupu (ACL) spolu s datovými soubory při upgradu z Data Lake Storage Gen1 na Data Lake Storage Gen2, přečtěte si téma [zachování seznamů ACL z Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1).

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Přečtěte si další informace o [transformaci zdrojového kódu](data-flow-source.md) a [transformaci jímky](data-flow-sink.md) v funkci toku dat mapování.

## <a name="lookup-activity-properties"></a>Vlastnosti aktivity vyhledávání

Chcete-li získat informace o vlastnostech, ověřte [aktivitu vyhledávání](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Vlastnosti aktivity GetMetadata

Pokud se chcete dozvědět víc o vlastnostech, podívejte se na [aktivitu GetMetadata](control-flow-get-metadata-activity.md) . 

## <a name="delete-activity-properties"></a>Odstranit vlastnosti aktivity

Další informace o vlastnostech najdete v části [Odstranění aktivity](delete-activity.md) .

## <a name="next-steps"></a>Další kroky

Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md##supported-data-stores-and-formats).
