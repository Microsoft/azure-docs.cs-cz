---
title: Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Data Factory | Microsoft Docs
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
ms.date: 09/09/2019
ms.author: jingwang
ms.openlocfilehash: 968e356947e99c3b6c4fe9d5acd2efed264be5b0
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010116"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Kopírování dat do nebo z Azure Data Lake Storage Gen1 pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Azure Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-azure-datalake-connector.md)
> * [Aktuální verze](connector-azure-data-lake-store.md)

Tento článek popisuje, jak kopírovat data do a z Azure Data Lake Storage Gen1. Další informace o Azure Data Factory najdete v článku [úvodní článek](introduction.md).

## <a name="supported-capabilities"></a>Podporované funkce

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
> Pokud kopírujete data pomocí místního prostředí Integration runtime, nakonfigurujte podnikovou bránu firewall tak, aby povolovala `<ADLS account name>.azuredatalakestore.net` odchozí `login.microsoftonline.com/<tenant>/oauth2/token` přenosy do a na portu 443. Druhá je služba tokenů zabezpečení Azure, kterou modul runtime integrace potřebuje ke komunikaci s získáním přístupového tokenu.

## <a name="get-started"></a>Začínáme

> [!TIP]
> Návod, jak používat konektor Azure Data Lake Store, najdete v tématu [načtení dat do Azure Data Lake Store](load-azure-data-lake-store.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Následující části obsahují informace o vlastnostech, které slouží k definování Data Factory entit specifických pro Azure Data Lake Store.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Azure Data Lake Store jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost musí být nastavená na **AzureDataLakeStore.** `type` | Ano |
| dataLakeStoreUri | Informace o účtu Azure Data Lake Store. Tyto informace má jednu z následujících formátů: `https://[accountname].azuredatalakestore.net/webhdfs/v1` nebo `adl://[accountname].azuredatalakestore.net/`. | Ano |
| subscriptionId | ID předplatného Azure, ke kterému patří účet Data Lake Store. | Vyžaduje se pro jímku |
| resourceGroupName | Název skupiny prostředků Azure, do které patří účet Data Lake Store. | Vyžaduje se pro jímku |
| connectVia | [Prostředí integration runtime](concepts-integration-runtime.md) se použije k připojení k úložišti. Pokud se vaše úložiště dat nachází v privátní síti, můžete použít prostředí Azure Integration runtime nebo místní prostředí Integration runtime. Pokud tato vlastnost není zadaná, použije se výchozí prostředí Azure Integration runtime. |Ne |

### <a name="use-service-principal-authentication"></a>Použít ověřování instančního objektu

Chcete-li použít ověřování instančního objektu, zaregistrujte entitu aplikace v Azure Active Directory a udělte jí přístup k Data Lake Store. Podrobné pokyny najdete v článku [ověřování služba služba](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Poznamenejte si následující hodnoty, které slouží k definování propojené služby:

- ID aplikace
- Klíč aplikace
- ID tenanta

>[!IMPORTANT]
> Udělte instančnímu objektu správné oprávnění v Data Lake Store:
>- **Jako zdroj**: V **Průzkumníku** > dat udělte oprávnění k vypsání alespoň oprávnění **ke čtení** a kopírování souborů do složek a podsložek. Nebo můžete udělit oprávnění **ke čtení** pro kopírování jednoho souboru. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Řízení přístupu na úrovni účtu (IAM) není nutné.
>- **Jako jímka**: V okně**přístup k**aplikaci **data** > Access udělte oprávnění k vytváření podřízených položek ve složce alespoň pro **zápis + Execute** . Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Pokud používáte prostředí Azure Integration runtime ke kopírování (zdroj i jímka jsou v cloudu), udělte v IAM aspoň roli **Čtenář** , aby data Factory rozpoznat oblast pro data Lake Store. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte prostředí Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) s umístěním Data Lake Store. Například pokud je vaše Data Lake Store v Západní Evropa, vytvořte prostředí Azure Integration runtime s umístěním nastaveným na "Západní Evropa". Přidružte je v propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

>[!NOTE]
>Chcete-li zobrazit seznam složek počínaje kořenovým adresářem, je nutné nastavit oprávnění instančního objektu, který je udělen na **kořenové úrovni s oprávněním "Execute"** . To platí v případě, že použijete:
>- **Nástroj pro kopírování dat** umožňuje vytvořit kanál pro kopírování.
>- **Data Factory uživatelské rozhraní** pro otestování připojení a procházení složek během vytváření obsahu.
>Pokud máte obavy o udělení oprávnění na kořenové úrovni, během vytváření, vynechání testování připojení a zadání paraent cesty s uděleným oprávněním a pak zvolte možnost Procházet z této zadané cesty. Aktivita kopírování funguje, pokud je instančnímu objektu uděleno správné oprávnění v kopírovaných souborech.

Podporovány jsou následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako `SecureString` pro bezpečné uložení v Data Factory nebo [odkaz na tajný kód uložený v Azure Key Vault](store-credentials-in-key-vault.md). | Ano |
| tenant | Zadejte informace o tenantovi, jako je název domény nebo ID tenanta, pod kterým se vaše aplikace nachází. Podržením ukazatele myši v pravém horním rohu webu Azure portal můžete načíst ji. | Ano |

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

Objekt pro vytváření dat můžou být spojené s [spravované identity pro prostředky Azure](data-factory-service-identity.md), která představuje této konkrétní datové továrně. Tuto spravovanou identitu můžete přímo použít pro ověřování Data Lake Store, podobně jako při používání vlastního instančního objektu. Umožňuje této určené továrně přístup k datům a jejich kopírování z Data Lake Store.

Použití spravované identity pro ověřování prostředků Azure:

1. [Načtěte informace o identitě spravované identity datovou továrnou](data-factory-service-identity.md#retrieve-managed-identity) , a to tak, že zkopírujete hodnotu ID aplikace identity služby generované společně s vaší továrnou.
2. Udělte spravované identitě přístup k Data Lake Store stejným způsobem jako u instančního objektu podle těchto poznámek.

>[!IMPORTANT]
> Ujistěte se, že udělíte patřičná oprávnění pro spravovanou identitu pro datovou továrnu v Data Lake Store:
>- **Jako zdroj**: V **Průzkumníku** > dat udělte oprávnění k vypsání alespoň oprávnění **ke čtení** a kopírování souborů do složek a podsložek. Nebo můžete udělit oprávnění **ke čtení** pro kopírování jednoho souboru. Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Řízení přístupu na úrovni účtu (IAM) není nutné.
>- **Jako jímka**: V okně**přístup k**aplikaci **data** > Access udělte oprávnění k vytváření podřízených položek ve složce alespoň pro **zápis + Execute** . Můžete zvolit, že se má do **této složky a všech podřízených objektů** přidat rekurzivní a přidat jako **oprávnění k přístupu a výchozí položku oprávnění**. Pokud používáte prostředí Azure Integration runtime ke kopírování (zdroj i jímka jsou v cloudu), udělte v IAM aspoň roli **Čtenář** , aby data Factory rozpoznat oblast pro data Lake Store. Pokud se chcete této roli IAM vyhnout, explicitně [vytvořte prostředí Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) s umístěním Data Lake Store. Přidružte je v propojené službě Data Lake Store, jak je znázorněno v následujícím příkladu.

>[!NOTE]
>Chcete-li zobrazit seznam složek počínaje kořenovým adresářem, je nutné nastavit oprávnění pro spravovanou identitu udělenou na **kořenové úrovni s oprávněním Execute**. To platí v případě, že použijete:
>- **Nástroj pro kopírování dat** umožňuje vytvořit kanál pro kopírování.
>- **Data Factory uživatelské rozhraní** pro otestování připojení a procházení složek během vytváření obsahu.
>Pokud máte obavy o udělení oprávnění na kořenové úrovni, během vytváření, vynechání testování připojení a zadání nadřazené cesty s uděleným oprávněním a pak zvolte možnost Procházet z této zadané cesty. Aktivita kopírování funguje, pokud je instančnímu objektu uděleno správné oprávnění v kopírovaných souborech.

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

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. 

- Pro **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [Parquet, oddělený text, JSON, Avro a binární formát binárního formátu](#format-based-dataset) .
- Pro jiné formáty, jako je **Formát ORC**, se podívejte na [jiný oddíl formátu DataSet](#other-format-dataset) .

### <a name="format-based-dataset"></a>Datová sada Parquet, oddělený text, JSON, Avro a binární formát

Chcete-li kopírovat data do a z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro datovou sadu založenou na formátu a podporovaná nastavení. .
Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `location` nastavení v datové sadě založené na formátu:

| Vlastnost   | Popis                                                  | Požaduje se |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Vlastnost Type v rámci `location` datové sady musí být nastavená na **AzureDataLakeStoreLocation**. | Ano      |
| folderPath | Cesta ke složce Pokud chcete použít zástupný znak pro filtrování složek, toto nastavení nechte a zadejte v nastavení zdroje aktivity. | Ne       |
| fileName   | Název souboru pod daným folderPath. Pokud chcete použít zástupný znak k filtrování souborů, přeskočte toto nastavení a zadejte ho do nastavení zdroje aktivity. | Ne       |

> [!NOTE]
>
> Datová sada typu **AzureDataLakeStoreFile** s Parquet nebo textem formátu uvedená v následující části je stále podporovaná tak, jak je to pro účely zpětné kompatibility kopírování, vyhledávání a GetMetadata. Ale nefunguje s funkcí toku dat mapování. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

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

### <a name="other-format-dataset"></a>Jiná Formátová datová sada

Chcete-li kopírovat data do a z Azure Data Lake Store Gen1 ve **formátu ORC**, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost Type datové sady musí být nastavená na **AzureDataLakeStoreFile**. |Ano |
| folderPath | Cesta ke složce v Data Lake Store. Pokud není zadán, odkazuje na kořen. <br/><br/>Filtr zástupných znaků je podporován. Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Použijte `^` k ukončení, jestli vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v. <br/><br/>Příklad: RootFolder/podsložce/. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). |Ne |
| fileName | Název nebo zástupný filtr pro soubory v zadaném "folderPath". Pokud nezadáte hodnotu pro tuto vlastnost, datová sada odkazuje na všechny soubory ve složce. <br/><br/>V případě filtru je povolených `*` zástupných znaků (odpovídá žádnému nebo více znakům) a `?` (odpovídá žádnému nebo jednomu znaku).<br/>– Příklad 1: `"fileName": "*.csv"`<br/>– Příklad 2: `"fileName": "???20180427.txt"`<br/>Použijte `^` k ukončení, pokud skutečný název souboru obsahuje zástupný znak nebo tento řídicí znak v.<br/><br/>Když není zadaný název souboru pro výstupní datovou sadu a v jímky aktivity není zadané **preserveHierarchy** , aktivita kopírování automaticky vygeneruje název souboru s následujícím vzorem: "*Data. [identifikátor GUID ID běhu aktivity]. [GUID if FlattenHierarchy]. [formát, pokud je nakonfigurován]. [komprese, je-li nakonfigurována]* ", například" data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Pokud kopírujete z tabulkového zdroje místo dotazu pomocí názvu tabulky, bude vzor názvu " *[název tabulky]. [ formát]. [komprese, je-li nakonfigurována]* ", například" myTable. csv ". |Ne |
| modifiedDatetimeStart | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| Ne |
| modifiedDatetimeEnd | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br/><br/> Celkový výkon přesunu dat je ovlivněn tím, že toto nastavení povolíte, pokud chcete provést filtr souborů s velkým množstvím souborů. <br/><br/> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime.| Ne |
| format | Pokud chcete kopírovat soubory mezi úložišti na základě souborů (binární kopie), přeskočte oddíl formát v definicích vstupní i výstupní datové sady.<br/><br/>Pokud chcete analyzovat nebo generovat soubory s konkrétním formátem, podporují se tyto typy formátů souborů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Nastavte **typ** vlastnosti v části **formátu** na jednu z těchto hodnot. Další informace najdete v tématu [textový formát](supported-file-formats-and-compression-codecs.md#text-format), [formátu JSON](supported-file-formats-and-compression-codecs.md#json-format), [formát Avro](supported-file-formats-and-compression-codecs.md#avro-format), [formát Orc](supported-file-formats-and-compression-codecs.md#orc-format), a [formát Parquet ](supported-file-formats-and-compression-codecs.md#parquet-format) oddíly. |Ne (pouze pro binární kopie scénář) |
| compression | Zadejte typ a úroveň komprese pro data. Další informace najdete v tématu [podporované formáty souborů a komprese kodeky](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Podporované typy jsou **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**.<br/>Jsou podporované úrovně **Optimal** a **nejrychlejší**. |Ne |


>[!TIP]
>Zkopírujte všechny soubory ve složce, zadejte **folderPath** pouze.<br>Chcete-li zkopírovat jeden soubor s určitým názvem, zadejte **FolderPath** s částí **složky a názvem souboru s** názvem souboru.<br>Chcete-li zkopírovat podmnožinu souborů ve složce, zadejte **FolderPath** s částí složky a **názvem souboru** s filtrem zástupných znaků. 

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

- Chcete-li kopírovat z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [zdrojové části Parquet, oddělovače text, JSON, Avro a Binary Format](#format-based-source) .
- Chcete-li kopírovat z jiných formátů, jako je například **Formát ORC**, přečtěte si část [Další zdrojový formát](#other-format-source) .

#### <a name="format-based-source"></a>Parquet, oddělený text, JSON, Avro a binární formát formátu

Chcete-li kopírovat data z **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro zdroj aktivity kopírování na základě formátu a podporovaný. možnost.  Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `storeSettings` nastavení ve zdroji kopírování založeném na formátu:

| Vlastnost                 | Popis                                                  | Požaduje se                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureDataLakeStoreReadSetting**. | Ano                                           |
| recursive                | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Pokud je rekurzivní nastavení nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne                                            |
| wildcardFolderPath       | Cesta ke složce se zástupnými znaky pro filtrování zdrojových složek. <br>Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Použijte `^` k ukončení, jestli vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v. <br>Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ne                                            |
| wildcardFileName         | Název souboru se zástupnými znaky v rámci daného folderPath/wildcardFolderPath pro filtrování zdrojových souborů. <br>Povolené zástupné `*` znaky jsou (Porovná žádný nebo více `?` znaků) a (Porovná žádný nebo jeden znak). Použijte `^` k ukončení, jestli vlastní název složky obsahuje zástupný znak nebo tento řídicí znak v. Další příklady najdete v [příkladech složky a filtru souborů](#folder-and-file-filter-examples). | Ano, `fileName` Pokud není v datové sadě určeno |
| modifiedDatetimeStart    | Filtr souborů na základě naposledy změněného atributu Soubory jsou vybrány, pokud čas poslední změny spadá do časového rozsahu mezi `modifiedDatetimeStart` a. `modifiedDatetimeEnd` Čas se použije na časové pásmo UTC ve formátu "2018-12-01T05:00:00Z". <br> Vlastnosti mohou mít hodnotu NULL, což znamená, že pro datovou sadu není použit filtr atributů souboru. Když `modifiedDatetimeStart` má hodnotu DateTime, ale `modifiedDatetimeEnd` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je větší nebo roven hodnotě DateTime. Pokud `modifiedDatetimeEnd` má hodnota DateTime, ale `modifiedDatetimeStart` je null, znamená to, že jsou vybrány soubory, jejichž atribut Last Modified je menší, než je hodnota DateTime. | Ne                                            |
| modifiedDatetimeEnd      | Stejné jako výše.                                               | Ne                                            |
| maxConcurrentConnections | Počet připojení, která se mají souběžně připojit k úložišti úložiště Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne                                            |

> [!NOTE]
> Pro Parquet nebo oddělený textový formát je zdroj aktivity kopírování typu **AzureDataLakeStoreSource** , který je uveden v následující části, stále podporován, jak je z důvodu zpětné kompatibility. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

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

#### <a name="other-format-source"></a>Jiný zdroj formátu

Chcete-li kopírovat data z Azure Data Lake Store Gen1 ve **formátu ORC**, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost zdroje aktivity kopírování musí být nastavená na AzureDataLakeStoreSource. `type` |Ano |
| recursive | Určuje, jestli se data číst rekurzivně z podsložky nebo pouze z určené složky. Když `recursive` je nastaveno na hodnotu true a jímka je úložiště založené na souborech, prázdná složka nebo podsložka není kopírována ani vytvořena v jímky. Povolené hodnoty jsou **true** (výchozí) a **false**. | Ne |
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

- Pokud chcete kopírovat do **Parquet, oddělený text, JSON, Avro a binární formát**, přečtěte si část [Parquet, oddělený text, JSON, Avro a binární formát](#format-based-sink) .
- Chcete-li kopírovat do jiných formátů, jako je například **Formát ORC/JSON**, přečtěte si [Další část formátování jímky](#other-format-sink) .

#### <a name="format-based-sink"></a>Jímka Parquet, oddělený text, JSON, Avro a binární formát

Chcete-li kopírovat data na **Parquet, oddělený text, formát JSON, Avro a binární formát**, přečtěte si článek [Parquet](format-parquet.md), formát [textu s oddělovači](format-delimited-text.md), formát [Avro](format-avro.md) a [binární formát](format-binary.md) pro jímku aktivity kopírování založené na formátu a podpora. možnost.  Následující vlastnosti jsou podporovány pro Azure Data Lake Store Gen1 v části `storeSettings` nastavení v jímky kopírování na základě formátu:

| Vlastnost                 | Popis                                                  | Požaduje se |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Vlastnost Type v poli `storeSettings` musí být nastavená na **AzureDataLakeStoreWriteSetting**. | Ano      |
| copyBehavior             | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě je automaticky generovaným názvem souboru. | Ne       |
| maxConcurrentConnections | Počet připojení, která mají být souběžně propojena s úložištěm dat. Určete pouze v případě, že chcete omezit souběžné připojení k úložišti dat. | Ne       |

> [!NOTE]
> Pro Parquet nebo oddělený textový formát je jímka aktivity kopírování typu **AzureDataLakeStoreSink** uvedená v následující části stále podporovaná tak, aby se zajistila zpětná kompatibilita. Doporučujeme, abyste tento nový model používali dál. Uživatelské rozhraní pro vytváření Data Factory generuje tyto nové typy.

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

#### <a name="other-format-sink"></a>Další jímka formátu

Chcete-li kopírovat data do Azure Data Lake Store Gen1 ve **formátu ORC**, jsou v části **jímky** podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost jímky aktivity kopírování musí být nastavená na AzureDataLakeStoreSink. `type` |Ano |
| copyBehavior | Definuje chování kopírování, pokud je zdroj souborů z úložiště dat založeného na souboru.<br/><br/>Povolené hodnoty jsou:<br/><b>-PreserveHierarchy (výchozí)</b>: Zachová hierarchii souborů v cílové složce. Relativní cesta ke zdrojovému souboru ke zdrojové složce je shodná s relativní cestou cílového souboru k cílové složce.<br/><b>-FlattenHierarchy</b>: Všechny soubory ze zdrojové složky jsou v první úrovni cílové složky. Cílové soubory mají automaticky generované názvy. <br/><b>-MergeFiles</b>: Sloučí všechny soubory ze zdrojové složky do jednoho souboru. Pokud je zadán název souboru, název sloučený soubor je zadaný název. V opačném případě se název souboru automaticky vygeneruje. | Ne |
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

| folderPath | fileName | recursive | Struktura zdrojové složky a výsledek filtru (jsou načteny soubory **tučně** )|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Prázdné, použít výchozí) | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Prázdné, použít výchozí) | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | false | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | Složka<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>Příklady chování operace kopírování

Tato část popisuje výsledné chování operace kopírování pro různé kombinace `recursive` hodnot a. `copyBehavior`

| recursive | copyBehavior | Struktura složky zdroje | Výsledný cíl |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cílový Složku1 se vytvoří se stejnou strukturou jako zdroj:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true (pravda) |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah souboru soubor1 + soubor2 + file3 + file4 + File5 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automaticky generovaným názvem File2<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Soubor3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Cíl složku1 se vytvoří s následující strukturou:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Obsah soubor1 + soubor2 se sloučí do jednoho souboru s automaticky generovaným názvem souboru. automaticky generovaným názvem File1<br/><br/>Subfolder1 s file3, file4 a File5 se nedají vyzvednutí. |

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

## <a name="next-steps"></a>Další postup

Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md##supported-data-stores-and-formats).
