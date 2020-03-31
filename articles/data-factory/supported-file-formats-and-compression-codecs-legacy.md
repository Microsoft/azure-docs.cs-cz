---
title: Podporované formáty souborů ve službě Azure Data Factory (starší verze)
description: Toto téma popisuje formáty souborů a kompresní kódy, které jsou podporované konektory založené na souborech v Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 423706c391e8d8c2c609798d9f50e5a22f5c39bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260680"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Podporované formáty souborů a kompresní kodeky v Azure Data Factory (starší verze)

*Tento článek se týká následujících konektorů: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)a [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory zavedla nový model datové sady založený na formátu, viz odpovídající formát článku s podrobnostmi: <br>- [Formát Avro](format-avro.md)<br>- [Binární formát](format-binary.md)<br>- [Formát textu s oddělovačem](format-delimited-text.md)<br>- [Formát JSON](format-json.md)<br>- [Formát ORC](format-orc.md)<br>- [Formát parket](format-parquet.md)<br>Konfigurace ostatních uvedené v tomto článku jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna. 

## <a name="text-format-legacy"></a><a name="text-format"></a>Formát textu (starší verze)

>[!NOTE]
>Naučte se nový model z článku [formátu Oddělovač textu.](format-delimited-text.md) Následující konfigurace v datové sadě úložiště dat založené na souborech jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna.

Pokud chcete číst z textového souboru nebo zapisovat do textového souboru, nastavte `type` vlastnost v `format` části datové sady na **TextFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu TextFormat](#textformat-example).

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| columnDelimiter |Znak, který slouží k oddělení sloupců v souboru. Můžete zvážit použití vzácné horečnatého znaku, který v datech nemusí existovat. Zadejte například "\u0001", což představuje začátek nadpisu (SOH). |Je povolený jenom jeden znak. **Výchozí** hodnota je **čárka (,)**. <br/><br/>Chcete-li použít znak Unicode, naleznete znaky [Unicode,](https://en.wikipedia.org/wiki/List_of_Unicode_characters) abyste pro něj získali odpovídající kód. |Ne |
| rowDelimiter |Znak, který slouží k oddělení řádků v souboru. |Je povolený jenom jeden znak. **Výchozí** hodnotou pro čtení může být libovolná z těchto hodnot: **[\r\n, \r, \n]** a pro zápis hodnota **\r\n**. |Ne |
| escapeChar |Speciální znak, který slouží k potlačení oddělovače sloupců v obsahu vstupního souboru. <br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: Hello, world), můžete jako řídicí znak definovat $ a použít ve zdroji řetězec Hello$, world. |Ne |
| quoteChar |Znak, který slouží k uvození textového řetězce. Oddělovače sloupců a řádků uvnitř znaků uvozovek budou považované za součást hodnoty příslušného řetězce. Tato vlastnost se vztahuje na vstupní i výstupní datové sady.<br/><br/>Pro tabulku nejde zadat escapeChar a quoteChar současně. |Je povolený jenom jeden znak. Žádná výchozí hodnota. <br/><br/>Příklad: Pokud jako oddělovač sloupců používáte čárku (,), ale chcete znak čárky použít v textu (příklad: <Hello, world>), můžete jako znak uvozovek definovat " (dvojité uvozovky) a použít ve zdroji řetězec "Hello$, world". |Ne |
| nullValue |Jeden nebo několik znaků, které se používají jako reprezentace hodnoty Null. |Jeden nebo několik znaků. **Výchozí** hodnoty jsou **\N a NULL** pro čtení a **\N** pro zápis. |Ne |
| encodingName |Zadejte název kódování. |Platný název kódování. Další informace najdete v tématu [Vlastnost Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je **UTF-8**. |Ne |
| firstRowAsHeader |Určuje, jestli se má první řádek považovat za záhlaví. U vstupní datové sady Data Factory načítá první řádek jako záhlaví. U výstupní datové sady Data Factory zapisuje první řádek jako záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (výchozí)</b> |Ne |
| skipLineCount |Označuje počet **neprázdných** řádků, které mají být při čtení dat ze vstupních souborů přeskočte. Pokud je zadaný parametr skipLineCount i firstRowAsHeader, nejdřív se přeskočí příslušný počet řádků a potom se ze vstupního souboru načtou informace záhlaví. <br/><br/>Vzorové scénáře najdete v tématu [Scénáře použití `firstRowAsHeader` a `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Ne |
| treatEmptyAsNull |Určuje, jestli se při čtení dat ze vstupního souboru má prázdný řetězec nebo řetězec s hodnotou null považovat za hodnotu null. |**True (výchozí)**<br/>False |Ne |

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

## <a name="json-format-legacy"></a><a name="json-format"></a>Formát JSON (starší verze)

>[!NOTE]
>Naučte se nový model z článku [ve formátu JSON.](format-json.md) Následující konfigurace v datové sadě úložiště dat založené na souborech jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna.

Pokud chcete **importovat nebo exportovat soubor JSON tak, jak je do/z Azure Cosmos DB**, najdete v článku Import a export dokumentů JSON v článku [Přesunout data do/z Azure Cosmos DB.](connector-azure-cosmos-db.md)

Pokud chcete analyzovat soubory JSON nebo zapsat data ve formátu `type` JSON, nastavte vlastnost v `format` sekci na **JsonFormat**. Můžete také zadat následující **nepovinné** vlastnosti v oddílu `format`. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example).

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| filePattern |Určete vzor dat uložených v jednotlivých souborech JSON. Povolené hodnoty jsou **setOfObjects** a **arrayOfObjects**. **Výchozí hodnota** je **setOfObjects**. Podrobné informace o těchto vzorech najdete v tématu [Vzory souborů JSON](#json-file-patterns). |Ne |
| jsonNodeReference | Pokud chcete iterovat a extrahovat data z objektů uvnitř pole se stejným vzorem, zadejte pro toto pole cestu JSON. Tato vlastnost je podporována pouze při kopírování dat **ze** souborů JSON. | Ne |
| jsonPathDefinition | Zadejte výraz cesty JSON pro každé mapování sloupců s vlastním názvem sloupce (s počátečním malým písmenem). Tato vlastnost je podporována pouze při kopírování dat **ze** souborů JSON a můžete extrahovat data z objektu nebo pole. <br/><br/> U polí v kořenovém objektu začtěte s kořenem $, u polí uvnitř pole vybraného pomocí vlastnosti `jsonNodeReference` začněte elementem pole. Postup konfigurace najdete v části [Příklad typu JsonFormat](#jsonformat-example). | Ne |
| encodingName |Zadejte název kódování. Seznam platných názvů kódování najdete v tématu věnovaném vlastnosti [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Příklad: windows-1250 nebo shift_jis. **Výchozí** hodnota je: **UTF-8**. |Ne |
| nestingSeparator |Znak, který se používá k oddělení úrovní vnoření. Výchozí hodnota je tečka (.). |Ne |

>[!NOTE]
>V případě křížového použití dat v poli do více řádků (případ 1 -> vzorku 2 v [příkladech JsonFormat](#jsonformat-example)) můžete rozšířit pouze jedno pole pomocí vlastnosti `jsonNodeReference`.

### <a name="json-file-patterns"></a>Vzory souborů JSON

Aktivita kopírování může analyzovat následující vzorky souborů JSON:

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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace naleznete v tématu [Mapování sloupců zdrojové datové sady na sloupce cílové datové sady](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. Chcete-li zkopírovat data `array[x].property` z pole, můžete extrahovat hodnotu dané vlastnosti z objektu `xth` nebo můžete použít `array[*].property` k nalezení hodnoty z libovolného objektu obsahujícího tuto vlastnost.

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

- Oddíl `structure` definuje vlastní názvy sloupců a odpovídající datový typ při převodu do tabulkového formátu. Pokud mapování sloupců není potřeba, je tento oddíl **nepovinný**. Další informace naleznete v tématu [Mapování sloupců zdrojové datové sady na sloupce cílové datové sady](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference`označuje iterát a extrahovat data z objektů se stejným vzorkem pod **polem** `orderlines`.
- `jsonPathDefinition` určuje cestu JSON pro jednotlivé sloupce a udává, odkud se mají extrahovat data. V tomto `ordernumber`příkladu , `orderdate` `city` , a jsou pod kořenovým objektem s cestou JSON začínající na `$.`, `order_pd` zatímco a `order_price` jsou definovány s cestou odvozenou z prvku pole bez `$.`.

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

Pokud máte v databázi SQL následující tabulku:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

a pro každý záznam očekáváte zápis do objektu JSON v následujícím formátu:

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

Výstupní datová sada typu **JsonFormat** je definovaná následujícím způsobem (částečná definice obsahující jenom relevantní části). Přesněji `structure` řečeno, oddíl definuje vlastní názvy `nestingSeparator` vlastností v cílovém souboru (výchozí je ".") se používají k identifikaci vrstvy hnízda z názvu. Pokud nechcete měnit název vlastnosti v porovnání se zdrojovým názvem sloupce nebo vnořit některé z vlastností, je tento oddíl **nepovinný**.

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

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a>Formát parket (starší verze)

>[!NOTE]
>Naučte se nový model z článku [formátu parket.](format-parquet.md) Následující konfigurace v datové sadě úložiště dat založené na souborech jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna.

Pokud chcete analyzovat soubory Parquet nebo zapisovat data ve formátu Parquet, nastavte vlastnost `format` `type` na hodnotu **Format**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy nejsou podporovány (MAP, LIST).
* Prázdné místo v názvu sloupce není podporováno.
* Soubory Parquet mají tyto možnosti komprese: NONE, SNAPPY, GZIP a LZO. Data Factory podporuje čtení dat z parketového souboru v některém z těchto komprimovaných formátů kromě LZO - používá kompresní kodek v metadatech ke čtení dat. Při zápisu do souboru Parquet ale Data Factory využívá možnost SNAPPY, která je pro formát Parquet výchozí. V současnosti toto chování nejde potlačit.

> [!IMPORTANT]
> Pro kopírování zmocněný Self-hosted Integrace Runtime například mezi on-premises a cloud úložiště dat, pokud nejste kopírování parketové **soubory, jak-je**, je třeba nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** na vašem počítači IR. Další podrobnosti naleznete v následujícím odstavci s dalšími podrobnostmi.

Pro kopírování spuštěné na samoobslužné ir s serializací par- souboru serializace/ *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* deserializace, ADF vyhledá java runtime *`JAVA_HOME`* za prvé kontrolou registru pro JRE, pokud nebyl nalezen, za druhé kontrolu systémové proměnné pro OpenJDK.

- **Použití JRE**: 64bitové infračervené záření vyžaduje 64bitový jre. Najdete ji [zde](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Chcete-li použít OpenJDK**: je podporován od IR verze 3.13. Zabalte soubor jvm.dll se všemi ostatními požadovanými sestaveními openjdk do samoobslužného infračerveného počítače a odpovídajícím způsobem nastavte proměnnou prostředí systému JAVA_HOME.

>[!TIP]
>Pokud zkopírujete data do/z formátu Parketu pomocí prostředí s vlastním hostitelem integračního běhu a stisknete chybu s tím, že "Při vyvolání java došlo k chybě, zpráva: **java.lang.OutOfMemoryError:Java hedžprostor**", můžete přidat proměnnou `_JAVA_OPTIONS` prostředí v počítači, který je hostitelem samoobslužné infračervené hodu, a upravit velikost haldy min/max pro JVM, aby se tato kopie uvolnila, a pak znovu spustit kanál.

![Nastavit velikost haldy JVM na samoobslužné infračervené ovládání](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Příklad: nastavená `_JAVA_OPTIONS` `-Xms256m -Xmx16g`proměnná s hodnotou . Příznak `Xms` určuje počáteční fond přidělení paměti pro virtuální počítač Java `Xmx` (JVM), zatímco určuje maximální fond přidělení paměti. To znamená, že JVM `Xms` bude spuštěn s množstvím paměti a `Xmx` bude moci používat maximální množství paměti. Ve výchozím nastavení používá ADF min 64MB a max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mapování datových typů pro soubory parket

| Dočasný datový typ datové továrny | Parket primitivní typ | Původní typ parket (deserializovat) | Původní typ parket (Serializovat) |
|:--- |:--- |:--- |:--- |
| Logická hodnota | Logická hodnota | Není dostupné. | Není dostupné. |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/Binární | UInt64 | Desetinné číslo |
| Single | Plovoucí desetinná čárka | Není dostupné. | Není dostupné. |
| Double | Double | Není dostupné. | Není dostupné. |
| Desetinné číslo | binární | Desetinné číslo | Desetinné číslo |
| Řetězec | binární | Utf8 | Utf8 |
| DateTime | Int96 | Není dostupné. | Není dostupné. |
| TimeSpan | Int96 | Není dostupné. | Není dostupné. |
| DateTimeOffset | Int96 | Není dostupné. | Není dostupné. |
| Bytearray | binární | Není dostupné. | Není dostupné. |
| Identifikátor GUID | binární | Utf8 | Utf8 |
| Char | binární | Utf8 | Utf8 |
| Pole CharArray | Nepodporuje se | Není dostupné. | Není dostupné. |

## <a name="orc-format-legacy"></a><a name="orc-format"></a>Formát ORC (starší verze)

>[!NOTE]
>Naučte se nový model z článku [formátu ORC.](format-orc.md) Následující konfigurace v datové sadě úložiště dat založené na souborech jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna.

Pokud chcete analyzovat soubory ORC nebo zapisovat data ve formátu ORC, nastavte vlastnost `format` `type` na hodnotu **OrcFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "OrcFormat"
}
```

Je třeba počítat s následujícím:

* Komplexní datové typy nejsou podporovány (STRUCT, MAP, LIST, UNION).
* Prázdné místo v názvu sloupce není podporováno.
* Soubory ORC mají tři [možnosti komprese](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB a SNAPPY. Data Factory podporuje čtení dat ze souborů ORC v libovolném z těchto komprimovaných formátů. K načtení dat využívá kompresní kodek v metadatech. Při zápisu do souboru ORC ale Data Factory využívá možnost ZLIB, která je pro formát ORC výchozí. V současnosti toto chování nejde potlačit.

> [!IMPORTANT]
> Pro kopírování zmocněný Self-hosted Integrace Runtime například mezi on-premises a cloud úložiště dat, pokud nejste kopírování ORC **soubory, jak-je**, je třeba nainstalovat **64-bit JRE 8 (Java Runtime Environment) nebo OpenJDK** na vašem počítači IR. Další podrobnosti naleznete v následujícím odstavci s dalšími podrobnostmi.

Pro kopírování spuštěné na samoobslužné infračervené kontrole se serializací/deserializací *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* souboru ORC vyhledá ADF zaběhu *`JAVA_HOME`* java nejprve kontrolou registru pro JRE, pokud není nalezen, za druhé kontrolou systémové proměnné pro OpenJDK.

- **Použití JRE**: 64bitové infračervené záření vyžaduje 64bitový jre. Najdete ji [zde](https://go.microsoft.com/fwlink/?LinkId=808605).
- **Chcete-li použít OpenJDK**: je podporován od IR verze 3.13. Zabalte soubor jvm.dll se všemi ostatními požadovanými sestaveními openjdk do samoobslužného infračerveného počítače a odpovídajícím způsobem nastavte proměnnou prostředí systému JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>Mapování datového typu pro soubory ORC

| Dočasný datový typ datové továrny | Typy ORC |
|:--- |:--- |
| Logická hodnota | Logická hodnota |
| SByte | Byte |
| Byte | Krátké |
| Int16 | Krátké |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Dlouhé |
| Int64 | Dlouhé |
| UInt64 | Řetězec |
| Single | Plovoucí desetinná čárka |
| Double | Double |
| Desetinné číslo | Desetinné číslo |
| Řetězec | Řetězec |
| DateTime | Časové razítko |
| DateTimeOffset | Časové razítko |
| TimeSpan | Časové razítko |
| Bytearray | binární |
| Identifikátor GUID | Řetězec |
| Char | Char(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a>Formát AVRO (starší verze)

>[!NOTE]
>Naučte se nový model z článku [formátu Avro.](format-avro.md) Následující konfigurace v datové sadě úložiště dat založené na souborech jsou stále podporovány jako-je pro zpětnou kompatibilitu. Doporučujeme použít nový model do budoucna.

Pokud chcete analyzovat soubory Avro nebo zapisovat data ve formátu Avro, nastavte vlastnost `format` `type` na hodnotu **AvroFormat**. V oddílu Format v části typeProperties není potřeba zadávat žádné vlastnosti. Příklad:

```json
"format":
{
    "type": "AvroFormat",
}
```

Pokud chcete formát Avro použít v tabulce Hive, najdete potřebné informace v [kurzu k Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Je třeba počítat s následujícím:

* [Komplexní datové typy](https://avro.apache.org/docs/current/spec.html#schema_complex) nejsou podporovány (záznamy, výčty, pole, mapy, sjednocení a pevné).

## <a name="compression-support-legacy"></a><a name="compression-support"></a>Podpora komprese (starší verze)

Azure Data Factory podporuje komprimovat/dekompresi dat během kopírování. Když zadáte `compression` vlastnost ve vstupní datové sadě, aktivita kopírování přečte komprimovaná data ze zdroje a dekomprimuje je; a když zadáte vlastnost ve výstupní datové sadě, komprese aktivity kopírování pak zapíše data do jímky. Zde je několik ukázkových scénářů:

* Přečtěte si komprimovaná data GZIP z objektu blob Azure, dekomprimujte je a zapište data výsledků do databáze Azure SQL. Definujete vstupní datovou sadu objektů `compression` `type` Blob Azure s vlastností jako GZIP.
* Čtení dat ze souboru ve formátu prostého textu z místního systému souborů, komprimujte je pomocí formátu GZip a zapište komprimovaná data do objektu blob Azure. Definujete výstupní datovou sadu objektů `compression` `type` Blob Azure s vlastností jako GZip.
* Přečtěte si soubor ZIP ze serveru FTP, dekomprimujte ho, abyste získali soubory dovnitř, a přiložte je do úložiště Azure Data Lake Store. Vstupní datovou sadu FTP `compression` `type` s vlastností definujete jako ZipDeflate.
* Přečtěte si data komprimovaná gzipz objektu blob Azure, dekomprimujte je, komprimujte je pomocí BZIP2 a zapište data výsledků do objektu blob Azure. Definujete vstupní datovou sadu `compression` `type` Objektů blob Azure s nastavenou na GZIP a výstupní datovou sadu s `compression` `type` nastavenou na BZIP2.

Chcete-li určit kompresi pro datovou sadu, použijte vlastnost **komprese** v datové sadě JSON jako v následujícím příkladu:

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

Část **komprese** má dvě vlastnosti:

* **Typ:** kompresní kodek, který může být **GZIP**, **Deflate**, **BZIP2**nebo **ZipDeflate**. Poznámka: Při použití kopírování aktivity dekompresi ZipDeflate soubor (y) a zapisovat `<path specified in dataset>/<folder named as source zip file>/`do úložiště dat jímky založené na souboru, soubory budou extrahovány do složky: .
* **Úroveň:** kompresní poměr, který může být **optimální** nebo **nejrychlejší**.

  * **Nejrychlejší:** Operace komprese by měla být dokončena co nejrychleji, a to i v případě, že výsledný soubor není optimálně komprimován.
  * **Optimální**: Operace komprese by měla být optimálně komprimována, i když operace trvá delší dobu.

    Další informace naleznete v tématu [Úroveň komprese.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)

> [!NOTE]
> Nastavení komprese nejsou podporována pro data v **avroformatu**, **orcformatu**nebo **formátu parkety**. Při čtení souborů v těchto formátech Data Factory detekuje a používá kompresní kodek v metadatech. Při zápisu do souborů v těchto formátech zvolte Data Factory pro tento formát výchozí kompresní kodek. Například ZLIB pro OrcFormat a SNAPPY pro ParketquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nepodporované typy souborů a formáty komprese

Funkce rozšiřitelnosti služby Azure Data Factory můžete použít k transformaci souborů, které nejsou podporované.
Dvě možnosti zahrnují funkce Azure a vlastní úkoly pomocí Azure Batch.

Můžete zobrazit ukázku, která používá funkci Azure k [extrahování obsahu souboru tar](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Další informace najdete v [tématu Aktivita funkce Azure](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Tuto funkci můžete také vytvořit pomocí vlastní aktivity dotnet. Další informace jsou k dispozici [zde](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Další kroky

Seznamte se s nejnovějšími podporovanými formáty a kompresemi souborů z [podporovaných formátů a kompresí](supported-file-formats-and-compression-codecs.md)souborů .
