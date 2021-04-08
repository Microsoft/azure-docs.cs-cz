---
title: Formáty souborů a komprese v Azure Data Factory
description: Přečtěte si o formátech souborů podporovaných nástrojem Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7b53d9cd64c50d8305714878324dd355eb6d1840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368723"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Formáty souborů a komprese podporované nástrojem Azure Data Factory
*Toto téma se vztahuje na následující konektory: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), [Azure Data Lake Store](data-factory-azure-datalake-connector.md), [File System](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [http](data-factory-http-connector.md)a [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [podporované formáty souborů a kompresní kodeky v Data Factory](../supported-file-formats-and-compression-codecs.md).

Azure Data Factory podporuje následující typy formátů souborů:

* [Formát textu](#text-format)
* [Formát JSON](#json-format)
* [Formát Avro](#avro-format)
* [Formát ORC](#orc-format)
* [Formát Parquet](#parquet-format)

## <a name="text-format"></a>Formát textu
Pokud chcete číst z textového souboru nebo zapisovat do textového souboru, nastavte `type` vlastnost v `format` části datové sady na **TextFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu TextFormat](#textformat-example).

| Vlastnost | Popis | Povolené hodnoty | Vyžadováno |
| --- | --- | --- | --- |
| columnDelimiter |Znak, který slouží k oddělení sloupců v souboru. Můžete uvažovat o použití zřídka netisknutelného znaku, který ve vašich datech pravděpodobně neexistuje. Zadejte například "\u0001", který představuje začátek záhlaví (SOH). |Je povolený jenom jeden znak. **Výchozí** hodnota je **čárka (,)**. <br/><br/>Chcete-li použít znak Unicode, přečtěte si text [znaků Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) a získejte pro něj odpovídající kód. |No |
| rowDelimiter |Znak, který slouží k oddělení řádků v souboru. |Je povolený jenom jeden znak. **Výchozí** hodnotou pro čtení může být libovolná z těchto hodnot: **[\r\n, \r, \n]** a pro zápis hodnota **\r\n**. |No |
| escapeChar |Speciální znak, který slouží k potlačení oddělovače sloupců v obsahu vstupního souboru. <br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale v textu chcete znak čárky (příklad: "Hello, World"), můžete jako řídicí znak definovat "$" a ve zdroji použít řetězec "Hello $, World". |No |
| quoteChar |Znak, který slouží k uvození textového řetězce. Oddělovače sloupců a řádků uvnitř znaků uvozovek budou považované za součást hodnoty příslušného řetězce. Tato vlastnost se vztahuje na vstupní i výstupní datové sady.<br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: <Hello, world>), můžete jako znak uvozovek definovat " (dvojité uvozovky) a použít ve zdroji řetězec "Hello$, world". |No |
| nullValue |Jeden nebo několik znaků, které se používají jako reprezentace hodnoty Null. |Jeden nebo několik znaků. **Výchozí** hodnoty jsou **\N a NULL** pro čtení a **\N** pro zápis. |No |
| encodingName |Zadejte název kódování. |Platný název kódování. Další informace najdete v tématu [Vlastnost Encoding.EncodingName](/dotnet/api/system.text.encoding). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |No |
| firstRowAsHeader |Určuje, jestli se má první řádek považovat za záhlaví. U vstupní datové sady Data Factory načítá první řádek jako záhlaví. U výstupní datové sady Data Factory zapisuje první řádek jako záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Ano<br/><b>False (výchozí)</b> |No |
| skipLineCount |Určuje počet řádků, které se při čtení dat ze vstupních souborů mají přeskočit. Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |No |
| treatEmptyAsNull |Určuje, jestli se při čtení dat ze vstupního souboru má prázdný řetězec nebo řetězec s hodnotou null považovat za hodnotu null. |**True (výchozí)**<br/>Ne |No |

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

## <a name="json-format"></a>Formát JSON
Chcete **-li importovat nebo exportovat soubor JSON tak, jak je do nebo z Azure Cosmos DB**, přečtěte si část [Dokumenty JSON import/export](data-factory-azure-documentdb-connector.md#importexport-json-documents) v tématu [přesun dat do/z Azure Cosmos DB](data-factory-azure-documentdb-connector.md) článku.

Chcete-li analyzovat soubory JSON nebo zapsat data ve formátu JSON, nastavte `type` vlastnost v `format` oddílu na **JsonFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example).

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |No |
| jsonNodeReference | Pokud chcete iterovat a extrahovat data z objektů uvnitř pole se stejným vzorem, zadejte pro toto pole cestu JSON. Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. | No |
| jsonPathDefinition | Zadejte výraz cesty JSON pro každé mapování sloupců s vlastním názvem sloupce (s počátečním malým písmenem). Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. Můžete extrahovat data z objektu nebo pole. <br/><br/> U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `jsonNodeReference` začněte elementem pole. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example). | No |
| encodingName |Zadejte název kódování. Seznam platných názvů kódování najdete v tématu věnovaném vlastnosti [Encoding.EncodingName](/dotnet/api/system.text.encoding). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je: **UTF-8**. |No |
| nestingSeparator |Znak, který se používá k oddělení úrovní vnoření. Výchozí hodnota je tečka (.). |No |

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

Při kopírování dat ze souborů JSON se podívejte na následující dvě ukázky. Obecné body, které se mají poznamenat:

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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další podrobnosti najdete v části [mapování sloupců zdrojové datové sady na cílové datové sady](data-factory-map-columns.md) .
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. Chcete-li kopírovat data z pole, můžete použít **pole [x]. Property** k extrakci hodnoty dané vlastnosti z objektu Xth nebo můžete použít **pole [*]. Property** k nalezení hodnoty z libovolného objektu, který tuto vlastnost obsahuje.

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

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

Vstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). A konkrétně:

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další podrobnosti najdete v části [mapování sloupců zdrojové datové sady na cílové datové sady](data-factory-map-columns.md) .
- `jsonNodeReference` určuje, že se budou iterovat a extrahovat data z objektů se stejným vzorem v rámci **pole** orderlines.
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. V tomto příkladu jsou položky ordernumber, orderdate a city v kořenovém objektu s cestou JSON, která začíná řetězcem „$.“. Oproti tomu order_pd a order_price jsou definované pomocí cesty odvozené z elementu pole bez řetězce „$.“.

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

Výstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). Přesněji řečeno, `structure` oddíl definuje názvy přizpůsobených vlastností v cílovém souboru `nestingSeparator` (výchozí je ".") slouží k identifikaci vnořené vrstvy od názvu. Pokud nechcete měnit název vlastnosti v porovnání se zdrojovým názvem sloupce nebo vnořit některé z vlastností, je tento oddíl **nepovinný**.

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

## <a name="avro-format"></a>Formát AVRO
Pokud chcete analyzovat soubory Avro nebo zapisovat data ve formátu Avro, nastavte vlastnost `format` `type` na hodnotu **AvroFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "AvroFormat",
}
```

Pokud chcete v tabulce podregistru použít formát Avro, můžete se podívat na [kurz Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Je třeba počítat s následujícím:  

* [Komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) se nepodporují (záznamy, výčty, pole, mapy, sjednocení a pevná).

## <a name="orc-format"></a>Formát ORC
Pokud chcete analyzovat soubory ORC nebo zapisovat data ve formátu ORC, nastavte vlastnost `format` `type` na hodnotu **OrcFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Pokud soubory ORC mezi místním prostředím a cloudovým úložištěm dat nekopírujete **tak, jak jsou**, musíte si na počítači brány nainstalovat prostředí JRE 8 (Java Runtime Environment). 64bitová verze brány vyžaduje 64bitové prostředí JRE a 32bitová verze brány vyžaduje 32bitové prostředí JRE. Obě verze najdete [tady](https://go.microsoft.com/fwlink/?LinkId=808605). Vyberte z nich tu vhodnou.
>
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (STRUCT, MAP, LIST, UNION).
* Soubory ORC mají tři [možnosti komprese](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

## <a name="parquet-format"></a>Formát Parquet
Pokud chcete analyzovat soubory Parquet nebo zapisovat data ve formátu Parquet, nastavte vlastnost `format` `type` na hodnotu **Format**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Pokud soubory Parquet mezi místním prostředím a cloudovým úložištěm dat nekopírujete **tak, jak jsou**, musíte si na počítači brány nainstalovat prostředí JRE 8 (Java Runtime Environment). 64bitová verze brány vyžaduje 64bitové prostředí JRE a 32bitová verze brány vyžaduje 32bitové prostředí JRE. Obě verze najdete [tady](https://go.microsoft.com/fwlink/?LinkId=808605). Vyberte z nich tu vhodnou.
>
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (MAP, LIST).
* Soubory Parquet mají tyto možnosti komprese: NONE, SNAPPY, GZIP a LZO. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru Parquet ale Data Factory využívá možnost SNAPPY, která je pro formát Parquet výchozí. V současnosti toto chování nejde potlačit.

## <a name="compression-support"></a>Kompresní podpora
Zpracování velkých datových sad může způsobit kritické body v/v a v síti. Proto komprimovaná data v úložištích neumožňují zrychlit přenos dat přes síť a šetřit místo na disku, ale také přináší významná vylepšení výkonu při zpracování velkých objemů dat. V současné době je komprese podporovaná pro souborové úložiště dat, jako je Azure Blob nebo místní systém souborů.  

Chcete-li určit kompresi pro datovou sadu, použijte vlastnost **Compression** v datové sadě JSON jako v následujícím příkladu:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Předpokládejme, že ukázková datová sada se používá jako výstup aktivity kopírování, aktivita kopírování komprimuje výstupní data pomocí kodeku GZIP pomocí optimálního poměru a pak zapisuje komprimovaná data do souboru s názvem pagecounts.csv. gz v Blob Storage Azure.

> [!NOTE]
> Nastavení komprese nejsou podporována pro data v **AvroFormat**, **OrcFormat** nebo **ParquetFormat**. Při čtení souborů v těchto formátech Data Factory detekuje a používá Kompresní kodek v metadatech. Při zápisu do souborů v těchto formátech Data Factory zvolí výchozí Kompresní kodek pro tento formát. Například ZLIB pro OrcFormat a přichycení pro ParquetFormat.   

Oddíl **Compression** má dvě vlastnosti:  

* **Typ:** Kompresní kodek, který může být **gzip**, **Deflate**, **bzip2** nebo **ZipDeflate**.  
* **Level (úroveň):** kompresní poměr, který může být **optimální** nebo **nejrychlejší**.

  * **Nejrychlejší:** Kompresní operace by se měla dokončit co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.
  * **Optimální**: komprese by měla být optimálně komprimována i v případě, že dokončení operace trvá delší dobu.

    Další informace najdete v tématu [úroveň komprese](/dotnet/api/system.io.compression.compressionlevel) .

Když zadáte `compression` vlastnost ve formátu JSON vstupní datové sady, kanál může číst komprimovaná data ze zdroje a když zadáte vlastnost do výstupní datové sady JSON, aktivita kopírování může zapsat komprimovaná data do cílového umístění. Tady je několik ukázkových scénářů:

* Načte komprimovaná data GZIP z objektu blob Azure, dekomprimuje je a zapíše data výsledků do Azure SQL Database. Můžete definovat vstupní datovou sadu Azure Blob s `compression` `type` vlastností JSON jako gzip.
* Načtěte data z textového souboru z místního systému souborů, Zkomprimujte ho pomocí formátu GZip a zapište komprimovaná data do objektu blob Azure. Nadefinujete výstupní datovou sadu Azure Blob s `compression` `type` vlastností JSON jako gzip.
* Přečtěte si soubor. zip ze serveru FTP, dekomprimujte ho, aby se soubory dostaly do všech souborů, a Azure Data Lake Store. Můžete definovat vstupní datovou sadu FTP s `compression` `type` vlastností JSON jako ZipDeflate.
* Přečtěte si komprimovaná data z objektu blob Azure, dekomprimujete je, Zkomprimujte je pomocí BZIP2 a zapište výsledná data do objektu blob Azure. V tomto případě definujete vstupní datovou sadu Azure Blob s `compression` `type` nastavením na gzip a výstupní datovou sadu s `compression` `type` nastavením na bzip2.   


## <a name="next-steps"></a>Další kroky
V následujících článcích najdete soubory úložiště dat založené na Azure Data Factory:

- [Azure Blob Storage](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Systém souborů](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)