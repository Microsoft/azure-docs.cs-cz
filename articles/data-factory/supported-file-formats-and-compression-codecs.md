---
title: Podporované formáty souborů v Azure Data Factory | Microsoft Docs
description: Toto téma popisuje formáty souborů a komprese kódy, které jsou podporovány na základě souborů konektory v Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 03/28/2018
ms.author: jingwang
ms.openlocfilehash: b038052776cad63030ca8a48a43b4b579ce6c83a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Podporované formáty souborů a komprese kodeky v Azure Data Factory

*Toto téma se vztahuje na následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Store](connector-azure-data-lake-store.md), [Azure File Storage](connector-azure-file-storage.md), [ Systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), a [SFTP](connector-sftp.md).*

Pokud chcete **zkopírujte soubory jako-je** mezi souborové úložiště (binární kopie), přeskočte část formátu v obou definice vstupní a výstupní datové sady. Pokud chcete **analyzovat nebo Generovat soubory s konkrétním formátu**, Azure Data Factory podporuje následující typy souboru formátu:

* [Textovém formátu](#text-format)
* [Formát JSON](#json-format)
* [Formát Avro](#avro-format)
* [Formát ORC](#orc-format)
* [Formát parquet](#parquet-format)

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [podporované formáty souborů a komprese v objektu pro vytváření dat version1](v1//data-factory-supported-file-and-compression-formats.md).

> [!TIP]
> Zjistěte, jak aktivity kopírování mapuje zdroje dat, aby se jímky z [schéma mapování v aktivitě kopírování](copy-activity-schema-and-type-mapping.md), včetně toho, jak metadat je určené na základě svého nastavení formátu souboru a tipů. když chcete zadat [datovou sadu `structure` ](concepts-datasets-linked-services.md#dataset-structure) části.

## <a name="text-format"></a>Textovém formátu

Pokud chcete pro čtení z textového souboru nebo zápis do textového souboru, nastavte `type` vlastnost `format` části datové sady, která **TextFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu TextFormat](#textformat-example).

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| columnDelimiter |Znak, který slouží k oddělení sloupců v souboru. Můžete zvážit použití výjimečných netisknutelné znak, který neexistuje v datech. Můžete například zadejte "\u0001", která představuje spustit z záhlaví (SOH). |Je povolený jenom jeden znak. **Výchozí** hodnota je **čárka (,)**. <br/><br/>Chcete-li použít znak Unicode, přečtěte [znaky znakové sady Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) získat odpovídající kód pro ni. |Ne |
| rowDelimiter |Znak, který slouží k oddělení řádků v souboru. |Je povolený jenom jeden znak. **Výchozí** hodnotou pro čtení může být libovolná z těchto hodnot: **[\r\n, \r, \n]** a pro zápis hodnota **\r\n**. |Ne |
| escapeChar |Speciální znak, který slouží k potlačení oddělovače sloupců v obsahu vstupního souboru. <br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: Hello, world), můžete jako řídicí znak definovat $ a použít ve zdroji řetězec Hello$, world. |Ne |
| quoteChar |Znak, který slouží k uvození textového řetězce. Oddělovače sloupců a řádků uvnitř znaků uvozovek budou považované za součást hodnoty příslušného řetězce. Tato vlastnost se vztahuje na vstupní i výstupní datové sady.<br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: <Hello, world>), můžete jako znak uvozovek definovat " (dvojité uvozovky) a použít ve zdroji řetězec "Hello$, world". |Ne |
| nullValue |Jeden nebo několik znaků, které se používají jako reprezentace hodnoty Null. |Jeden nebo několik znaků. **Výchozí** hodnoty jsou **\N a NULL** pro čtení a **\N** pro zápis. |Ne |
| encodingName |Zadejte název kódování. |Platný název kódování. Další informace najdete v tématu [Vlastnost Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |Ne |
| firstRowAsHeader |Určuje, jestli se má první řádek považovat za záhlaví. U vstupní datové sady Data Factory načítá první řádek jako záhlaví. U výstupní datové sady Data Factory zapisuje první řádek jako záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (výchozí)</b> |Ne |
| skipLineCount |Určuje počet řádků, které se při čtení dat ze vstupních souborů mají přeskočit. Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Ne |
| treatEmptyAsNull |Určuje, jestli se při čtení dat ze vstupního souboru má prázdný řetězec nebo řetězec s hodnotou null považovat za hodnotu null. |**True (výchozí)**<br/>False |Ne |

### <a name="textformat-example"></a>Příklad typu TextFormat

V následující definici JSON pro datovou sadu některé volitelné vlastnosti nejsou zadány.

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

K **importu a exportu souboru JSON jako-je do/z Azure Cosmos DB**, najdete v části dokumenty JSON importu a exportu v [přesun dat do/z Azure Cosmos DB](connector-azure-cosmos-db.md) článku.

Pokud chcete analyzovat JSON soubory nebo zapisovat data ve formátu JSON, nastavte `type` vlastnost `format` části k **JsonFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example).

| Vlastnost | Popis | Požadováno |
| --- | --- | --- |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |
| jsonNodeReference | Pokud chcete iterovat a extrahovat data z objektů uvnitř pole se stejným vzorem, zadejte pro toto pole cestu JSON. Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. | Ne |
| jsonPathDefinition | Zadejte výraz cesty JSON pro každé mapování sloupců s vlastním názvem sloupce (s počátečním malým písmenem). Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. Můžete extrahovat data z objektu nebo pole. <br/><br/> U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `jsonNodeReference` začněte elementem pole. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example). | Ne |
| encodingName |Zadejte název kódování. Seznam platných názvů kódování najdete v tématu věnovaném vlastnosti [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |Ne |
| nestingSeparator |Znak, který se používá k oddělení úrovní vnoření. Výchozí hodnota je tečka (.). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování lze analyzovat následující vzorce soubory JSON:

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

Při kopírování dat z soubory JSON, najdete v následujících dvou vzorcích. Obecné body poznamenat:

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
                    "ResourceManagmentProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
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

| ID | deviceType | targetResourceType | resourceManagmentProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Vstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). A konkrétně:

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace najdete v tématu [mapování zdrojové sloupce datové sady na cílový datovou sadu sloupců](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. Ke zkopírování dat z pole, můžete použít `array[x].property` extrahovat hodnotu dané vlastnosti z `xth` objektu, nebo můžete použít `array[*].property` k nalezení hodnoty z libovolného objektu, který obsahuje tato vlastnost.

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
            "name": "resourceManagmentProcessRunId",
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
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagmentProcessRunId": "$.context.custom.dimensions[1].ResourceManagmentProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace najdete v tématu [mapování zdrojové sloupce datové sady na cílový datovou sadu sloupců](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` označuje k iteraci a extrahovat data z objektů se stejným vzorkem pod **pole** `orderlines`.
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. V tomto příkladu `ordernumber`, `orderdate`, a `city` pod kořenový objekt s JSON cesta začínat `$.`, zatímco `order_pd` a `order_price` jsou definovány s cestou odvozen z elementu pole bez `$.` .

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

Pokud máte v databázi SQL v následující tabulce:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

a pro každý záznam očekáváte k zápisu do objektu JSON v následujícím formátu:

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

Výstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). Přesněji řečeno `structure` oddíl definuje názvy přizpůsobených vlastností, které v cílovém souboru `nestingSeparator` (výchozí hodnota je ".") se používají k identifikaci vrstvě vnoření od názvu. Pokud nechcete měnit název vlastnosti v porovnání se zdrojovým názvem sloupce nebo vnořit některé z vlastností, je tento oddíl **nepovinný**.

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

Pokud chcete formát Avro použít v tabulce Hive, najdete potřebné informace v [kurzu k Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Je třeba počítat s následujícím:

* [Komplexní datové typy](http://avro.apache.org/docs/current/spec.html#schema_complex) nejsou podporovány (zaznamenává výčty, pole, map, sjednocení a pevné).

## <a name="orc-format"></a>Formát ORC

Pokud chcete analyzovat soubory ORC nebo zapisovat data ve formátu ORC, nastavte vlastnost `format` `type` na hodnotu **OrcFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Pro kopírování oprávněné modulem Runtime integrace Self-hosted například mezi místními a cloudovými ukládá data, pokud nejsou kopírování souborů ORC **jako-je**, musíte nainstalovat prostředí JRE 8 (prostředí Java Runtime) v počítači reakcí na Incidenty. 64bitová verze IR vyžaduje prostředí JRE 64-bit. Obě verze najdete [tady](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (STRUCT, MAP, LIST, UNION).
* Soubory ORC mají tři [možnosti komprese](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

### <a name="data-type-mapping-for-orc-files"></a>Datový typ mapování pro souborů ORC

| Typ průběžných dat objektu pro vytváření dat | Typy ORC |
|:--- |:--- |
| Logická hodnota | Logická hodnota |
| SByte – | Bajtů |
| Bajtů | Krátké |
| Int16 | Krátké |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Dlouhý |
| Int64 | Dlouhý |
| UInt64 | Řetězec |
| Jednoduchý | Plovoucí desetinná čárka |
| Dvojitý | Dvojitý |
| Decimal | Decimal |
| Řetězec | Řetězec |
| DateTime | Časové razítko |
| DateTimeOffset | Časové razítko |
| TimeSpan | Časové razítko |
| ByteArray | Binární hodnota |
| Guid | Řetězec |
| Char | Char(1) |

## <a name="parquet-format"></a>Formát parquet

Pokud chcete analyzovat soubory Parquet nebo zapisovat data ve formátu Parquet, nastavte vlastnost `format` `type` na hodnotu **Format**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "ParquetFormat"
}
```

> [!IMPORTANT]
> Pro kopírování oprávněné modulem Runtime integrace Self-hosted například mezi místními a cloudovými ukládá data, pokud nejsou kopírování souborů Parquet **jako-je**, musíte nainstalovat prostředí JRE 8 (prostředí Java Runtime) v počítači reakcí na Incidenty. 64bitová verze IR vyžaduje prostředí JRE 64-bit. Obě verze najdete [tady](http://go.microsoft.com/fwlink/?LinkId=808605).
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (MAP, LIST).
* Soubory Parquet mají tyto možnosti komprese: NONE, SNAPPY, GZIP a LZO. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru Parquet ale Data Factory využívá možnost SNAPPY, která je pro formát Parquet výchozí. V současnosti toto chování nejde potlačit.

### <a name="data-type-mapping-for-parquet-files"></a>Datový typ mapování pro Parquet soubory

| Typ průběžných dat objektu pro vytváření dat | Primitivní typ parquet | Parquet původní typ (deserializovat) | Parquet původní typ (serializovat) |
|:--- |:--- |:--- |:--- |
| Logická hodnota | Logická hodnota | Nevztahuje se. | Nevztahuje se. |
| SByte – | Int32 | int8 | int8 |
| Bajtů | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64 nebo binární | UInt64 | Decimal |
| Jednoduchý | Plovoucí desetinná čárka | Nevztahuje se. | Nevztahuje se. |
| Dvojitý | Dvojitý | Nevztahuje se. | Nevztahuje se. |
| Decimal | Binární hodnota | Decimal | Decimal |
| Řetězec | Binární hodnota | Utf8 | Utf8 |
| DateTime | Int96 | Nevztahuje se. | Nevztahuje se. |
| TimeSpan | Int96 | Nevztahuje se. | Nevztahuje se. |
| DateTimeOffset | Int96 | Nevztahuje se. | Nevztahuje se. |
| ByteArray | Binární hodnota | Nevztahuje se. | Nevztahuje se. |
| Guid | Binární hodnota | Utf8 | Utf8 |
| Char | Binární hodnota | Utf8 | Utf8 |
| CharArray | Nepodporováno | Nevztahuje se. | Nevztahuje se. |

## <a name="compression-support"></a>Podporu komprese

Azure Data Factory podporuje komprimovat nebo dekomprimovat dat během kopírování. Pokud zadáte `compression` vlastnost vstupní datové sady, aktivitě kopírování čtení komprimovaná data ze zdrojové a dekomprimovat a pokud zadáte vlastnost v datovou sadu výstupů, aktivitě kopírování komprimovat pak zapsat data do jímky. Tady je několik ukázkových scénářů:

* Komprimované GZIP čtení dat z objektu blob Azure dekomprimovat ho a zapsat Výsledná data do Azure SQL database. Definujete vstupní datové sady objektu Blob Azure s `compression` `type` vlastnost jako GZIP.
* Čtení dat ze souboru ve formátu prostého textu z místního systému souborů, je formátu GZip komprimovat a zapíše komprimovaná data do Azure blob. Definujte výstupní datové Azure Blob s `compression` `type` vlastnost jako GZip.
* Soubor ZIP číst ze serveru FTP dekomprimovat ho k získání souborů uvnitř a zobrazovat tyto soubory v Azure Data Lake Store. Definujete vstupní datové sady FTP s `compression` `type` vlastnost jako ZipDeflate.
* Číst kompresí GZIP dat z Azure blob, dekomprimovat ho, je pomocí BZIP2 komprimovat a zapsat Výsledná data do Azure blob. Definujete vstupní datové sady objektu Blob Azure s `compression` `type` nastavena na GZIP a výstupní datovou sadu s `compression` `type` nastavena na BZIP2.

Pokud chcete zadat komprese datové sady, použijte **komprese** vlastnost v datové sadě JSON jako v následujícím příkladu:   

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

**Komprese** části má dvě vlastnosti:

* **Typ:** kodeků kompresi, což může být **GZIP**, **Deflate**, **BZIP2**, nebo **ZipDeflate**.
* **Úroveň:** kompresní poměr, což může být **Optimal** nebo **nejrychlejší**.

  * **Nejrychlejší:** komprese operace by se měla dokončit co nejrychleji, i když bude výsledný soubor není komprimována optimálně.
  * **Optimální**: komprese operace by měl být optimálně komprimován, i v případě, že trvá delší dobu pro dokončení operace.

    Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tématu.

> [!NOTE]
> Nastavení komprese nejsou podporovány pro data v **AvroFormat**, **OrcFormat**, nebo **ParquetFormat**. Při čtení souborů v těchto formátů, Data Factory zjišťuje a používá kompresní kodek v metadatech. Při zápisu do souborů v těchto formátů, Data Factory zvolí kodek komprese výchozí pro tento formát. Například ZLIB OrcFormat a SNAPPY pro ParquetFormat.

## <a name="next-steps"></a>Další postup

Najdete v následujících článcích pro úložiště dat na základě souborů podporovaných službou Azure Data Factory:

- [Konektor služby Azure Blob Storage](connector-azure-blob-storage.md)
- [Konektor Azure Data Lake Store](connector-azure-data-lake-store.md)
- [Konektor Amazon S3](connector-amazon-simple-storage-service.md)
- [Konektor systému souborů](connector-file-system.md)
- [Konektor FTP](connector-ftp.md)
- [Konektor protokolu SFTP](connector-sftp.md)
- [HDFS konektoru](connector-hdfs.md)
- [Konektor HTTP](connector-http.md)