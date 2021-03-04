---
title: Formát JSON v Azure Data Factory
description: Toto téma popisuje, jak se zabývat formátem JSON v Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: f19f8fb3811435e9bbc207d2d130c0655a6dee02
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706061"
---
# <a name="json-format-in-azure-data-factory"></a>Formát JSON v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte tento článek, pokud chcete **analyzovat soubory JSON nebo zapsat data do formátu JSON**. 

Formát JSON se podporuje pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou JSON.

| Vlastnost         | Popis                                                  | Povinné |
| ---------------- | ------------------------------------------------------------ | -------- |
| typ             | Vlastnost Type datové sady musí být nastavená na **JSON**. | Ano      |
| location         | Nastavení umístění souborů. Každý konektor založený na souborech má svůj vlastní typ umístění a podporované vlastnosti v rámci `location` . **Podrobnosti najdete v článku o konektoru – > vlastnosti datové sady**. | Ano      |
| encodingName     | Typ kódování používaný pro čtení a zápis testovacích souborů. <br>Povolené hodnoty jsou následující: UTF-8, UTF-16, "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "Shift-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860"; "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "" 2022, "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13"; , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Ne       |
| komprese | Skupina vlastností pro konfiguraci komprese souborů. Tuto část nakonfigurujte, pokud chcete během provádění aktivit provést kompresi nebo dekompresi. | Ne |
| typ<br/>(*pod `compression`*) | Kompresní kodek používaný pro čtení a zápis souborů JSON. <br>Povolené hodnoty jsou **bzip2**, **gzip**, **Deflate**, **ZipDeflate**, **TarGzip**, **tar**, **přichycení** a **LZ4**. Výchozí hodnota není komprimovaná.<br>**Poznámka:** aktivita kopírování nepodporuje "přichycení" & "LZ4" a tok dat mapování nepodporuje "ZipDeflate" "," TarGzip "a" tar ".<br>**Poznámka:** při použití aktivity kopírování k dekompresi souborů **ZipDeflate** / **TarGzip** /  a zápisu do úložiště dat jímky založeného na souborech se ve výchozím nastavení extrahují soubory do složky: `<path specified in dataset>/<folder named as source compressed file>/` pomocí `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` [zdroje aktivity kopírování](#json-as-source) můžete určit, jestli se má název komprimovaných souborů zachovat jako struktura složek.| No.  |
| úroveň<br/>(*pod `compression`*) | Kompresní poměr <br>Povolené hodnoty jsou **optimální** nebo **nejrychlejší**.<br>- **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.<br>- **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu. Další informace najdete v tématu [úroveň komprese](/dotnet/api/system.io.compression.compressionlevel) . | Ne       |

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
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem a jímkou JSON.

Přečtěte si o tom, jak extrahovat data ze souborů JSON a mapovat je na úložiště nebo formát dat jímky nebo naopak z [mapování schématu](copy-activity-schema-and-type-mapping.md).

### <a name="json-as-source"></a>JSON jako zdroj

V části ***\* zdroj \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSource**. | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **nastavení čtení JSON** níže. | Ne       |
| storeSettings | Skupina vlastností, jak číst data z úložiště dat. Jednotlivé konektory založené na souborech mají v rámci své vlastní podporované nastavení pro čtení `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

Podporovaná **nastavení čtení JSON** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **JsonReadSettings**. | Ano      |
| compressionProperties | Skupina vlastností, jak dekomprimovat data pro daný Kompresní kodek. | Ne       |
| preserveZipFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `ZipDeflateReadSettings`*)  | Platí v případě, že je vstupní datová sada nakonfigurovaná s **ZipDeflate** kompresí. Určuje, jestli se má při kopírování zachovat název zdrojového souboru ZIP jako struktura složek.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje soubory unzip do `<path specified in dataset>/<folder named as source zip file>/` .<br>– Pokud je nastavené na **false**, Data Factory zapisuje soubory unzip přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech ZIP, abyste se vyhnuli neočekávanému chování.  | Ne |
| preserveCompressionFileNameAsFolder<br>(*v části `compressionProperties` -> `type` jako `TarGZipReadSettings` nebo `TarReadSettings`*) | Použije se, když je pro vstupní datovou sadu nakonfigurovaná komprese **TarGzip** / **tar** . Určuje, zda se má při kopírování zachovat zdrojový komprimovaný název souboru jako struktura složky.<br>-Když je nastavená **hodnota true (výchozí)**, Data Factory zapisuje dekomprimované soubory do `<path specified in dataset>/<folder named as source compressed file>/` . <br>– Pokud je nastavené na **false**, Data Factory zapisuje dekomprimované soubory přímo do `<path specified in dataset>` . Ujistěte se, že nemáte duplicitní názvy souborů v různých zdrojových souborech, abyste se vyhnuli neočekávanému chování. | Ne |

### <a name="json-as-sink"></a>JSON jako jímka

V části ***\* jímka \**** aktivity kopírování jsou podporovány následující vlastnosti.

| Vlastnost      | Popis                                                  | Povinné |
| ------------- | ------------------------------------------------------------ | -------- |
| typ          | Vlastnost Type zdroje aktivity kopírování musí být nastavená na **JSONSink**. | Ano      |
| formatSettings | Skupina vlastností Další informace najdete v tabulce **nastavení zápisu JSON** níže. | Ne       |
| storeSettings | Skupina vlastností, jak zapisovat data do úložiště dat. Každý konektor založený na souborech má vlastní podporované nastavení zápisu v rámci `storeSettings` . **Podrobnosti najdete v článku informace o konektoru – > část kopírování vlastností aktivity**. | Ne       |

Podporovaná **nastavení zápisu JSON** v rámci `formatSettings` :

| Vlastnost      | Popis                                                  | Povinné                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| typ          | Typ formatSettings musí být nastaven na hodnotu **JsonWriteSettings**. | Ano                                                   |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou: **setOfObjects** (řádky JSON) a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Při kopírování dat ze souborů JSON může aktivita kopírování automaticky detekovat a analyzovat následující vzory souborů JSON. Při zápisu dat do souborů JSON můžete nakonfigurovat vzor souboru pro jímku aktivity kopírování.

- **Typ I: setOfObjects**

    Každý soubor obsahuje jeden objekt, řádky JSON nebo zřetězené objekty.

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

    * **Řádky JSON (výchozí pro jímku)**

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

V části mapování toků dat můžete číst a zapisovat do formátu JSON v následujících úložištích dat: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Vlastnosti zdroje

V níže uvedené tabulce jsou uvedeny vlastnosti podporované zdrojem JSON. Tyto vlastnosti můžete upravit na kartě **Možnosti zdrojového kódu** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Cesty k zástupným kartám | Budou zpracovány všechny soubory, které odpovídají zástupné cestě. Přepíše složku a cestu k souboru nastavenou v datové sadě. | ne | Řetězec [] | wildcardPaths |
| Kořenová cesta oddílu | Pro souborová data, která jsou rozdělená na oddíly, můžete zadat kořenovou cestu oddílu, aby bylo možné číst rozdělené složky jako sloupce. | ne | Řetězec | partitionRootPath |
| Seznam souborů | Určuje, zda váš zdroj odkazuje na textový soubor se seznamem souborů, které se mají zpracovat. | ne | `true` nebo `false` | fileList |
| Sloupec, ve kterém se má uložit název souboru | Vytvoří nový sloupec s názvem a cestou ke zdrojovému souboru. | ne | Řetězec | rowUrlColumn |
| Po dokončení | Odstraní nebo přesune soubory po zpracování. Cesta k souboru začíná z kořene kontejneru | ne | Odstranit: `true` nebo `false` <br> Pøesunout `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Filtrovat podle poslední změny | Zvolit filtrování souborů podle toho, kdy se naposledy změnily | ne | Timestamp | modifiedAfter <br> modifiedBefore |
| Jeden dokument | Mapování toků dat čtení jednoho dokumentu JSON z každého souboru | ne | `true` nebo `false` | singleDocument |
| Názvy sloupců bez uvozovek | Pokud je vybraná možnost **názvy sloupců bez uvozovek** , mapování toků dat přečte sloupce JSON, které nejsou obklopené uvozovkami. | ne | `true` nebo `false` |  unquotedColumnNames |
| Obsahuje komentáře | Vyberte **má komentáře** , pokud data JSON obsahují komentáře ve stylu C nebo C++. | ne | `true` nebo `false` | asComments |
| Jednoduché v uvozovkách | Přečte sloupce JSON, které nejsou obklopené uvozovkami. | ne | `true` nebo `false` | singleQuoted |
| Zpětné lomítko – řídicí | Vyberte **zpětné lomítko** , pokud se k řídicím znakům v datech JSON použijí zpětná lomítka. | ne | `true` nebo `false` | backslashEscape |
| Nenalezeny žádné soubory | Pokud je nastaveno na true, chyba není vyvolána, pokud nebyly nalezeny žádné soubory. | ne | `true` nebo `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>Možnosti formátu zdroje

Použití datové sady JSON jako zdroje v toku dat umožňuje nastavit pět dalších nastavení. Tato nastavení se dají najít v rámci souhlasu **Nastavení JSON** na kartě **zdrojové možnosti** . Pro nastavení **formuláře dokumentu** můžete vybrat jeden z **jednoho dokumentu**, **dokumentovat na řádek**  a **pole typů dokumentů** .

![Nastavení JSON](media/data-flow/json-settings.png "Nastavení v souboru JSON")

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
> [!NOTE]
> Pokud toky dat vyvolávají při zobrazení náhledu dat JSON chybu informující "corrupt_record", je pravděpodobný, že vaše data obsahují jeden dokument v souboru JSON. Nastavení jednotlivého dokumentu by mělo tuto chybu vymazat.

#### <a name="unquoted-column-names"></a>Názvy sloupců bez uvozovek

Pokud je vybraná možnost **názvy sloupců bez uvozovek** , mapování toků dat přečte sloupce JSON, které nejsou obklopené uvozovkami. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Obsahuje komentáře

Vyberte **má komentáře** , pokud data JSON obsahují komentáře ve stylu C nebo C++.

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

Vyberte **zpětné lomítko** , které se řídí zpětným lomítkem, aby se v datech JSON řídicí znaky používaly.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Vlastnosti jímky

V níže uvedené tabulce jsou uvedeny vlastnosti, které jímka JSON podporuje. Tyto vlastnosti můžete upravit na kartě **Nastavení** .

| Název | Popis | Povinné | Povolené hodnoty | Vlastnost skriptu toku dat |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Vymazat složku | Pokud před zápisem není cílová složka smazána | ne | `true` nebo `false` | zkrátit |
| Možnost názvu souboru | Formát názvů zapsaných dat. Ve výchozím nastavení je jeden soubor na oddíl ve formátu `part-#####-tid-<guid>` | ne | Vzor: řetězec <br> Na oddíl: řetězec [] <br> Jako data ve sloupci: String <br> Výstup do jednoho souboru: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Vytváření struktur JSON v odvozeném sloupci

Do toku dat můžete přidat složitý sloupec prostřednictvím Tvůrce výrazu odvozeného sloupce. V transformaci odvozeného sloupce přidejte nový sloupec a otevřete Tvůrce výrazů kliknutím na modrý rámeček. Chcete-li nastavit sloupec jako složitý, můžete ručně zadat strukturu JSON nebo použít uživatelské prostředí k interaktivnímu přidání podsloupců.

#### <a name="using-the-expression-builder-ux"></a>Použití uživatelského rozhraní Tvůrce výrazů

V podokně výstupní schéma umístěte ukazatel myši na sloupec a klikněte na ikonu se symbolem plus. Vyberte možnost **Přidat dílčí sloupec** a nastavte si sloupec jako složitý typ.

![Přidat Podsloupec](media/data-flow/derive-add-subcolumn.png "Přidat Podsloupec")

Můžete přidat další sloupce a podsloupce stejným způsobem. Pro každé nekomplikované pole lze přidat výraz do pravé části editoru výrazů.

![Přidat složitý sloupec](media/data-flow/derive-complex-column.png "Přidání sloupců")

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

## <a name="next-steps"></a>Další kroky

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Mapování toku dat](concepts-data-flow-overview.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
