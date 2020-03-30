---
title: Koncepce dovednostních sad a pracovní postup
titleSuffix: Azure Cognitive Search
description: Dovednosti jsou místo, kde ve službě Azure Cognitive Search nakončujete kanál obohacení umělou ai. Seznamte se s důležitými koncepty a podrobnostmi o složení sady dovedností.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191033"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Koncepty a složení sady dovedností v Azure Cognitive Search

Tento článek je určen pro vývojáře, kteří potřebují hlubší pochopení toho, jak funguje kanál obohacení a předpokládá, že máte koncepční pochopení procesu obohacení AI. Pokud jste nový tento koncept, začněte s:
+ [Obohacení umělou a ii v Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Knowledge Store (Preview)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Určení sady dovedností
Skillset je opakovaně použitelný prostředek v Azure Cognitive Search, který určuje kolekci kognitivnídovednosti používané pro analýzu, transformaci a obohacení obsahu textu nebo obrázku během indexování. Vytvoření sady dovedností umožňuje připojit obohacení textu a obrázků ve fázi příjem dat, extrahování a vytváření nových informací a struktur z nezpracovaného obsahu.

Skillset má tři vlastnosti:

+   ```skills```, neuspořádaný soubor dovedností, pro který platforma určuje pořadí provádění na základě vstupů požadovaných pro každou dovednost
+   ```cognitiveServices```, klíč kognitivních služeb potřebný pro fakturaci kognitivních dovedností
+   ```knowledgeStore```, účet úložiště, ve kterém budou promítnuty vaše obohacené dokumenty



Dovednosti jsou napsány v JSON. Můžete vytvořit komplexní skillsets s opakování a [větvení](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) pomocí [jazyka výrazu](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Jazyk výrazu používá zápis cesty [ukazatele JSON](https://tools.ietf.org/html/rfc6901) s několika úpravami k identifikaci uzlů ve stromu obohacení. Protáhne ```"/"``` úroveň nižší ```"*"``` ve stromu a funguje jako pro každý operátor v kontextu. Tyto koncepty jsou nejlépe popsány s příkladem. Pro ilustraci některých konceptů a schopností projdeme [ukázkové](knowledge-store-connect-powerbi.md) dovednosti v hotelech. Chcete-li zobrazit sadu dovedností po sledování pracovního postupu importu dat, budete muset použít klienta rozhraní REST API získat [skillset](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Strom obohacení

Chcete-li si představit, jak sada dovedností postupně obohacuje váš dokument, začněme s tím, jak dokument vypadá před jakýmkoli obohacením. Výstup prolomení dokumentu závisí na zdroji dat a vybraném konkrétním režimu analýzy. Toto je také stav dokumentu, ze kterého mohou [mapování polí](search-indexer-field-mappings.md) odevzdávat obsah při přidávání dat do indexu vyhledávání.
![Úložiště znalostí v diagramu potrubí](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Úložiště znalostí v diagramu potrubí")

Jakmile je dokument v kanálu obohacení, je reprezentován jako strom obsahu a přidružené obohacení. Tento strom je vytvořena jako výstup popraskání dokumentu. Formát stromu obohacení umožňuje kanálu obohacení připojit metadata i primitivní datové typy, není platný objekt JSON, ale může být promítán do platného formátu JSON. V následující tabulce je uveden stav dokumentu vstupujícího do kanálu obohacování:

|Zdroj dat\Režim analýzy|Výchozí|JSON, JSON Linky & CSV|
|---|---|---|
|Blob Storage|/dokument/obsah<br>/dokument/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|Není dostupné. |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|Není dostupné.|

 Při provádění dovedností přidávají nové uzly do stromu obohacení. Tyto nové uzly pak mohou být použity jako vstupy pro podřízené dovednosti, promítání do úložiště znalostí nebo mapování na pole indexu. Obohacení není proměnlivé: po vytvoření uzly nelze upravovat. Jak se vaše dovednosti zvažují, tak bude váš strom obohacení, ale ne všechny uzly ve stromu obohacení je třeba, aby se do indexu nebo úložiště znalostí. 

Můžete selektivně zachovat pouze podmnožinu obohacení do indexu nebo úložiště znalostí.
Pro zbytek tohoto dokumentu budeme předpokládat, že pracujeme s [příkladem recenzí hotelů](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), ale stejné koncepty platí pro obohacování dokumentů ze všech ostatních zdrojů dat.

### <a name="context"></a>Kontext
Každá dovednost vyžaduje kontext. Kontext určuje:
+   Počet provedení dovednosti na základě vybraných uzlů. Pro kontextové hodnoty kolekce ```/*``` typů přidání na konci bude mít za následek dovednost, která je vyvolána jednou pro každou instanci v kolekci. 
+   Kde ve stromu obohacení jsou přidány výstupy dovedností. Výstupy jsou vždy přidány do stromu jako podřízené uzel kontextu. 
+   Tvar vstupů. U víceúrovňových kolekcí bude nastavení kontextu na nadřazenou kolekci mít vliv na tvar vstupu pro dovednost. Například pokud máte strom obohacení se seznamem zemí, z nichž každá obohacena o seznam stavů obsahujících seznam PSČ.

|Kontext|Vstup|Tvar vstupu|Vyvolání dovednosti|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Seznam všech PSČ v zemi |Jednou za zemi |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Seznam PSČ ve státě | Jednou za kombinaci země a státu|

### <a name="sourcecontext"></a>SourceContext

Používá `sourceContext` se pouze ve vstupech dovednosti a [projekcích](knowledge-store-projection-overview.md). Používá se k vytvoření víceúrovňové vnořené objekty. Možná budete muset vytvořit nový objekt, aby jej buď předat jako vstup do dovednosti nebo projektu do úložiště znalostí. Jako uzly obohacení nemusí být platný objekt JSON ve stromu obohacení a odkazování uzel ve stromu pouze vrátí tento stav uzlu, když byl vytvořen, pomocí obohacení jako vstupy dovednosti nebo projekce vyžaduje vytvoření dobře tvarované ho json objektu. Umožňuje `sourceContext` vytvořit hierarchický, anonymní typ objektu, který by vyžadoval více dovedností, pokud jste používali pouze kontext. Použití `sourceContext` je uvedeno v další části. Podívejte se na výstup dovednosti, který generoval obohacení k určení, zda se jedná o platný objekt JSON a nikoli primitivní typ.

### <a name="projections"></a>Projekce

Projekce je proces výběru uzlů ze stromu obohacení, který má být uložen v úložišti znalostí. Projekce jsou vlastní tvary dokumentu (obsah a obohacení), které mohou být výstupem jako projekce tabulky nebo objektu. Další informace o práci s projekcemi naleznete v [tématu práce s projekcemi](knowledge-store-projection-overview.md).

![Možnosti mapování polí](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Možnosti mapování polí pro kanál obohacení")

Výše uvedený diagram popisuje volič, se kterým pracujete, na základě toho, kde se nacházíte v kanálu obohacení.

## <a name="generate-enriched-data"></a>Generování obohacených dat 

Pojďme nyní krok přes hotel recenze skillset, můžete sledovat [výukový program](knowledge-store-connect-powerbi.md) vytvořit skillset nebo [zobrazit](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) skillset. Podíváme se na:

* jak se strom obohacení vyvíjí s prováděním každé dovednosti 
* jak kontext a vstupy pracují k určení, kolikrát dovednost provede 
* jaký tvar vstupu je založen na kontextu. 

Vzhledem k tomu, že používáme režim oddělené analýzy textu pro indexer, dokument v rámci procesu obohacení představuje jeden řádek v souboru CSV.

### <a name="skill-1-split-skill"></a>dovednost #1: Split dovednost 

![strom obohacení po popraskání dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Strom obohacení po popraskání dokumentu a před provedením dovednosti")

S dovednostním ```"/document/reviews_text"```kontextem bude tato dovednost `reviews_text`provedena jednou pro . Výstup dovednosti je seznam, `reviews_text` kde je bloků do segmentů 5000 znaků. Výstup z rozdělení dovednosti `pages` je pojmenován a přidán do stromu obohacení. Tato `targetName` funkce umožňuje přejmenovat výstup dovednosti před přidáním do stromu obohacení.

Strom obohacení má nyní nový uzel umístěn v kontextu dovednosti. Tento uzel je k dispozici pro všechny dovednosti, projekce nebo mapování výstupního pole.


Kořenový uzel pro všechny obohacení je `"/document"`. Při práci s indexery `"/document"` objektů blob bude `"/document/content"` mít `"/document/normalized_images"`uzel podřízené uzly a . Při práci s daty CSV, jak jsme v tomto příkladu, `"/document"`názvy sloupců se mapují na uzly pod . Chcete-li získat přístup k některému z obohacení přidané do uzlu dovedností, je potřeba úplnou cestu pro obohacení. Chcete-li například použít text z ```pages``` uzlu jako vstup do jiné dovednosti, budete ```"/document/reviews_text/pages/*"```jej muset zadat jako .
 
 ![strom obohacení po #1 dovedností](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Strom obohacení po provedení #1 dovedností")

### <a name="skill-2-language-detection"></a>Detekce znalosti #2 jazyka
 Zatímco dovednost detekce jazyka je třetí (dovednost #3) dovednost definovaná v skillset, je to další dovednost, kterou je třeba provést. Vzhledem k tomu, že není blokován vyžadováním jakýchkoli vstupů, bude spuštěn souběžně s předchozí dovedností. Stejně jako dovednost rozdělení, která mu předcházela, je dovednost detekce jazyka vyvolána také jednou pro každý dokument. Strom obohacení má nyní nový uzel pro jazyk.
 ![strom obohacení po #2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Strom obohacení po provedení #2 dovedností")
 
 ### <a name="skill-3-key-phrases-skill"></a>Dovednost #3: Klíčové fráze dovednost 

Vzhledem k ```/document/reviews_text/pages/*``` kontextu klíčové fráze dovednost bude vyvolána jednou pro `pages` každou z položek v kolekci. Výstup z dovednosti bude uzel pod přidruženým prvkem stránky. 

 Nyní byste měli být schopni podívat se na zbytek dovedností v skillset a vizualizovat, jak strom obohacení bude i nadále růst s prováděním každé dovednosti. Některé dovednosti, jako je například dovednost sloučení a dovednosti tvarovače, také vytvářejí nové uzly, ale používají pouze data z existujících uzlů a nevytvářejí nové čisté obohacení.

![obohacení strom po všech dovedností](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Obohacení strom po všech dovedností")

Barvy spojnice ve stromu výše označují, že obohacení byly vytvořeny různými dovednostmi a uzly bude nutné řešit jednotlivě a nebude součástí objektu vráceného při výběru nadřazeného uzlu.

## <a name="save-enrichments-in-a-knowledge-store"></a>Ukládání obohacení v úložišti znalostí 

Dovednosti také definují úložiště znalostí, kde lze promítat vaše obohacené dokumenty jako tabulky nebo objekty. Chcete-li uložit obohacená data do úložiště znalostí, definujte sadu projekcí pro obohacený dokument. Další informace o úložišti znalostí najdete v přehledu [úložiště znalostí](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Krájení projekcí

Při definování skupiny projekce tabulky lze jeden uzel ve stromu obohacení rozdělit do více souvisejících tabulek. Pokud přidáte tabulku se zdrojovou cestou, která je podřízenou existující projekcí tabulky, výsledný podřízený uzel nebude podřízeným existujícím projekcí tabulky, ale bude promítán do nové, související tabulky. Tato technika krájení umožňuje definovat jeden uzel v dovednostech tvarovače, který může být zdrojem pro všechny projekce tabulky. 

### <a name="shaping-projections"></a>Tvarování výstupků

Existují dva způsoby, jak definovat projekce. Dovednosti shaperu můžete použít k vytvoření nového uzlu, který je kořenovým uznem pro všechny obohacení, které promítáte. Potom ve svých projekcích byste odkazovali pouze na výstup dovednosti tvarovače. Můžete také vstěhovat tvar projekce v rámci samotné definice projekce.

Shaper přístup je podrobnější než inline tvarování, ale zajišťuje, že všechny mutace stromu obohacení jsou obsaženy v dovednosti a výstup je objekt, který lze znovu použít. Inline tvarování umožňuje vytvořit obrazec, který potřebujete, ale je anonymní objekt a je k dispozici pouze pro projekci, pro kterou je definován. Přístupy lze použít společně nebo samostatně. Sada dovedností vytvořená v pracovním postupu portálu obsahuje obojí. Používá shaper dovednost pro projekce tabulky, ale také používá inline tvarování pro projekt ovat klíčové fráze tabulky.

Chcete-li rozšířit příklad, můžete odstranit vřádkové tvarování a použít dovednost shaper k vytvoření nového uzlu pro klíčové fráze. Chcete-li vytvořit obrazec promítaný `hotelReviewsDocument` `hotelReviewsPages`do `hotelReviewsKeyPhrases`tří tabulek, konkrétně , , a , jsou obě možnosti popsány v následujících částech.


#### <a name="shaper-skill-and-projection"></a>Shaper dovednost a projekce 

> [!Note]
> Některé sloupce z tabulky dokumentů byly odebrány z tohoto příkladu stručnosti.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

S `tableprojection` uzem definovaným `outputs` v části výše, nyní můžeme použít funkci `tableprojection` krájení k promítnutí částí uzlu do různých tabulek:

> [!Note]
> Toto je pouze úryvek projekce v rámci konfigurace úložiště znalostí.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Inline tvarovací projekce

Přístup k inline tvarování nevyžaduje dovednost tvarovače, protože všechny tvary potřebné pro projekce jsou vytvořeny v době, kdy jsou potřeba. Chcete-li promítnout stejná data jako v předchozím příkladu, bude možnost vsazení vypadat takto:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Jedním z pozorování z obou `"Keyphrases"` přístupů je, `"sourceContext"`jak jsou promítnuty hodnoty pomocí . Uzel, `"Keyphrases"` který obsahuje kolekci řetězců, je sám podřízený text stránky. Protože však projekce vyžadují objekt JSON a stránka je `"sourceContext"` primitivní (řetězec), slouží k zabalení klíčové fráze do objektu s pojmenovanou vlastností. Tato technika umožňuje i primitivy, které mají být promítány nezávisle.

## <a name="next-steps"></a>Další kroky

Jako další krok vytvořte svůj první skillset s kognitivními dovednostmi.

> [!div class="nextstepaction"]
> [Vytvořte si první skillset](cognitive-search-defining-skillset.md).
