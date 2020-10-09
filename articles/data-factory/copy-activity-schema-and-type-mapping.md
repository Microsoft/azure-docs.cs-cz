---
title: Mapování schématu a datového typu v aktivitě kopírování
description: Přečtěte si, jak aktivita kopírování v Azure Data Factory mapuje schémata a datové typy ze zdrojových dat na data jímky.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: b48fb28a56cdc1c836233cd2bd03a1f9e750a0a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85249648"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Mapování schématu a datového typu v aktivitě kopírování
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje, jak Azure Data Factory aktivita kopírování provádí mapování schématu a mapování datových typů ze zdrojových dat na data jímky.

## <a name="schema-mapping"></a>Mapování schématu

### <a name="default-mapping"></a>Výchozí mapování

Ve výchozím nastavení aktivita kopírování mapuje zdrojová data do jímky **podle názvů sloupců** , a to způsobem, který rozlišuje velká a malá písmena. Pokud jímka neexistuje, například zápis do souborů, názvy zdrojových polí budou trvalé jako názvy jímky. Toto výchozí mapování podporuje flexibilní schémata a posun schématu ze zdroje na jímku od spuštění po spuštění – všechna data vrácená zdrojovým úložištěm dat lze zkopírovat do jímky.

Pokud je váš zdroj textový soubor bez čáry záhlaví, vyžaduje se [explicitní mapování](#explicit-mapping) , protože zdroj neobsahuje názvy sloupců.

### <a name="explicit-mapping"></a>Explicitní mapování

Můžete také zadat explicitní mapování pro přizpůsobení mapování sloupce nebo pole ze zdroje na jímku podle vašich potřeb. Pomocí explicitního mapování můžete kopírovat pouze částečně zdrojová data do jímky nebo mapovat zdrojová data na jímku s různými názvy nebo změnit tvar tabulkových/hierarchických dat. Aktivita kopírování:

1. Přečte data ze zdroje a určí zdrojové schéma.
2. Použije vaše definované mapování.
3. Zapisuje data do jímky.

Přečtěte si další informace:

- [Tabulkový zdroj do tabulkové jímky](#tabular-source-to-tabular-sink)
- [Hierarchický zdroj do tabulkové jímky](#hierarchical-source-to-tabular-sink)
- [Tabelární nebo hierarchický zdroj pro hierarchickou jímku](#tabularhierarchical-source-to-hierarchical-sink)

Můžete nakonfigurovat mapování Data Factory vytváření uživatelského rozhraní – > na kartě mapování > aktivity kopírování, nebo programově určit mapování v poli Kopírovat aktivitu – > `translator` vlastnost. Následující vlastnosti jsou podporovány v `translator`  ->  `mappings` objektech > Array-> `source` a `sink` , což odkazuje na konkrétní sloupec nebo pole pro mapování dat.

| Vlastnost | Popis                                                  | Povinné |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Název sloupce/pole zdroje nebo jímky. Platí pro tabelární zdroj a jímku. | Ano      |
| řadový  | Index sloupce. Začněte od 1. <br>Použít a vyžádat při použití oddělovače textu bez řádku záhlaví. | No       |
| program     | Výraz cesty JSON pro každé pole k extrakci nebo mapování Platí pro hierarchické zdroje a jímky, například Cosmos DB, MongoDB nebo konektory REST.<br>Pro pole v rámci kořenového objektu začíná cesta JSON kořenem `$` ; pro pole v poli, které je zvoleno `collectionReference` vlastností, cesta JSON začíná z prvku pole bez `$` . | No       |
| typ     | Data Factory pomocný datový typ sloupce zdroj nebo jímka. Obecně platí, že tuto vlastnost nemusíte zadávat ani měnit. Přečtěte si další informace o [mapování datových typů](#data-type-mapping). | No       |
| jazyková verze  | Jazyková verze zdroje nebo sloupce jímky. Použijte, pokud je typ `Datetime` nebo `Datetimeoffset` . Výchozí formát je `en-us`.<br>Obecně platí, že tuto vlastnost nemusíte zadávat ani měnit. Přečtěte si další informace o [mapování datových typů](#data-type-mapping). | No       |
| formát   | Řetězec formátu, který se má použít, pokud je typ `Datetime` nebo `Datetimeoffset` . Informace o formátování hodnoty DateTime naleznete v tématu [Vlastní řetězce formátu data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . Obecně platí, že tuto vlastnost nemusíte zadávat ani měnit. Přečtěte si další informace o [mapování datových typů](#data-type-mapping). | No       |

Následující vlastnosti jsou podporovány v nástroji `translator` kromě následujících `mappings` :

| Vlastnost            | Popis                                                  | Povinné |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Použijte při kopírování dat z hierarchického zdroje, například Cosmos DB, MongoDB nebo konektory REST.<br>Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pole se stejným vzorem a převést je na každý řádek na objekt, zadejte cestu JSON tohoto pole, aby se provedlo křížové použití. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Tabulkový zdroj do tabulkové jímky

Pokud například chcete zkopírovat data ze Salesforce do Azure SQL Database a explicitně namapovat tři sloupce:

1. Na kartě > aktivity kopírování klikněte na tlačítko **importovat schéma** a importujte zdrojová i schémata jímky.

2. Namapujte potřebná pole a vylučte/odstraňujte zbytek.

![Mapovat tabulkovou na tabulkové](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Stejné mapování lze nakonfigurovat jako následující v datové části aktivity kopírování (viz `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Chcete-li kopírovat data z textových souborů s oddělovači bez řádku záhlaví, jsou sloupce zobrazeny podle pořadového čísla namísto názvů. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Hierarchický zdroj do tabulkové jímky

Při kopírování dat z hierarchického zdroje do tabulkové jímky, aktivita kopírování podporuje následující možnosti:

- Extrakce dat z objektů a polí.
- Umožňuje příčně použít více objektů se stejným vzorem z pole. v takovém případě můžete převést jeden objekt JSON na více záznamů v tabulkovém výsledku.

Pro pokročilejší transformaci hierarchických tabulek můžete použít [tok dat](concepts-data-flow-overview.md). 

Například pokud máte zdrojový MongoDB dokument s následujícím obsahem:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

A chcete ho zkopírovat do textového souboru v následujícím formátu s hlavičkou záhlaví, a to tak, že sloučíte data uvnitř pole *(order_pd a order_price)* a spojíte se s běžnými kořenovými informacemi *(číslo, datum a město)*:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Toto mapování můžete definovat při vytváření Data Factory uživatelského rozhraní:

1. Na kartě > aktivity kopírování klikněte na tlačítko **importovat schéma** a importujte zdrojová i schémata jímky. Když Data Factory při importu schématu vyvzorkuje nejvyšší počet objektů, můžete ho přidat do správné vrstvy v hierarchii – najeďte na existující název pole a vyberte přidat uzel, objekt nebo pole.

2. Vyberte pole, ze kterého chcete iterovat a extrahujte data. Automaticky se vyplní jako **odkaz na kolekci**. Poznámka pro takovou operaci je podporována pouze jedno pole.

3. Namapujte potřebná pole na jímku. Data Factory automaticky určuje odpovídající cesty JSON pro hierarchickou stranu.

![Mapování hierarchicky na tabelární pomocí uživatelského rozhraní](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Můžete také přepnout na **Rozšířený editor**. v takovém případě můžete přímo zobrazit a upravit cesty JSON polí. Pokud se rozhodnete přidat nové mapování v tomto zobrazení, zadejte cestu JSON.

![Mapování hierarchicky na tabulkové pomocí rozšířeného editoru](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Stejné mapování lze nakonfigurovat jako následující v datové části aktivity kopírování (viz `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Tabelární nebo hierarchický zdroj pro hierarchickou jímku

Tok činnosti koncového uživatele je podobný [hierarchickému jímky jako u tabelárních umyvadel](#hierarchical-source-to-tabular-sink). 

Při kopírování dat z tabulkového zdroje do hierarchické jímky není podporováno psaní do pole uvnitř objektu.

Při kopírování dat z hierarchického zdroje do hierarchické jímky můžete také zachovat hierarchii celé vrstvy, a to tak, že vyberete objekt nebo pole a namapujete ji na jímku bez toho, aby se dotýkala vnitřních polí.

Pro pokročilejší transformaci přetvarování dat můžete použít [tok dat](concepts-data-flow-overview.md). 

### <a name="parameterize-mapping"></a>Parametrizovat mapování

Pokud chcete vytvořit kanál založena a dynamicky kopírovat velký počet objektů, určete, zda lze využít [výchozí mapování](#default-mapping) , nebo musíte definovat [explicitní mapování](#explicit-mapping) pro příslušné objekty.

Pokud je potřeba explicitní mapování, můžete:

1. Definujte parametr s typem objektu na úrovni kanálu, například `mapping` .

2. Parametrizovat mapování: aktivita kopírování na > karta mapování, zvolte možnost Přidat dynamický obsah a vyberte výše uvedený parametr. Datová část aktivity by byla následující:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Sestavte hodnotu, která bude předána parametru mapování. Měl by se jednat o celý objekt `translator` definice, přečtěte si část ukázky v tématu [explicitní mapování](#explicit-mapping) . Například u tabulkového zdroje do tabulkového kopírování by měla být hodnota `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Mapování datových typů

Aktivita kopírování provádí typy zdrojů pro mapování typů jímky s následujícím tokem: 

1. Převeďte ze zdrojových nativních datových typů na Azure Data Factory dočasné datové typy.
2. Automaticky převede dočasný datový typ podle potřeby tak, aby odpovídal odpovídajícím typům jímky, které lze použít pro [výchozí mapování](#default-mapping) i pro [explicitní mapování](#explicit-mapping).
3. Převeďte z Azure Data Factory dočasných datových typů na jímky nativních datových typů.

Aktivita kopírování aktuálně podporuje následující dočasné datové typy: Boolean, Byte, byte array, DateTime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, TimeSpan, UInt16, UInt32 a UInt64.

Následující převody datových typů jsou podporovány mezi dočasnými typy ze zdroje do jímky.

| Source\Sink | Logická hodnota | Bajtové pole | Decimal | Datum a čas <small>(1)</small> | Plovoucí desetinná čárka <small>(2)</small> | Identifikátor GUID | Celé číslo <small>(3)</small> | Řetězec | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Logická hodnota     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Bajtové pole  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Datum/čas   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Decimal     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Plovoucí desetinná čárka | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Identifikátor GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Integer     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Řetězec      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) datum a čas zahrnuje DateTime a DateTimeOffset.

(2) float-Point zahrnuje jednoduchá a Dvojitá přesnost.

(3) celé číslo zahrnuje SByte, Byte, Int16, UInt16, Int32, UInt32, Int64 a UInt64.

> [!NOTE]
> - Tento převod datového typu se v současnosti podporuje při kopírování mezi tabelárními daty. Hierarchické zdroje a jímky nejsou podporovány, což znamená, že neexistuje žádný typ uživatelsky definovaného převodu dat mezi zdrojem a dočasnými typy jímky.
> - Tato funkce funguje s nejnovějším modelem DataSet. Pokud tuto možnost nevidíte v uživatelském rozhraní, zkuste vytvořit novou datovou sadu.

Následující vlastnosti jsou podporovány v aktivitě kopírování pro převod datového typu (v části `translator` část pro programový vývoj):

| Vlastnost                         | Popis                                                  | Povinné |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Povolte nové prostředí pro konverzi datových typů. <br>Výchozí hodnota je false z důvodu zpětné kompatibility.<br><br>Pro nové aktivity kopírování vytvořené prostřednictvím Data Factory vytváření uživatelského rozhraní od 12. června 2020 je tento převod datového typu ve výchozím nastavení povolený pro dosažení optimálního prostředí a u příslušných scénářů můžete zobrazit následující nastavení převodu typů na kartě aktivity kopírování – > mapování. <br>Chcete-li vytvořit kanál programově, je nutné explicitně nastavit `typeConversion` vlastnost na hodnotu true, chcete-li ji povolit.<br>Pro existující aktivity kopírování vytvořené před vydáním této funkce se nezobrazí možnosti konverze typu na Data Factory vytváření uživatelského rozhraní pro zpětnou kompatibilitu. | No       |
| typeConversionSettings           | Skupina nastavení převodu typů. Použijte, pokud `typeConversion` je nastavená na `true` . V rámci této skupiny jsou všechny níže uvedené vlastnosti. | No       |
| *Položk `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Povolí zkracování dat při převodu zdrojových dat na jímku s jiným typem během kopírování, například z desítkového čísla na celé číslo od DatetimeOffset do data a času. <br>Výchozí hodnota je true (pravda). | No       |
| treatBooleanAsNumber             | Považovat logické hodnoty jako čísla, například true jako 1.<br>Výchozí hodnota je false. | No       |
| dateTimeFormat                   | Formátovací řetězec při konverzi mezi daty bez posunutí a řetězce časového pásma, například `yyyy-MM-dd HH:mm:ss.fff` .  Podrobné informace najdete v tématu [Vlastní řetězce formátu data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| dateTimeOffsetFormat             | Formátovací řetězec při konverzi mezi daty pomocí posunutí a řetězců časového pásma, například `yyyy-MM-dd HH:mm:ss.fff zzz` .  Podrobné informace najdete v tématu [Vlastní řetězce formátu data a času](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| timeSpanFormat                   | Řetězec formátu při převodu mezi časovými obdobími a řetězci, například `dd\.hh\:mm` . Podrobné informace najdete v tématu [Vlastní řetězce formátu TimeSpan](https://docs.microsoft.com/dotnet/standard/base-types/custom-timespan-format-strings) . | No       |
| jazyková verze                          | Informace o jazykové verzi, která má být použita při převodu typů, například `en-us` nebo `fr-fr` . | No       |

**Příklad:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Starší modely

> [!NOTE]
> Následující modely pro mapování zdrojových sloupců/polí na jímku jsou stále podporovány, protože jsou z důvodu zpětné kompatibility. Doporučujeme použít nový model uvedený v [mapování schématu](#schema-mapping). Uživatelské rozhraní pro vytváření Data Factory bylo přepnuto na generování nového modelu.

### <a name="alternative-column-mapping-legacy-model"></a>Alternativní mapování sloupce (starší model)

Můžete určit aktivitu kopírování – > `translator`  ->  `columnMappings` k mapování mezi daty tabelárních ve tvaru. V tomto případě je oddíl "Structure" vyžadován pro vstupní i výstupní datové sady. Mapování sloupce podporuje **mapování všech nebo dílčích sloupců ve struktuře zdrojové datové sady na všechny sloupce ve struktuře datové sady jímky**. Následující jsou chybové stavy, jejichž výsledkem je výjimka:

- Výsledek dotazu na zdrojové úložiště dat nemá název sloupce, který je zadaný v části struktura vstupní datové sady.
- Úložiště dat jímky (Pokud s předem definovaným schématem) nemá název sloupce, který je zadaný v části struktura výstupní datové sady.
- Buď méně sloupců, nebo více sloupců ve struktuře datové sady jímky, než je uvedeno v mapování.
- Duplicitní mapování

V následujícím příkladu má vstupní datová sada strukturu a odkazuje na tabulku v místní databázi Oracle.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

V této ukázce má výstupní datová sada strukturu a odkazuje na tabulku v Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Následující JSON definuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapované na sloupce v jímky pomocí vlastnosti **Translator**  ->  **ColumnMappings** .

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Pokud používáte syntaxi `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` pro k určení mapování sloupce, je stále podporováno tak, jak je.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternativní mapování schématu (starší model)

Můžete určit aktivitu kopírování – > `translator`  ->  `schemaMapping` k mapování mezi hierarchicky naformátovaná data a tabulková data, například kopírovat z MongoDB/REST do textového souboru a kopírovat z Oracle do Azure Cosmos DB rozhraní API pro MongoDB. V části aktivita kopírování jsou podporovány následující vlastnosti `translator` :

| Vlastnost            | Popis                                                  | Povinné |
| :------------------ | :----------------------------------------------------------- | :------- |
| typ                | Vlastnost Type pro překladatele aktivity kopírování musí být nastavená na: **TabularTranslator** . | Ano      |
| schemaMapping       | Kolekce párů klíč-hodnota, která představuje vztah mapování **ze strany zdroje na stranu jímky**.<br/>- **Key:** představuje zdroj. V poli **tabelární zdroj**zadejte název sloupce definovaný ve struktuře datové sady. u **hierarchického zdroje**zadejte výraz cesty JSON pro každé pole, které se má extrahovat a mapovat.<br>- **Hodnota:** reprezentuje jímku. Pro **tabulkovou jímku**zadejte název sloupce definovaný ve struktuře datové sady. u **hierarchické jímky**zadejte výraz cesty JSON pro každé pole, které chcete extrahovat a mapovat. <br>V případě hierarchických dat pro pole v části kořenový objekt začíná cesta JSON kořenem $;. pro pole uvnitř pole, které je zvoleno `collectionReference` vlastností, začíná cesta JSON od elementu pole. | Ano      |
| collectionReference | Pokud chcete iterovat a extrahovat data z objektů **uvnitř pole** pole se stejným vzorem a převést je na každý řádek na objekt, zadejte cestu JSON tohoto pole, aby se provedlo křížové použití. Tato vlastnost je podporována pouze v případě, že jsou hierarchická data zdrojem. | No       |

**Příklad: kopírování z MongoDB do Oracle:**

Například pokud máte MongoDB dokument s následujícím obsahem:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

a chcete ho zkopírovat do tabulky Azure SQL v následujícím formátu, a to tak, že sloučíte data uvnitř pole *(order_pd a order_price)* a spojíte se společnými kořenovými informacemi *(číslo, datum a město)*:

| orderNumber | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Nakonfigurujte pravidlo mapování schématu jako následující ukázku JSON aktivity kopírování:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
