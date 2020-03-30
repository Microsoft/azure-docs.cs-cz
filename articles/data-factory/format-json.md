---
title: Formát JSON ve službě Azure Data Factory
description: Toto téma popisuje, jak zajít s formátem JSON v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260641"
---
# <a name="json-format-in-azure-data-factory"></a>Formát JSON ve službě Azure Data Factory

Postupujte podle tohoto článku, pokud chcete **analyzovat soubory JSON nebo zapsat data do formátu JSON**. 

Formát JSON je podporovaný pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [Datové sady.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou JSON.

| Vlastnost         | Popis                                                  | Požaduje se |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Vlastnost type datové sady musí být nastavena na **Json**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souboru má svůj `location`vlastní typ umístění a podporované vlastnosti v části . **Viz podrobnosti v článku konektoru -> část vlastností datové sady**. | Ano      |
| encodingName     | Typ kódování používaný ke čtení a zápisu testovacích souborů. <br>Povolené hodnoty jsou následující: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01147", "IBM01148", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Ne       |
| kompreseKodlič | Kompresní kodek používaný ke čtení a zápisu textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**nebo **lz4**. použijete při ukládání souboru. <br>Poznámka: Aktuálně kopírovat aktivitu nepodporuje "snappy" & "lz4".<br>Poznámka: Při použití kopírování aktivity dekompresi ZipDeflate soubor (y) a zapisovat `<path specified in dataset>/<folder named as source zip file>/`do úložiště dat jímky založené na souboru, soubory budou extrahovány do složky: . | Ne       |
| kompreseÚroveň | Kompresní poměr. <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Operace komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: Operace komprese by měla být optimálně komprimována, i když operace trvá delší dobu. Další informace naleznete v tématu [Úroveň komprese.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) | Ne       |

Níže je uveden příklad datové sady JSON ve službě Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem json a jímkou.

### <a name="json-as-source"></a>JSON jako zdroj

Následující vlastnosti jsou podporovány v části *** \*zdroje aktivity\* *** kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **JSONSource**. | Ano      |
| storeSettings | Skupina vlastností o čtení dat z úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení čtení v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

### <a name="json-as-sink"></a>JSON jako umyvadlo

Následující vlastnosti jsou podporovány v části *** \*jímky\* *** aktivity kopírování.

| Vlastnost      | Popis                                                  | Požaduje se |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Vlastnost type zdroje aktivity kopírování musí být nastavena na **JSONSink**. | Ano      |
| formatSettings | Skupina vlastností. Viz tabulka **nastavení zápisu JSON** níže. | Ne       |
| storeSettings | Skupina vlastností o tom, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má `storeSettings`vlastní podporovaná nastavení zápisu v části . **Viz podrobnosti v článku konektoru -> Kopírovat vlastnosti aktivity .** | Ne       |

Podporované **nastavení zápisu JSON** v části `formatSettings`:

| Vlastnost      | Popis                                                  | Požaduje se                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Typ formatSettings musí být nastavena na **JsonWriteSettings**. | Ano                                                   |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může automaticky detekovat a analyzovat následující vzorky souborů JSON. 

- **Typ I: setOfObjects**

    Každý soubor obsahuje jeden objekt nebo několik objektů, které jsou zřetězené nebo oddělené řádkem. 
    Pokud je tato možnost vybrána v jímce aktivity kopírování, zkopíruje aktivitu vytvoří jeden soubor JSON s každým objektem na řádek (čára oddělená).

    * **Příklad JSON s jedním objektem**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Příklad JSON s oddělením řádky**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Příklad JSON se zřetězením**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Každý soubor obsahuje pole objektů.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Mapování vlastností toku dat

Typy souborů JSON lze použít jako jímku i zdroj v mapování toku dat.

### <a name="creating-json-structures-in-a-derived-column"></a>Vytváření struktur JSON v odvozeném sloupci

Složitý sloupec můžete přidat do toku dat prostřednictvím tvůrce odvozených výrazů sloupců. V transformaci odvozeného sloupce přidejte nový sloupec a otevřete tvůrce výrazů kliknutím na modré pole. Chcete-li vytvořit sloupec složitý, můžete zadat strukturu JSON ručně nebo použít uživatelské číslo pro interaktivní přidání dílčích sloupců.

#### <a name="using-the-expression-builder-ux"></a>Použití uživatelského uživatelského jazyka tvůrce výrazů

V bočním podokně výstupního schématu najeďte na sloupec a klikněte na ikonu plus. Vyberte **Přidat podsloupec,** aby se sloupec zmítaný jako typ zkomplikoval.

![Přidat podsloupec](media/data-flow/addsubcolumn.png "Přidat podsloupec")

Stejným způsobem můžete přidat další sloupce a dílčí sloupce. Pro každé nesložité pole lze výraz přidat do editoru výrazů vpravo.

![Složitý sloupec](media/data-flow/complexcolumn.png "Složitý sloupec")

#### <a name="entering-the-json-structure-manually"></a>Ruční zadávání struktury JSON

Chcete-li ručně přidat strukturu JSON, přidejte nový sloupec a zadejte výraz do editoru. Výraz se řídí následujícím obecným formátem:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Pokud tento výraz byly zadány pro sloupec s názvem "complexColumn", pak by být zapsándo jímky jako následující JSON:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Ukázkový ruční skript pro úplnou hierarchickou definici
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

### <a name="source-format-options"></a>Možnosti zdrojového formátu

Použití datové sady JSON jako zdroje v toku dat umožňuje nastavit pět dalších nastavení. Tato nastavení lze nalézt v akordeon **nastavení JSON** na kartě **Možnosti zdroje.**  

![Nastavení JSON](media/data-flow/json-settings.png "Nastavení v souboru JSON")

#### <a name="default"></a>Výchozí

Ve výchozím nastavení jsou data JSON čtena v následujícím formátu.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Jeden dokument

Pokud je vybrán **jeden dokument,** mapování toků dat číst jeden dokument JSON z každého souboru. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

#### <a name="unquoted-column-names"></a>Názvy nekotovaných sloupců

Pokud je **vybraná volba Nekotované názvy sloupců,** mapování datových toků čte sloupce JSON, které nejsou obklopeny uvozovkami. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Má komentáře

Vyberte **Má komentáře,** pokud data JSON má C nebo C++ styl komentování.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Jeden citovaný

Vyberte **Jedno v uvozovkách,** pokud pole a hodnoty JSON používají jednoduché uvozovky místo dvojitých uvozovek.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Zpětné lomítko uvozeno

Vyberte **jeden v uvozovkách,** pokud se zpětná lomítka používají k úniku znaků v datech JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Další kroky

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Vyhledávací aktivita](control-flow-lookup-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
