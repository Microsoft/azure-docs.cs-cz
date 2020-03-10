---
title: Formát JSON v Azure Data Factory
description: Toto téma popisuje, jak se zabývat formátem JSON v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: jingwang
ms.openlocfilehash: 7dac8d21e3b45307284ece15ca5ddbcc69db909b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397005"
---
# <a name="json-format-in-azure-data-factory"></a>Formát JSON v Azure Data Factory

Použijte tento článek, pokud chcete **analyzovat soubory JSON nebo zapsat data do formátu JSON**. 

Formát JSON se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou JSON.

| Vlastnost         | Popis                                                  | Požadováno |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **JSON**. | Ano      |
| umístění         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location`. **Podrobnosti najdete v článku o konektoru – > Vlastnosti datové sady**. | Ano      |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".| Ne       |
| compressionCodec | Kompresní kodek používaný pro čtení a zápis textových souborů. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **přichycení**nebo **LZ4**. pro použití při ukládání souboru. <br>Poznámka: v tuto chvíli aktivita kopírování nepodporuje "přichycení" & "LZ4".<br>Poznámka: při použití aktivity kopírování k dekompresi souborů ZipDeflate a zápisu do úložiště dat jímky založeného na souborech se soubory extrahují do složky: `<path specified in dataset>/<folder named as source zip file>/`. | Ne       |
| compressionLevel | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **nejrychlejší:** komprese by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **optimální**: komprese by měla být optimálně komprimovaná, i když dokončení operace trvá déle. Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Ne       |

Níže je příklad datové sady JSON v Azure Blob Storage:

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou JSON.

### <a name="json-as-source"></a>JSON jako zdroj

V části ***\*zdrojového\**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSource**. | Ano      |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají ve `storeSettings`své vlastní podporované nastavení pro čtení. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

### <a name="json-as-sink"></a>JSON jako jímka

V části\*aktivita kopírování ***\*jímka*** jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Požadováno |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSink**. | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **nastavení zápisu JSON** níže. | Ne       |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Jednotlivé konektory založené na souborech mají vlastní podporované nastavení zápisu v rámci `storeSettings`. **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

Podporovaná **nastavení zápisu JSON** v `formatSettings`:

| Vlastnost      | Popis                                                  | Požadováno                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **JsonWriteSettings**. | Ano                                                   |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může automaticky detekovat a analyzovat následující vzory souborů JSON. 

- **Typ I: setOfObjects**

    Každý soubor obsahuje jeden objekt nebo několik objektů, které jsou zřetězené nebo oddělené řádkem. 
    Pokud je tato možnost vybrána v jímky aktivity kopírování, aktivita kopírování vytvoří jeden soubor JSON s každým objektem na řádek (oddělený řádek).

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

Typy souborů JSON lze použít jako jímky i zdroj v toku dat mapování.

### <a name="creating-json-structures-in-a-derived-column"></a>Vytváření struktur JSON v odvozeném sloupci

Do toku dat můžete přidat složitý sloupec prostřednictvím Tvůrce výrazu odvozeného sloupce. V transformaci odvozeného sloupce přidejte nový sloupec a otevřete Tvůrce výrazů kliknutím na modrý rámeček. Chcete-li nastavit sloupec jako složitý, můžete ručně zadat strukturu JSON nebo použít uživatelské prostředí k interaktivnímu přidání podsloupců.

#### <a name="using-the-expression-builder-ux"></a>Použití uživatelského rozhraní Tvůrce výrazů

V podokně výstupní schéma umístěte ukazatel myši na sloupec a klikněte na ikonu se symbolem plus. Vyberte možnost **Přidat dílčí sloupec** a nastavte si sloupec jako složitý typ.

![Přidat Podsloupec](media/data-flow/addsubcolumn.png "Přidat Podsloupec")

Můžete přidat další sloupce a podsloupce stejným způsobem. Pro každé nekomplikované pole lze přidat výraz do pravé části editoru výrazů.

![Složitý sloupec](media/data-flow/complexcolumn.png "Složitý sloupec")

#### <a name="entering-the-json-structure-manually"></a>Ruční zadání struktury JSON

Chcete-li ručně přidat strukturu JSON, přidejte nový sloupec a v editoru zadejte výraz. Výraz následuje po tomto obecném formátu:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Pokud byl tento výraz zadán pro sloupec s názvem "complexColumn", pak bude zapsán do jímky jako následující JSON:

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Ukázka ručního skriptu pro kompletní hierarchickou definici
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

### <a name="source-format-options"></a>Možnosti formátu zdroje

Použití datové sady JSON jako zdroje v toku dat umožňuje nastavit pět dalších nastavení. Tato nastavení se dají najít v rámci souhlasu **Nastavení JSON** na kartě **zdrojové možnosti** .  

![Nastavení JSON](media/data-flow/json-settings.png "Nastavení JSON")

#### <a name="default"></a>Výchozí

Ve výchozím nastavení jsou data JSON čtena v následujícím formátu.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Jeden dokument

Je-li vybrán **jeden dokument** , mapování toků dat načtou z každého souboru jeden dokument JSON. 

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

#### <a name="unquoted-column-names"></a>Názvy sloupců bez uvozovek

Pokud je vybraná možnost **názvy sloupců bez uvozovek** , mapování toků dat přečte sloupce JSON, které nejsou obklopené uvozovkami. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Obsahuje komentáře

Vyberte **má komentáře** , pokud data JSON obsahují komentáře v C++ jazyce C nebo Style.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Jednoduché v uvozovkách

Pokud pole a hodnoty JSON místo dvojitých uvozovek používají jednoduché uvozovky, vyberte **jedno uvozovky** .

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Zpětné lomítko – řídicí

Pokud se k řídicím znakům v datech JSON mají použít zpětná lomítka, vyberte **jedno uvozovky** .

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita Lookup](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
