---
title: Podporované formáty souborů v Azure Data Factory (starší verze)
description: Toto téma popisuje formáty souborů a kompresní kódy, které jsou podporovány konektory na bázi souborů v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: b3241bc16c0613189faa169032632303788dac3e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634126"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Podporované formáty souborů a kompresní kodeky v Azure Data Factory (starší verze)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Tento článek se týká následujících konektorů: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)a [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory představil nový model datové sady založené na formátu, viz odpovídající článek o formátu s podrobnostmi: <br>- [Formát Avro](format-avro.md)<br>- [Binární formát](format-binary.md)<br>- [Textový formát s oddělovači](format-delimited-text.md)<br>- [Formát JSON](format-json.md)<br>- [Formát ORC](format-orc.md)<br>- [Formát Parquet](format-parquet.md)<br>Konfigurace REST, které jsou uvedené v tomto článku, se pořád podporují, protože jsou pro zpětnou compabitility. Navrhnete použití nového modelu. 

## <a name="text-format-legacy"></a><a name="text-format"></a> Formát textu (starší verze)

>[!NOTE]
>Naučte se nový model z článku [oddělený text format](format-delimited-text.md) . Následující konfigurace pro datovou sadu datového úložiště založeného na souborech je stále podporovaná tak, jak je to pro zpětnou compabitility. Navrhnete použití nového modelu.

Pokud chcete číst z textového souboru nebo zapisovat do textového souboru, nastavte `type` vlastnost v `format` části datové sady na **TextFormat** . Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu TextFormat](#textformat-example).

| Vlastnost | Popis | Povolené hodnoty | Povinné |
| --- | --- | --- | --- |
| columnDelimiter |Znak, který slouží k oddělení sloupců v souboru. Můžete uvažovat o použití zřídka netisknutelného znaku, který ve vašich datech možná neexistuje. Zadejte například "\u0001", který představuje začátek záhlaví (SOH). |Je povolený jenom jeden znak. **Výchozí** hodnota je **čárka (,)** . <br/><br/>Chcete-li použít znak Unicode, přečtěte si text [znaků Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) a získejte pro něj odpovídající kód. |Ne |
| rowDelimiter |Znak, který slouží k oddělení řádků v souboru. |Je povolený jenom jeden znak. **Výchozí** hodnotou pro čtení může být libovolná z těchto hodnot: **[\r\n, \r, \n]** a pro zápis hodnota **\r\n** . |Ne |
| escapeChar |Speciální znak, který slouží k potlačení oddělovače sloupců v obsahu vstupního souboru. <br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale v textu chcete znak čárky (příklad: "Hello, World"), můžete jako řídicí znak definovat "$" a ve zdroji použít řetězec "Hello $, World". |Ne |
| quoteChar |Znak, který slouží k uvození textového řetězce. Oddělovače sloupců a řádků uvnitř znaků uvozovek budou považované za součást hodnoty příslušného řetězce. Tato vlastnost se vztahuje na vstupní i výstupní datové sady.<br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: <Hello, world>), můžete jako znak uvozovek definovat " (dvojité uvozovky) a použít ve zdroji řetězec "Hello$, world". |Ne |
| nullValue |Jeden nebo několik znaků, které se používají jako reprezentace hodnoty Null. |Jeden nebo několik znaků. **Výchozí** hodnoty jsou **\N a NULL** pro čtení a **\N** pro zápis. |Ne |
| encodingName |Zadejte název kódování. |Platný název kódování. Další informace najdete v tématu [Vlastnost Encoding.EncodingName](/dotnet/api/system.text.encoding). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8** . |Ne |
| firstRowAsHeader |Určuje, jestli se má první řádek považovat za záhlaví. U vstupní datové sady Data Factory načítá první řádek jako záhlaví. U výstupní datové sady Data Factory zapisuje první řádek jako záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Pravda<br/><b>False (výchozí)</b> |Ne |
| skipLineCount |Označuje počet **neprázdných** řádků, které se mají přeskočit při čtení dat ze vstupních souborů. Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Ne |
| treatEmptyAsNull |Určuje, jestli se při čtení dat ze vstupního souboru má prázdný řetězec nebo řetězec s hodnotou null považovat za hodnotu null. |**True (výchozí)**<br/>Nepravda |Ne |

### <a name="textformat-example"></a>Příklad typu TextFormat

V následující definici JSON pro datovou sadu jsou zadány některé volitelné vlastnosti.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Pokud chcete místo `quoteChar` použít `escapeChar`, nahraďte řádek s `quoteChar` touto hodnotou escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Scénáře použití firstRowAsHeader a skipLineCount

* Kopírujete z nesouborového zdroje do textového souboru a chcete přidat řádek záhlaví obsahující metadata schématu (třeba schéma SQL). V tomto scénáři zadejte ve vstupní sadě `firstRowAsHeader` jako true.
* Kopírujete text z textového souboru obsahujícího řádek záhlaví do nesouborové jímky a chcete tento řádek vynechat. Zadejte ve vstupní sadě `firstRowAsHeader` jako true.
* Kopírujete text z textového souboru a chcete vynechat několik prvních řádků, které neobsahují data ani informace záhlaví. Zadáním `skipLineCount` určete, kolik řádků se má přeskočit. Pokud zbytek souboru obsahuje řádek záhlaví, můžete také zadat `firstRowAsHeader`. Pokud je zadaný parametr `skipLineCount` i `firstRowAsHeader`, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví.

## <a name="json-format-legacy"></a><a name="json-format"></a> Formát JSON (starší verze)

>[!NOTE]
>Přečtěte si článek o novém modelu z [formátu JSON](format-json.md) . Následující konfigurace pro datovou sadu datového úložiště založeného na souborech je stále podporovaná tak, jak je to pro zpětnou compabitility. Navrhnete použití nového modelu.

Pokud chcete **Importovat/exportovat soubor JSON tak, jak je do nebo z Azure Cosmos DB** , přečtěte si část import/export dokumentů JSON v článku [přesun dat do a z Azure Cosmos DB](connector-azure-cosmos-db.md) .

Chcete-li analyzovat soubory JSON nebo zapsat data ve formátu JSON, nastavte `type` vlastnost v `format` oddílu na **JsonFormat** . Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example).

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects** . **Výchozí hodnota** je **setOfObjects** . Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |
| jsonNodeReference | Pokud chcete iterovat a extrahovat data z objektů uvnitř pole se stejným vzorem, zadejte pro toto pole cestu JSON. Tato vlastnost je podporována pouze při kopírování dat **ze** souborů JSON. | Ne |
| jsonPathDefinition | Zadejte výraz cesty JSON pro každé mapování sloupců s vlastním názvem sloupce (s počátečním malým písmenem). Tato vlastnost je podporována, pouze pokud kopírujete data **ze** souborů JSON a můžete extrahovat data z objektu nebo pole. <br/><br/> U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `jsonNodeReference` začněte elementem pole. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example). | Ne |
| encodingName |Zadejte název kódování. Seznam platných názvů kódování najdete v tématu věnovaném vlastnosti [Encoding.EncodingName](/dotnet/api/system.text.encoding). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je: **UTF-8** . |Ne |
| nestingSeparator |Znak, který se používá k oddělení úrovní vnoření. Výchozí hodnota je tečka (.). |Ne |

>[!NOTE]
>Pro případ křížového použití dat v poli do více řádků (například případ 1-> Sample 2 v [příkladech JsonFormat](#jsonformat-example)) můžete zvolit pouze rozšíření jednoho pole pomocí vlastnosti `jsonNodeReference` .

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může analyzovat následující vzory souborů JSON:

- **Typ I: setOfObjects**

    Každý soubor obsahuje jeden objekt nebo několik objektů, které jsou zřetězené nebo oddělené řádkem. Když je pro výstupní datovou sadu vybraná tato možnost, aktivita kopírování vytvoří jeden soubor JSON, ve kterém je každý objekt na jednom řádku (oddělení řádkem).

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

### <a name="jsonformat-example"></a>Příklad typu JsonFormat

**Případ 1: Kopírování dat ze souborů JSON**

**Ukázka 1: Extrakce dat z objektu a pole**

V této ukázce očekáváte, že jeden kořenový objekt JSON se mapuje na jeden záznam v tabulkovém výsledku. Pokud máte soubor JSON s následujícím obsahem:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

a chcete ho zkopírovat do tabulky Azure SQL v následujícím formátu a přitom data uvnitř pole linearizovat, a to extrakcí dat z objektů i z pole:

| ID | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Vstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). A konkrétně:

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný** . Další informace najdete v tématu [mapování sloupců zdrojové datové sady na cílové sloupce datové sady](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. Chcete-li kopírovat data z pole, lze použít `array[x].property` k extrakci hodnoty dané vlastnosti z `xth` objektu nebo můžete použít `array[*].property` k vyhledání hodnoty z libovolného objektu, který tuto vlastnost obsahuje.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Ukázka 2: Křížové použití více objektů se stejným vzorkem z pole**

V této ukázce očekáváte, že transformujete jeden kořenový objekt JSON do několika záznamů v tabulkovém výsledku. Pokud máte soubor JSON s následujícím obsahem:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

a chcete ho zkopírovat do tabulky Azure SQL v následujícím formátu a přitom data uvnitř pole linearizovat a propojit je se společnými kořenovými informacemi:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Vstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). A konkrétně:

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný** . Další informace najdete v tématu [mapování sloupců zdrojové datové sady na cílové sloupce datové sady](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Určuje, že se mají iterovat a extrahovat data z objektů se stejným vzorem v **poli Array** `orderlines` .
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. V tomto příkladu, `ordernumber` , `orderdate` a `city` jsou pod kořenovým objektem s cestou JSON začínající `$.` , zatímco `order_pd` a `order_price` jsou definovány s cestou odvozenou z prvku pole bez `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Je třeba počítat s následujícím:**

* Pokud v datové sadě Data Factory nejsou `structure` a `jsonPathDefinition` definované, aktivita kopírování detekuje schéma z prvního objektu a celý objekt linearizuje.
* Pokud vstup JSON obsahuje pole, aktivita kopírování ve výchozím nastavení převede celou hodnotu pole na řetězec. Můžete si vybrat, jestli z něj budete data extrahovat pomocí `jsonNodeReference` a/nebo `jsonPathDefinition`, nebo jestli ho přeskočíte tím, že ho nezadáte v `jsonPathDefinition`.
* Pokud je několik duplicitních názvů na stejné úrovni, aktivita kopírování vybere poslední z nich.
* V názvech vlastností se rozlišují velká a malá písmena. Vlastnosti se stejným názvem, ale různým použitím velkých a malých písmen, se budou považovat za různé.

**Příklad 2: Zápis dat do souboru JSON**

Pokud máte v SQL Database následující tabulku:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

u každého záznamu očekáváte, že budete zapisovat do objektu JSON v následujícím formátu:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Výstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). Přesněji řečeno, `structure` oddíl definuje názvy přizpůsobených vlastností v cílovém souboru `nestingSeparator` (výchozí je ".") slouží k identifikaci vnořené vrstvy od názvu. Pokud nechcete měnit název vlastnosti v porovnání se zdrojovým názvem sloupce nebo vnořit některé z vlastností, je tento oddíl **nepovinný** .

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a> Formát Parquet (starší verze)

>[!NOTE]
>Seznamte se s novým modelem z článku [Parquet Format](format-parquet.md) . Následující konfigurace pro datovou sadu datového úložiště založeného na souborech je stále podporovaná tak, jak je to pro zpětnou compabitility. Navrhnete použití nového modelu.

Pokud chcete analyzovat soubory Parquet nebo zapisovat data ve formátu Parquet, nastavte vlastnost `format` `type` na hodnotu **Format** . V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (mapa, seznam).
* Prázdné znaky v názvu sloupce nejsou podporovány.
* Soubory Parquet mají tyto možnosti komprese: NONE, SNAPPY, GZIP a LZO. Data Factory podporuje čtení dat ze souboru Parquet v některém z těchto komprimovaných formátů s výjimkou LZO – používá ke čtení dat Kompresní kodek v metadatech. Při zápisu do souboru Parquet ale Data Factory využívá možnost SNAPPY, která je pro formát Parquet výchozí. V současnosti toto chování nejde potlačit.

> [!IMPORTANT]
> Pro kopii, která je oprávněná pro místní hostování Integration Runtime například mezi místními a cloudovým úložištěm dat, pokud soubory Parquet nekopírujete **tak, jak jsou** , je nutné na počítač IR nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** . Další podrobnosti najdete v následujícím článku.

Pro kopírování běžící v prostředí IR v místním prostředí s Parquet serializací/deserializace vyhledá ADF modul runtime Java tím, že se nejprve zkontroluje registr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pro JRE, pokud se nenajde, druhá kontroluje proměnnou systému *`JAVA_HOME`* pro OpenJDK.

- **Použití JRE** : 64-bit IR vyžaduje 64-bit JRE. Můžete ho najít [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Použití OpenJDK** : podporuje se od verze IR 3,13. Zabalit jvm.dll se všemi ostatními požadovanými sestaveními OpenJDK do místně hostovaného počítače IR a nastavit proměnnou prostředí systému JAVA_HOME odpovídajícím způsobem.

>[!TIP]
>Pokud kopírujete data do nebo z formátu Parquet pomocí Integration Runtime v místním prostředí a omylem zaznamenáte chybu při vyvolání Java, zpráva: **Java. lang. OutOfMemoryError: prostor haldy Java** ", můžete přidat proměnnou prostředí `_JAVA_OPTIONS` v počítači, který je hostitelem prostředí IR v místním prostředí, a upravit tak minimální/maximální velikost haldy pro JVM, abyste mohli takovou kopii provést, a pak znovu spustit kanál.

![Nastavení velikosti haldy JVM v místním prostředí IR](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavte proměnnou `_JAVA_OPTIONS` s hodnotou `-Xms256m -Xmx16g` . Příznak `Xms` Určuje počáteční fond přidělení paměti pro prostředí Java Virtual Machine (JVM), zatímco `Xmx` Určuje maximální fond přidělení paměti. To znamená, že JVM bude spuštěn s `Xms` velikostí paměti a bude moci využít maximální `Xmx` množství paměti. Ve výchozím nastavení ADF používá minimální 64 MB a maximální 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapování datových typů pro soubory Parquet

| Typ dat interim Data Factory | Parquet primitivní typ | Původní typ Parquet (deserializace) | Parquet – původní typ (serializace) |
|:--- |:--- |:--- |:--- |
| Logická hodnota | Logická hodnota | N/A | N/A |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binární | UInt64 | Decimal |
| Jednoduché | Float | N/A | N/A |
| dvojité | dvojité | N/A | N/A |
| Decimal | Binární | Decimal | Decimal |
| Řetězec | Binární | UTF | UTF |
| DateTime | Int96 | N/A | N/A |
| TimeSpan | Int96 | N/A | N/A |
| DateTimeOffset | Int96 | N/A | N/A |
| ByteArray | Binární | N/A | N/A |
| Identifikátor GUID | Binární | UTF | UTF |
| Char | Binární | UTF | UTF |
| CharArray | Nepodporováno | N/A | N/A |

## <a name="orc-format-legacy"></a><a name="orc-format"></a> Formát ORC (starší verze)

>[!NOTE]
>Seznamte se s novým modelem z článku [ORC Format](format-orc.md) . Následující konfigurace pro datovou sadu datového úložiště založeného na souborech je stále podporovaná tak, jak je to pro zpětnou compabitility. Navrhnete použití nového modelu.

Pokud chcete analyzovat soubory ORC nebo zapisovat data ve formátu ORC, nastavte vlastnost `format` `type` na hodnotu **OrcFormat** . V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "OrcFormat"
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (struktura, mapování, seznam, SJEDNOCENí).
* Prázdné znaky v názvu sloupce nejsou podporovány.
* Soubory ORC mají tři [možnosti komprese](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

> [!IMPORTANT]
> Pro kopii, která je oprávněná pro místní hostování Integration Runtime například mezi místními a cloudovým úložištěm dat, pokud soubory ORC nekopírujete **tak, jak jsou** , je nutné na počítač IR nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** . Další podrobnosti najdete v následujícím článku.

Pro kopírování běžící v prostředí IR v místním prostředí s ORC serializací/deserializace vyhledá ADF modul runtime Java tím, že se nejprve zkontroluje registr *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* pro JRE, pokud se nenajde, druhá kontroluje proměnnou systému *`JAVA_HOME`* pro OpenJDK.

- **Použití JRE** : 64-bit IR vyžaduje 64-bit JRE. Můžete ho najít [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Použití OpenJDK** : podporuje se od verze IR 3,13. Zabalit jvm.dll se všemi ostatními požadovanými sestaveními OpenJDK do místně hostovaného počítače IR a nastavit proměnnou prostředí systému JAVA_HOME odpovídajícím způsobem.

### <a name="data-type-mapping-for-orc-files"></a>Mapování datových typů pro soubory ORC

| Typ dat interim Data Factory | Typy ORC |
|:--- |:--- |
| Logická hodnota | Logická hodnota |
| SByte | Byte |
| Byte | Dostatečná |
| Int16 | Dostatečná |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Dlouhou |
| Int64 | Dlouhou |
| UInt64 | Řetězec |
| Jednoduché | Float |
| dvojité | dvojité |
| Decimal | Decimal |
| Řetězec | Řetězec |
| DateTime | Timestamp |
| DateTimeOffset | Timestamp |
| TimeSpan | Timestamp |
| ByteArray | Binární |
| Identifikátor GUID | Řetězec |
| Char | Char (1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a> Formát AVRO (starší verze)

>[!NOTE]
>Seznamte se s novým modelem z článku [Avro Format](format-avro.md) . Následující konfigurace pro datovou sadu datového úložiště založeného na souborech je stále podporovaná tak, jak je to pro zpětnou compabitility. Navrhnete použití nového modelu.

Pokud chcete analyzovat soubory Avro nebo zapisovat data ve formátu Avro, nastavte vlastnost `format` `type` na hodnotu **AvroFormat** . V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "AvroFormat",
}
```

Pokud chcete v tabulce podregistru použít formát Avro, můžete se podívat na [kurz Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Je třeba počítat s následujícím:

* [Komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) se nepodporují (záznamy, výčty, pole, mapy, sjednocení a pevná).

## <a name="compression-support-legacy"></a><a name="compression-support"></a> Podpora komprese (starší verze)

Azure Data Factory v průběhu kopírování podporuje komprimaci a dekomprimaci dat. Když zadáte `compression` vlastnost ve vstupní datové sadě, aktivita kopírování přečte komprimovaná data ze zdroje a dekomprimuje je. když zadáte vlastnost do výstupní datové sady, komprimace aktivity kopírování pak zapíše data do jímky. Tady je několik ukázkových scénářů:

* Načte komprimovaná data GZIP z objektu blob Azure, dekomprimuje je a zapíše data výsledků do Azure SQL Database. Nadefinujete vstupní datovou sadu Azure Blob s `compression` `type` vlastností jako gzip.
* Načtěte data z textového souboru z místního systému souborů, Zkomprimujte ho pomocí formátu GZip a zapište komprimovaná data do objektu blob Azure. Nadefinujete výstupní datovou sadu Azure Blob s `compression` `type` vlastností jako gzip.
* Přečtěte si soubor. zip ze serveru FTP, dekomprimujte ho, aby se soubory dostaly do všech souborů, a Azure Data Lake Store. Nadefinujete vstupní datovou sadu FTP s `compression` `type` vlastností jako ZipDeflate.
* Přečtěte si komprimovaná data z objektu blob Azure, dekomprimujete je, Zkomprimujte je pomocí BZIP2 a zapište výsledná data do objektu blob Azure. Definujete vstupní datovou sadu Azure Blob s `compression` `type` nastavením na gzip a výstupní datovou sadu s `compression` `type` nastavením na bzip2.

Chcete-li určit kompresi pro datovou sadu, použijte vlastnost **Compression** v datové sadě JSON jako v následujícím příkladu:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

Oddíl **Compression** má dvě vlastnosti:

* **Typ:** Kompresní kodek, který může být **gzip** , **Deflate** , **bzip2** nebo **ZipDeflate** . Poznámka: při použití aktivity kopírování k dekompresi souborů ZipDeflate a zápisu do úložiště dat jímky založeného na souborech se soubory extrahují do složky: `<path specified in dataset>/<folder named as source zip file>/` .
* **Level (úroveň):** kompresní poměr, který může být **optimální** nebo **nejrychlejší** .

  * **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.
  * **Optimální** : komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu.

    Další informace najdete v tématu [úroveň komprese](/dotnet/api/system.io.compression.compressionlevel) .

> [!NOTE]
> Nastavení komprese nejsou podporována pro data v **AvroFormat** , **OrcFormat** nebo **ParquetFormat** . Při čtení souborů v těchto formátech Data Factory detekuje a používá Kompresní kodek v metadatech. Při zápisu do souborů v těchto formátech Data Factory zvolí výchozí Kompresní kodek pro tento formát. Například ZLIB pro OrcFormat a přichycení pro ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nepodporované typy souborů a formáty komprese

K transformaci souborů, které nejsou podporované, můžete použít funkce rozšíření Azure Data Factory.
Mezi dvě možnosti patří Azure Functions a vlastní úkoly pomocí Azure Batch.

Můžete vidět ukázku, která používá funkci Azure k [extrakci obsahu souboru tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Další informace najdete v tématu [aktivita Azure Functions](./control-flow-azure-function-activity.md).

Tuto funkci můžete také vytvořit pomocí vlastní aktivity dotnet. Další informace jsou k dispozici [zde](./transform-data-using-dotnet-custom-activity.md) .

## <a name="next-steps"></a>Další kroky

Přečtěte si nejnovější podporované formáty souborů a komprimace z [podporovaných formátů souborů a kompresí](supported-file-formats-and-compression-codecs.md).