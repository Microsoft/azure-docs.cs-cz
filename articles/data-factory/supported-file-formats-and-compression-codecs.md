---
title: Podporované formáty souborů ve službě Azure Data Factory | Dokumentace Microsoftu
description: Toto téma popisuje formáty souborů a komprese kódy, které jsou podporovány souborové konektorů v Azure Data Factory.
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: jingwang
ms.openlocfilehash: 217af96f23ad048ff4bb8a0bbb4902b3af16f21a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868314"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Podporované formáty souborů a komprese kodeky ve službě Azure Data Factory

*Toto téma platí pro následující konektory: [Amazon S3](connector-amazon-simple-storage-service.md), [objektů Blob v Azure](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [systém souborů](connector-file-system.md), [FTP](connector-ftp.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), a [SFTP](connector-sftp.md).*

Pokud chcete **kopírovat soubory jako-je** mezi souborové úložištěm (binární kopie) a přeskočit část o formátu v definicích oba vstupní a výstupní datové sady. Pokud chcete **analyzovat a generovat soubory s konkrétním formátu**, Azure Data Factory podporuje následující typy formátů souboru:

* [Formát textu](#text-format)
* [Formát JSON](#json-format)
* [Formát Avro](#avro-format)
* [Formát ORC](#orc-format)
* [Formát parquet](#parquet-format)

> [!TIP]
> Zjistěte, jak aktivitu kopírování, která mapuje svá zdrojová data pro zpracování z [mapování schématu v aktivitě kopírování](copy-activity-schema-and-type-mapping.md), včetně jak metadata je určen na základě svého nastavení formátu souboru a tipy na tom, kdy k určení [datovou sadu `structure` ](concepts-datasets-linked-services.md#dataset-structure) oddílu.

## <a name="text-format"></a>Formát textu

Pokud chcete pro čtení z textového souboru nebo zápis do textového souboru, nastavte `type` vlastnost `format` části datové sady na **TextFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu TextFormat](#textformat-example).

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| columnDelimiter |Znak, který slouží k oddělení sloupců v souboru. Můžete zvážit použití výjimečných málo častého netisknutelného znaku, který nemusí existovat ve vašich datech. Zadejte například "\u0001", který představuje Start začátek hlavičky (SOH). |Je povolený jenom jeden znak. **Výchozí** hodnota je **čárka (,)**. <br/><br/>Použijte znakovou sadu Unicode, najdete v tématu [znaky znakové sady Unicode](https://en.wikipedia.org/wiki/List_of_Unicode_characters) získat pro ni odpovídající kód. |Ne |
| rowDelimiter |Znak, který slouží k oddělení řádků v souboru. |Je povolený jenom jeden znak. **Výchozí** hodnotou pro čtení může být libovolná z těchto hodnot: **[\r\n, \r, \n]** a pro zápis hodnota **\r\n**. |Ne |
| escapeChar |Speciální znak, který slouží k potlačení oddělovače sloupců v obsahu vstupního souboru. <br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: Hello, world), můžete jako řídicí znak definovat $ a použít ve zdroji řetězec Hello$, world. |Ne |
| quoteChar |Znak, který slouží k uvození textového řetězce. Oddělovače sloupců a řádků uvnitř znaků uvozovek budou považované za součást hodnoty příslušného řetězce. Tato vlastnost se vztahuje na vstupní i výstupní datové sady.<br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: <Hello, world>), můžete jako znak uvozovek definovat " (dvojité uvozovky) a použít ve zdroji řetězec "Hello$, world". |Ne |
| nullValue |Jeden nebo několik znaků, které se používají jako reprezentace hodnoty Null. |Jeden nebo několik znaků. **Výchozí** hodnoty jsou **\N a NULL** pro čtení a **\N** pro zápis. |Ne |
| encodingName |Zadejte název kódování. |Platný název kódování. Další informace najdete v tématu [Vlastnost Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |Ne |
| firstRowAsHeader |Určuje, jestli se má první řádek považovat za záhlaví. U vstupní datové sady Data Factory načítá první řádek jako záhlaví. U výstupní datové sady Data Factory zapisuje první řádek jako záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (výchozí)</b> |Ne |
| skipLineCount |Určuje počet řádků, které se při čtení dat ze vstupních souborů mají přeskočit. Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Ne |
| treatEmptyAsNull |Určuje, jestli se při čtení dat ze vstupního souboru má prázdný řetězec nebo řetězec s hodnotou null považovat za hodnotu null. |**True (výchozí)**<br/>False |Ne |

### <a name="textformat-example"></a>Příklad typu TextFormat

V následující definici JSON pro datovou sadu jsou uvedeny některé vlastnosti volitelné.

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

K **Importovat/exportovat soubor JSON jako – je do nebo ze služby Azure Cosmos DB**, naleznete v části dokumentů JSON importu a exportu v [přesun dat do a ze služby Azure Cosmos DB](connector-azure-cosmos-db.md) článku.

Pokud chcete analyzovat soubory JSON nebo zapisovat data ve formátu JSON, nastavte `type` vlastnost `format` části **JsonFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example).

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |
| jsonNodeReference | Pokud chcete iterovat a extrahovat data z objektů uvnitř pole se stejným vzorem, zadejte pro toto pole cestu JSON. Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. | Ne |
| jsonPathDefinition | Zadejte výraz cesty JSON pro každé mapování sloupců s vlastním názvem sloupce (s počátečním malým písmenem). Tato vlastnost se podporuje jenom pro kopírování dat ze souborů JSON. Můžete extrahovat data z objektu nebo pole. <br/><br/> U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `jsonNodeReference` začněte elementem pole. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example). | Ne |
| encodingName |Zadejte název kódování. Seznam platných názvů kódování najdete v tématu věnovaném vlastnosti [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |Ne |
| nestingSeparator |Znak, který se používá k oddělení úrovní vnoření. Výchozí hodnota je tečka (.). |Ne |

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může analyzovat tyto vzory souborů JSON:

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

Při kopírování dat ze souborů JSON najdete v následujících dvou vzorcích. Obecné aspekty, které mějte na paměti:

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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace najdete v tématu [mapování sloupců zdrojové datové sady na cílové datové sadě sloupce](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. Chcete-li kopírovat data z pole, můžete použít `array[x].property` a extrahovat hodnotu dané vlastnosti z `xth` objektu, nebo můžete použít `array[*].property` k nalezení hodnoty z libovolného objektu, který obsahuje tuto vlastnost.

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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace najdete v tématu [mapování sloupců zdrojové datové sady na cílové datové sadě sloupce](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` Označuje, iterovat a extrahovat data z objektů se stejným vzorem v rámci **pole** `orderlines`.
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. V tomto příkladu `ordernumber`, `orderdate`, a `city` v kořenovém objektu s JSON cesta začínající `$.`, zatímco `order_pd` a `order_price` jsou definované pomocí cesty odvozené z elementu pole bez řetězce `$.` .

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

Pokud máte ve službě SQL Database v následující tabulce:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

a pro každý záznam očekáváte, že k zápisu do objektu JSON v následujícím formátu:

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

Výstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). Přesněji řečeno `structure` oddíl definuje vlastní názvy vlastností v cílovém souboru a `nestingSeparator` (výchozí hodnota je ".") se používají k určení vrstvy vnoření od názvu. Pokud nechcete měnit název vlastnosti v porovnání se zdrojovým názvem sloupce nebo vnořit některé z vlastností, je tento oddíl **nepovinný**.

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

* [Komplexní datové typy](http://avro.apache.org/docs/current/spec.html#schema_complex) se nepodporují (záznamy, výčty, pole, mapy, sjednocení a oprava).

## <a name="orc-format"></a>Formát ORC

Pokud chcete analyzovat soubory ORC nebo zapisovat data ve formátu ORC, nastavte vlastnost `format` `type` na hodnotu **OrcFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Pro kopírování pověřený modul Integration Runtime například mezi místním prostředím a cloudem úložiště dat, pokud soubory ORC nekopírujete **jako-je**, je nutné nainstalovat prostředí JRE 8 (Java Runtime Environment) na svém počítači reakcí na Incidenty. Prostředí IR 64-bit vyžaduje 64bitovou platformu JRE. Obě verze najdete [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (STRUCT, MAP, LIST, UNION).
* Soubory ORC mají tři [možnosti komprese](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

### <a name="data-type-mapping-for-orc-files"></a>Datový typ mapování pro soubory ORC

| Data factory dočasné datový typ | Typy ORC |
|:--- |:--- |
| Logická hodnota | Logická hodnota |
| SByte – | Bajt |
| Bajt | Krátké |
| Int16 | Krátké |
| UInt16 | Int |
| Datový typ Int32 | Int |
| UInt32 | Dlouhé |
| Int64 | Dlouhé |
| UInt64 | Řetězec |
| Jednoduchá | Float |
| Double | Double |
| Decimal | Decimal |
| Řetězec | Řetězec |
| DateTime | Timestamp |
| DateTimeOffset | Timestamp |
| Časový interval | Timestamp |
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
> Pro kopírování pověřený modul Integration Runtime například mezi místním prostředím a cloudem úložiště dat, pokud soubory Parquet nekopírujete **jako-je**, je nutné nainstalovat prostředí JRE 8 (Java Runtime Environment) na svém počítači reakcí na Incidenty. Prostředí IR 64-bit vyžaduje 64bitovou platformu JRE. Obě verze najdete [tady](https://go.microsoft.com/fwlink/?LinkId=808605).
>

Je třeba počítat s následujícím:

* Komplexní datové typy se nepodporují (MAP, LIST).
* Soubory Parquet mají tyto možnosti komprese: NONE, SNAPPY, GZIP a LZO. Služba data Factory podporuje čtení dat ze souboru Parquet v některém z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru Parquet ale Data Factory využívá možnost SNAPPY, která je pro formát Parquet výchozí. V současnosti toto chování nejde potlačit.

### <a name="data-type-mapping-for-parquet-files"></a>Mapování pro soubory Parquet typu dat.

| Data factory dočasné datový typ | Primitivní typ parquet | Parquet původního typu (deserializovat) | Parquet původního typu (serializuje) |
|:--- |:--- |:--- |:--- |
| Logická hodnota | Logická hodnota | neuvedeno | neuvedeno |
| SByte – | Datový typ Int32 | Int8 | Int8 |
| Bajt | Datový typ Int32 | UInt8 | Int16 |
| Int16 | Datový typ Int32 | Int16 | Int16 |
| UInt16 | Datový typ Int32 | UInt16 | Datový typ Int32 |
| Datový typ Int32 | Datový typ Int32 | Datový typ Int32 | Datový typ Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binární soubor | UInt64 | Decimal |
| Jednoduchá | Float | neuvedeno | neuvedeno |
| Double | Double | neuvedeno | neuvedeno |
| Decimal | Binární hodnota | Decimal | Decimal |
| Řetězec | Binární hodnota | Utf8 | Utf8 |
| DateTime | Int96 | neuvedeno | neuvedeno |
| Časový interval | Int96 | neuvedeno | neuvedeno |
| DateTimeOffset | Int96 | neuvedeno | neuvedeno |
| ByteArray | Binární hodnota | neuvedeno | neuvedeno |
| Guid | Binární hodnota | Utf8 | Utf8 |
| Char | Binární hodnota | Utf8 | Utf8 |
| CharArray | Nepodporuje se | neuvedeno | neuvedeno |

## <a name="compression-support"></a>Podpora komprese

Azure Data Factory podporuje komprese nebo dekomprese dat během kopírování. Pokud zadáte `compression` vlastnost ve vstupní datovou sadu aktivita kopírování čtení komprimovaných dat ze zdrojové a dekomprimovat; a při zadání vlastnosti ve výstupní datovou sadu aktivita kopírování komprimovat a zapisovat data do jímky. Tady je několik ukázkových scénářů:

* Čtení GZIP komprimovaná data z objektu blob Azure by jej dekomprimovat a zápis Výsledná data do služby Azure SQL database. Definujte vstupní datové sady objektů Blob v Azure se `compression` `type` vlastnost jako GZIP.
* Čtení dat ze souboru prostého textu v místním systému souborů, je ve formátu GZip komprimovat a zapíše komprimovaných dat do objektu blob Azure. Definujte výstupní datovou sadu objektů Blob v Azure se `compression` `type` vlastnost jako GZip.
* Soubor ZIP ke čtení ze serveru FTP, jsou dekomprimace ho k získání souborů uvnitř a tyto soubory přímo do Azure Data Lake Store. Definujte vstupní datová sada FTP se `compression` `type` vlastnost jako ZipDeflate.
* Čtení GZIP komprimovaných dat z objektu blob Azure, by jej dekomprimovat, je pomocí BZIP2 komprimovat a zápis Výsledná data do objektu blob Azure. Definujte vstupní datové sady objektů Blob v Azure s `compression` `type` nastavena na GZIP a výstupní datovou sadu s `compression` `type` nastavena na BZIP2.

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

**Komprese** oddíl má dvě vlastnosti:

* **Typ:** kompresní kodek, který může být **GZIP**, **Deflate**, **BZIP2**, nebo **ZipDeflate**.
* **Úroveň:** kompresní poměr, který může být **Optimal** nebo **nejrychlejší**.

  * **Nejrychlejší:** komprese operace by se měla dokončit co nejrychleji, i v případě, že výsledný soubor není komprimována optimálně.
  * **Optimální**: Operace komprese by měl být optimálně v komprimovaném tvaru i v případě, že operace trvá delší dobu pro dokončení.

    Další informace najdete v tématu [úroveň komprese](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) tématu.

> [!NOTE]
> Nastavení komprese nejsou podporovány pro data ve službě **AvroFormat**, **OrcFormat**, nebo **ParquetFormat**. Při čtení souborů v těchto formátů, Data Factory zjistí a využívá kompresní kodek v metadatech. Při zápisu do souborů v těchto formátů, Data Factory využívá možnost kompresní kodek výchozí pro daný formát. Například pro OrcFormat ZLIB a SNAPPY pro ParquetFormat.

## <a name="next-steps"></a>Další postup

Naleznete v následujících článcích pro úložiště souborových dat podporovaných službou Azure Data Factory:

- [Konektor Azure Blob Storage](connector-azure-blob-storage.md)
- [Konektor Azure Data Lake Store](connector-azure-data-lake-store.md)
- [Konektor Amazon S3](connector-amazon-simple-storage-service.md)
- [Konektoru systému souborů](connector-file-system.md)
- [Konektor FTP](connector-ftp.md)
- [Konektor SFTP](connector-sftp.md)
- [Konektor HDFS](connector-hdfs.md)
- [Konektor HTTP](connector-http.md)
