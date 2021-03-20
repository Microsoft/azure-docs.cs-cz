---
title: Dovednosti koncepty a pracovní postup
titleSuffix: Azure Cognitive Search
description: Dovednosti je místo, kde vytvoříte kanál pro rozšíření AI v Azure Kognitivní hledání. Seznamte se s důležitými koncepty a podrobnostmi o dovednosti složení.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558109"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Dovednosti koncepty v Azure Kognitivní hledání

Tento článek je určen pro vývojáře, kteří potřebují hlubší porozumění konceptům a sestavování dovednosti a předpokládají, že se proces obohacení AI předpokládá. Pokud s tímto konceptem začínáte, začněte s [rozšířením AI v Azure kognitivní hledání](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Představujeme dovednosti

Dovednosti je opakovaně použitelný prostředek v Azure Kognitivní hledání, který je připojený k indexeru, a určuje kolekci dovedností, které se používají k analýze, transformaci a obohacení textu nebo obrázku v průběhu indexování. Dovednosti mají vstupy a výstupy a často se výstup jedné dovednosti projeví jako vstup jiného v řetězu nebo sekvenci procesů.

Dovednosti má tři hlavní vlastnosti:

+ `skills`, neuspořádaná kolekce dovedností, pro kterou platforma Určuje sekvenci provádění na základě vstupů vyžadovaných pro každou dovednost.
+ `cognitiveServices`, klíč Cognitive Services prostředku, který provádí zpracování obrázků a textu pro dovednosti, které obsahují integrované dovednosti.
+ `knowledgeStore`, (nepovinný) účet Azure Storage, kde se budou obohacené dokumenty promítnout. Rozšířené dokumenty jsou také využívány vyhledávacími indexy.

Dovednosti jsou vytvořeny ve formátu JSON. Následující příklad je mírně zjednodušená verze tohoto [hotelu-recenze dovednosti](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), která slouží k ilustraci konceptů v tomto článku. 

První dvě dovednosti jsou uvedené níže:

+ #1 dovednosti je [dovednost rozdělená na text](cognitive-search-skill-textsplit.md) , která přijímá obsah pole "reviews_text" jako vstup a rozděluje tento obsah na "stránky" na 5000 znaků jako výstup.
+ #2 dovedností je [dovednost mínění pro zjišťování](cognitive-search-skill-sentiment.md) přijímá jako vstup hodnotu stránky a jako výstup vytvoří nové pole s názvem "mínění", které obsahuje výsledky analýzy mínění.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> Komplexní dovednosti můžete vytvářet pomocí smyček a větvení, a to pomocí [podmíněné dovednosti](cognitive-search-skill-conditional.md) pro vytváření výrazů. Syntaxe je založena na zápisu cesty [ukazatele JSON](https://tools.ietf.org/html/rfc6901) , s několika úpravami k identifikaci uzlů ve stromu obohacení. Ve `"/"` stromové struktuře projde nižší úroveň a  `"*"` funguje jako operátor for-each v kontextu. Syntaxe je znázorněna v mnoha příkladech v tomto článku. 

### <a name="enrichment-tree"></a>Strom obohacení

V průběhu [kroků v kanálu rozšíření](cognitive-search-concept-intro.md#enrichment-steps)zpracovává zpracování obsahu postupné fáze *dokumentu* , při které se ze zdroje extrahují text a obrázky. Obsah obrázku je pak možné směrovat do dovedností, které určují zpracování obrázků, zatímco textový obsah je zařazený do fronty pro zpracování textu. U zdrojových dokumentů, které obsahují velké množství textu, můžete nastavit *režim analýzy* v indexeru tak, aby se text segmentoval na menší bloky dat, aby bylo možné lépe dosáhnout optimálního zpracování. 

![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Znalostní úložiště v diagramu kanálu")

Jakmile je dokument v kanálu obohacení, je reprezentován jako strom obsahu a přidružených rozšíření. Tento strom se vytvoří jako výstup odhalujícího dokumentu.  Stromová struktura obohacení umožňuje kanálu obohacení připojit metadata k ještě primitivním datovým typům, nejedná se o platný objekt JSON, ale lze jej převést do platného formátu JSON. Následující tabulka ukazuje stav dokumentu, který se zadává do kanálu rozšíření:

|Režim Source\Parsing dat|Výchozí|JSON, řádky JSON & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/Document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|– |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|–|

 Při provádění dovedností přidávají nové uzly do stromu obohacení. Tyto nové uzly pak mohou být použity jako vstupy pro dovednosti s využitím pro příjem dat, projekci do obchodu Knowledge Store nebo mapování na pole indexu. Rozšíření nejsou proměnlivá: po vytvoření se uzly nedají upravovat. Vzhledem k tomu, že vaše dovednostiy jsou složitější, takže se strom pro rozšíření, ale ne všechny uzly ve stromu pro rozšíření, nemusí dělat na index nebo na obchod znalostní báze. 

Můžete selektivně zachovat jenom podmnožinu obohacení na index nebo úložiště znalostní báze.

### <a name="context"></a>Kontext

Každá dovednost vyžaduje kontext. Kontext určuje:

+ Počet, kolikrát se dovednost spustí na základě vybraných uzlů. V případě hodnot kontextu typu kolekce je přidání `/*` na konci výsledkem uplatnění dovednosti u každé instance v kolekci. 

+ Ve stromu obohacení se přidávají výstupy dovedností. Výstupy jsou vždy přidány do stromu jako podřízené objekty uzlu kontextu. 

+ Tvar vstupů. U kolekcí s více úrovněmi ovlivní nastavení kontextu pro nadřazenou kolekci tvar vstupu pro dovednost. Například pokud máte strom obohacení se seznamem zemí nebo oblastí, každý obohacený seznamem stavů, které obsahují seznam kódů ZIP.

|Kontext|Vstup|Tvar vstupu|Vyvolání dovedností|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Seznam všech PSČ v zemi nebo oblasti |Jednou za zemi nebo oblast |
|`/document/countries/*/states/*` |'/Document/countries/*/States/*/ZipCodes/* ' ' |Seznam kódů PSČ ve stavu | Jednou za kombinaci země/oblasti a státu|

## <a name="generate-enriched-data"></a>Generovat obohacená data 

Pomocí [dovednostich recenzí hotelů](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) jako referenčního bodu si projdeme:

+ Způsob vývoje stromu rozšíření s prováděním jednotlivých dovedností
+ Jak kontext a vstupy pracují k určení, kolikrát se dovednost spustí
+ Jak je tvar vstupu založen na kontextu

"Dokument" v rámci procesu rozšíření představuje jeden řádek (přezkoumání hotelu) ve zdrojovém souboru hotel_reviews.csv.

### <a name="skill-1-split-skill"></a>Dovednost #1: rozdělit dovednost

Pokud se zdrojový obsah skládá z velkých bloků textu, je vhodné ho rozdělit na menší součásti, aby byla větší přesnost jazyka, mínění a detekce klíčových frází. K dispozici jsou dvě zrna: stránky a věty. Stránka se skládá z přibližně 5000 znaků.

Dovednost rozdělení textu je obvykle první v dovednosti.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

V souvislosti s dovednostmi dovedností se u každého z nich provede `"/document/reviews_text"` rozdělená dovednost `reviews_text` . Výstup dovedností je seznam, ve kterém `reviews_text` je rozdělen do segmentů 5000 znaků. Výstup z rozdělené dovednosti se jmenuje `pages` a přidá se do stromu obohacení. `targetName`Funkce umožňuje přejmenovat výstup dovedností před přidáním do stromu obohacení.

Strom rozkládání teď má nový uzel umístěný pod rámec dovednosti. Tento uzel je k dispozici pro jakékoli mapování dovedností, projekce nebo výstupních polí. Konceptuální struktura vypadá takto:

![strom obohacení po vytrhlině dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Strom obohacení po prolomení dokumentů a před provedením dovedností")

Kořenový uzel pro všechna rozšíření je `"/document"` . Při práci s indexery objektů BLOB `"/document"` bude uzel mít podřízené uzly `"/document/content"` a `"/document/normalized_images"` . Při práci s daty ve formátu CSV, jak je v tomto příkladu, se názvy sloupců mapují na uzly níže `"/document"` . 

Aby bylo možné získat přístup k jakémukoli obohacení rozšíření přidaným do uzlu dovedností, je nutná úplná cesta pro rozšíření. Například pokud chcete použít text z ```pages``` uzlu jako vstup pro jinou dovednost, budete ho muset zadat jako ```"/document/reviews_text/pages/*"``` .
 
 ![strom obohacení po #1 dovednosti](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Strom obohacení po provedení #1 dovednosti")

### <a name="skill-2-language-detection"></a>Zjišťování jazyka pro dovednost #2

Dokumenty recenze pro Hotel zahrnují zpětnou vazbu zákazníků vyjádřenou v několika jazycích. Dovednost detekce jazyka Určuje, který jazyk se používá. Výsledek se pak předává extrakci klíčových frází a detekci mínění, při rozpoznávání mínění a frází zohledňuje jazyk.

I když je dovednost detekce jazyka třetí (dovednostní #3) definovaná v dovednosti, jedná se o další dovednost, kterou je potřeba provést. Vzhledem k tomu, že není zablokované vyžadováním jakýchkoli vstupů, spustí se paralelně s předchozí dovedností. Stejně jako rozdělení dovedností, které předcházejí, je pro každý dokument také vyvolána dovednost detekce jazyka. Strom rozrozšíření teď má nový uzel pro jazyk.

 ![strom obohacení po #2 dovednosti](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Strom obohacení po provedení #2 dovednosti")
 
 ### <a name="skill-3-key-phrases-skill"></a>Dovednost #3: dovednost klíčových frází 

Vzhledem k, že se kontext `/document/reviews_text/pages/*` dovedností klíčových frází vyvolá jednou pro každou položku v `pages` kolekci. Výstup z dovednosti bude uzel pod přidruženým prvkem stránky. 

 Nyní byste měli být schopni se podívat na zbytek dovedností v dovednosti a vizualizovat, jak bude strom rozšíření nadále rostoucí s prováděním jednotlivých dovedností. Některé dovednosti, jako je například dovednost sloučení a kvalifikace Shaper, také vytvářejí nové uzly, ale používají data z existujících uzlů a nevytvářejí čisté nové rozšíření.

![strom obohacení po všech dovednostech](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Strom obohacení po všech dovednostech")

Barvy konektorů ve stromu výše označují, že rozšíření byly vytvořeny různými dovednostmi a uzly budou muset být řešeny individuálně a nebudou součástí objektu vráceného při výběru nadřazeného uzlu.

## <a name="save-enrichments"></a>Uložit obohacení

V Azure Kognitivní hledání ukládá indexer výstup, který vytvoří. Jeden z výstupů je vždy [index s možností prohledávání](search-what-is-an-index.md). Zadání indexu je požadavek a když připojíte dovednosti, data ingestovaná indexem zahrnují obsah rozšíření. Výstupy specifických dovedností, jako jsou klíčové fráze nebo mínění, jsou obvykle ingestované do indexu v poli vytvořeném pro tento účel.

V případě potřeby může indexer také odeslat výstup do [úložiště znalostí](knowledge-store-concept-intro.md) pro využití v jiných nástrojích nebo procesech. Znalostní databáze je definována jako součást dovednosti. Jeho definice určuje, jestli jsou obohacené dokumenty probíhají jako tabulky nebo objekty (soubory nebo objekty BLOB). Tabulkové projekce jsou vhodné pro interaktivní analýzu v nástrojích, jako je Power BI, zatímco soubory a objekty BLOB se obvykle používají v datových věd nebo podobných procesech. V této části se dozvíte, jak může složení dovednosti tvarovat tabulky nebo objekty, které chcete projektovat.

### <a name="projections"></a>Projekce

U obsahu, který se zaměřuje na znalostní bázi Knowledge Store, budete chtít zvážit, jak je obsah strukturovaný. *Projekcí* je proces výběru uzlů ze stromu obohacení a vytvoření fyzického výrazu v úložišti znalostí. Projekce jsou vlastní tvary dokumentu (obsah a rozšíření), které mohou být výstupem buď tabulky nebo projekce objektů. Další informace o práci s projekcemi najdete v tématu [práce s projekcemi](knowledge-store-projection-overview.md).

![Možnosti mapování polí](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Možnosti mapování polí pro kanál pro obohacení")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`Element se používá pouze v dovednostech a v dovednostech. Slouží k vytváření víceúrovňových vnořených objektů. Je možné, že budete muset vytvořit nový objekt, který ho buď předáte jako vstup do odbornosti nebo projektu, do znalostní báze Knowledge Store. Uzly obohacení nemusí být platný objekt JSON ve stromu obohacení a odkazující na uzel ve stromové struktuře vrátí tento stav uzlu, když byl vytvořen, pomocí rozšíření, jako jsou vstupy nebo projekce, vyžaduje vytvoření objektu JSON ve správném formátu. `sourceContext`Umožňuje vytvořit hierarchický, anonymní objekt typu, který by vyžadoval více dovedností v případě, že jste používali pouze kontext. 

Použití `sourceContext` je zobrazeno v následujících příkladech. Podívejte se na výstup dovedností, který vygeneroval rozšíření, aby bylo možné zjistit, zda se jedná o platný objekt JSON a nikoli primitivní typ.

### <a name="slicing-projections"></a>Projekce na průřezy

Při definování skupiny projekce tabulky lze jeden uzel ve stromu rozšíření rozřezat na více souvisejících tabulek. Pokud přidáte tabulku se zdrojovou cestou, která je podřízenou položkou existující projekce tabulky, výsledný podřízený uzel nebude podřízenosti existující projekce tabulky, ale bude promítnuta do nové tabulky související s tabulkou. Tato metoda dělení na řezy umožňuje definovat jeden uzel v Shaper dovednosti, která může být zdrojem pro všechny vaše projekce tabulky. 

### <a name="shaping-projections"></a>Tvarování projekce

Existují dva způsoby, jak definovat projekci:

+ Použijte text Shaper dovednost k vytvoření nového uzlu, který je kořenovým uzlem pro všechna obohacená rozšíření. Pak v projekcích byste měli odkazovat jenom na výstup Shaper dovednosti.

+ Použijte vložený tvar projekce v rámci samotné definice projekce.

Přístup Shaper je podrobnější než při vložené tvarování, ale zajišťuje, že všechny mutace stromu rozšíření jsou obsaženy v rámci dovedností a že výstupem je objekt, který lze znovu použít. Naproti tomu vložené tvarování umožňuje vytvořit tvar, který potřebujete, ale je anonymní objekt a je k dispozici pouze pro projekci, pro kterou je definována. Přístupy lze použít společně nebo samostatně. Dovednosti vytvořená v pracovním postupu na portálu obsahuje obojí. Používá shaperou dovednost pro projektové projekce, ale také používá včleněné tvarování k tomu, aby se v tabulce klíčových frází.

Chcete-li příklad zvětšit, můžete odebrat vložené tvarování a použít dovednost Shaper k vytvoření nového uzlu pro klíčové fráze. Chcete-li vytvořit tvar vytvořený do tří tabulek, konkrétně, `hotelReviewsDocument` , `hotelReviewsPages` a `hotelReviewsKeyPhrases` , jsou tyto dvě možnosti popsány v následujících částech.

#### <a name="shaper-skill-and-projection"></a>Shaper dovednosti a projekce

> [!Note]
> Některé sloupce z tabulky dokumentů byly z tohoto příkladu odebrány pro zkrácení.
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

Pomocí `tableprojection` uzlu, který je definován v `outputs` předchozí části, teď můžeme použít funkci dělení k projektové části `tableprojection` uzlu do různých tabulek:

> [!Note]
> Toto je pouze fragment projekce v rámci konfigurace znalostní databáze.
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

#### <a name="inline-shaping-projections"></a>Vložené tvarování projekce

Přístup k vloženým tvarům nevyžaduje shaperou dovednost, protože všechny tvary potřebné pro projekce jsou vytvořeny v době, kdy jsou potřeba. Chcete-li promítnout stejná data jako předchozí příklad, možnost vložené projekce by vypadala takto:

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
  
Jedním z přístupů z obou metod je způsob, jakým jsou hodnoty z `"Keyphrases"` aplikace probíhají pomocí `"sourceContext"` . `"Keyphrases"`Uzel, který obsahuje kolekci řetězců, je podřízeným prvkem textu stránky. Vzhledem k tomu, že projekce vyžadují objekt JSON a stránka je primitivní (řetězec), `"sourceContext"` je použita k zabalení klíčové fráze do objektu s pojmenovanou vlastností. Tento postup umožňuje, aby se dokonce nezávisle promohl projektovat bez primitiv.

## <a name="next-steps"></a>Další kroky

V dalším kroku vytvoříte první dovednosti s dovednostmi rozpoznávání.

> [!div class="nextstepaction"]
> [Vytvořte svoje první dovednosti](cognitive-search-defining-skillset.md).
