---
title: Práce s dovednosti – Azure Search
description: Dovednosti jsou místo, kde vytvoříte kanál pro obohacení AI při rozpoznávání rozpoznávání, pochopíte několik konceptů a to, jak dovednosti práce umožňuje sestavovat jednoduché nebo komplexní dovednosti
manager: eladz
author: vkurpad
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: vikurpad
ms.openlocfilehash: f75e6dece376076d4aa5e33497aff7e4f9f56857
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265697"
---
# <a name="working-with-skillsets"></a>Práce s dovednosti
Tento článek je určen pro vývojáře, kteří potřebují hlubší porozumění způsobu, jakým funguje kanál pro obohacení a předpokládá, že máte koncepční porozumění procesu vyhledávání v rozpoznávání. Pokud začínáte vyhledáváním v rozpoznávání, začněte na:
+ [Co je "rozpoznávání hledání" v Azure Search?](cognitive-search-concept-intro.md)
+ [Co je znalostní úložiště v Azure Search?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Zadejte dovednosti
Dovednosti je opakovaně použitelný prostředek v Azure Search, který určuje kolekci dovedností vnímání používaných k analýze, transformaci a obohacení textu nebo obrázku v průběhu indexování. Vytvořením dovednosti můžete připojit obohacení textu a obrázků ve fázi příjmu dat, extrakci a vytváření nových informací a struktur z nezpracovaného obsahu.

Dovednosti má tři vlastnosti:

+   ```skills```, neuspořádaná kolekce dovedností, pro kterou platforma Určuje sekvenci provádění na základě vstupů vyžadovaných pro každou dovednost
+   ```cognitiveServices```, klíč služeb rozpoznávání požadovaných k fakturaci vyvolalo vnímání odbornosti.
+   ```knowledgeStore```, účet úložiště, ve kterém se budou obohacené dokumenty promítnout



Dovednosti jsou vytvořeny ve formátu JSON. Pomocí [jazyka Expression](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)můžete vytvářet komplexní dovednosti s využitím smyček a [větvení](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-conditional) . Jazyk výrazů používá zápis cesty k [ukazateli JSON](https://tools.ietf.org/html/rfc6901) s několika úpravami k identifikaci uzlů ve stromu obohacení. Ve stromové struktuře ```"*"``` projdenižšíúroveňafungujejakooperátorfor-eachvkontextu.```"/"``` Tyto koncepty jsou nejlépe popsané v příkladu. K ilustraci některých konceptů a funkcí si projdeme dovednosti [Sample prohlídek](knowledge-store-connect-powerbi.md) . Pokud se chcete podívat na dovednosti, když jste následovali pracovní postup importu dat, budete muset použít klienta REST API k [získání dovednosti](https://docs.microsoft.com/en-us/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Strom obohacení

Abychom představte, jak dovednosti postupně rozšiřuje váš dokument, pojďme začít s tím, jak dokument vypadá před jakýmkoli obohacením. Výstup pro trhliny dokumentů závisí na zdroji dat a na zvoleném režimu analýzy. To je také stav dokumentu, z něhož [mapování polí](search-indexer-field-mappings.md) umožňuje zdrojový obsah při přidávání dat do indexu vyhledávání.
![Znalostní úložiště v diagramu kanálu](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Znalostní úložiště v diagramu kanálu")

Jakmile je dokument v kanálu obohacení, je reprezentován jako strom obsahu a přidružených rozšíření. Tento strom se vytvoří jako výstup odhalujícího dokumentu. Stromová struktura obohacení umožňuje kanálu obohacení připojit metadata k ještě primitivním datovým typům, nejedná se o platný objekt JSON, ale lze jej převést do platného formátu JSON. Následující tabulka ukazuje stav dokumentu, který se zadává do kanálu rozšíření:

|Režim Source\Parsing dat|Výchozí|JSON, řádky JSON & CSV|
|---|---|---|
|Úložiště objektů blob|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|neuvedeno |
|Databáze Cosmos|/document/{key1}<br>/document/{key2}<br>…|neuvedeno|

 Při provádění dovedností přidávají nové uzly do stromu obohacení. Tyto nové uzly pak mohou být použity jako vstupy pro dovednosti s využitím pro příjem dat, projekci do obchodu Knowledge Store nebo mapování na pole indexu. Rozšíření nejsou proměnlivá: po vytvoření se uzly nedají upravovat. Vzhledem k tomu, že vaše dovednostiy jsou složitější, takže se strom pro rozšíření, ale ne všechny uzly ve stromu pro rozšíření, nemusí dělat na index nebo na obchod znalostní báze. Můžete selektivně zachovat jenom podmnožinu obohacení na index nebo úložiště znalostní báze.

Můžete selektivně zachovat jenom podmnožinu obohacení na index nebo úložiště znalostní báze.
Ve zbývající části tohoto dokumentu budeme předpokládat, že pracujeme s [ukázkami hotelů](https://docs.microsoft.com/en-us/azure/search/knowledge-store-connect-powerbi), ale stejné koncepty platí i pro rozšiřování dokumentů ze všech ostatních zdrojů dat.

### <a name="context"></a>Kontext
Každá dovednost vyžaduje kontext. Kontext určuje:
+   Počet, kolikrát se dovednost spustí na základě vybraných uzlů. V případě hodnot kontextu typu kolekce je přidání ```/*``` na konci výsledkem uplatnění dovednosti u každé instance v kolekci. 
+   Ve stromu obohacení se přidávají výstupy dovedností. Výstupy jsou vždy přidány do stromu jako podřízené objekty uzlu kontextu. 
+   Tvar vstupů. V případě kolekcí s více úrovněmi bude nastavení kontextu nadřazené kolekce ovlivňovat tvar vstupu dovednosti. Například pokud máte strom obohacení se seznamem zemí, každý obohacený seznamem stavů, které obsahují seznam PSČ.

|Kontext|Vstup|Tvar vstupu|Vyvolání dovedností|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Seznam všech PSČ v zemi |Jednou za zemi |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Seznam PSČ ve stavu | Jednou za kombinaci země a státu|

### <a name="sourcecontext"></a>SourceContext

Používá se pouze v [Shaper dovednostech](cognitive-search-skill-shaper.md) a projekcích. [](knowledge-store-projection-overview.md) `sourceContext` Slouží k vytváření víceúrovňových vnořených objektů. `sourceContext` Umožňuje vytvořit hierarchický, anonymní objekt typu, který by vyžadoval více dovedností v případě, že jste používali pouze kontext. Použití `sourceContext` je zobrazeno v následující části.

### <a name="projections"></a>Projekce

Projekcí je proces výběru uzlů z stromu obohacení pro uložení ve znalostní bázi Store. Projekce jsou vlastní tvary dokumentu (obsah a rozšíření), které mohou být výstupem buď tabulky nebo projekce objektů. Další informace o práci s projekcemi najdete v tématu [práce s projekcemi](knowledge-store-projection-overview.md).

![Možnosti mapování polí](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Možnosti mapování polí pro kanál pro obohacení")

Diagram výše popisuje selektor, se kterým pracujete, na základě toho, kde se nacházíte v kanálu pro obohacení.

## <a name="generate-enriched-data"></a>Generovat obohacená data 

Teď si projdeme dovednosti a Projděte si kurz, kde můžete postupovat podle [kurzu](knowledge-store-connect-powerbi.md) a vytvořit dovednosti nebo [Zobrazit](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) dovednosti. Budeme se pohlížet na:

* způsob vývoje stromu rozšíření s prováděním jednotlivých dovedností 
* jak kontext a vstupy pracují k určení, kolikrát se dovednost spustí 
* Jaký tvar vstupu je založen na kontextu. 

Vzhledem k tomu, že pro indexer používáme režim analýzy textu s oddělovači, představuje dokument v rámci procesu rozšíření jeden řádek v souboru CSV.

### <a name="skill-1-split-skill"></a>#1 dovedností: Rozdělit dovednost 

![strom obohacení po vytrhlině dokumentu](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Strom obohacení po prolomení dokumentů a před provedením dovedností")

V souvislosti ```"/document/reviews_text"```s dovednostmi dovedností se tato dovednost spustí jednou `reviews_text`pro. Výstup dovedností je seznam, ve kterém `reviews_text` je rozdělen do segmentů 5000 znaků. Výstup z rozdělené dovednosti je pojmenován `pages` a přidán do stromu obohacení. `targetName` Funkce umožňuje přejmenovat výstup dovedností před přidáním do stromu obohacení.

Strom rozkládání teď má nový uzel umístěný pod rámec dovednosti. Tento uzel je k dispozici pro jakékoli mapování dovedností, projekce nebo výstupních polí.


Kořenový uzel pro všechna rozšíření je `"/document"`. Při práci s indexery `"/document"` objektů BLOB bude uzel mít podřízené `"/document/content"` uzly a `"/document/normalized_images"`. Při práci s daty ve formátu CSV, jak je v tomto příkladu, se názvy sloupců mapují na uzly níže `"/document"`. Aby bylo možné získat přístup k jakémukoli obohacení rozšíření přidaným do uzlu dovedností, je nutná úplná cesta pro rozšíření. Například pokud chcete použít text z ```pages``` uzlu jako vstup pro jinou dovednost, budete ho muset zadat jako. ```"/document/reviews_text/pages/*"```
 
 ![strom obohacení po #1 dovednosti](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Strom obohacení po provedení #1 dovednosti")

### <a name="skill-2-language-detection"></a>Zjišťování jazyka pro dovednost #2
 I když je dovednost detekce jazyka třetí (dovednostní #3) definovaná v dovednosti, jedná se o další dovednost, kterou je potřeba provést. Vzhledem k tomu, že není zablokované vyžadováním jakýchkoli vstupů, spustí se paralelně s předchozí dovedností. Stejně jako rozdělení dovedností, které předcházejí, je pro každý dokument také vyvolána dovednost detekce jazyka. Strom rozrozšíření teď má nový uzel pro jazyk.
 ![strom obohacení po #2 dovednosti](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Strom obohacení po provedení #2 dovednosti")
 
 ### <a name="skill-3-key-phrases-skill"></a>#3 dovedností: Dovednost klíčových frází 

Vzhledem k, že ```/document/reviews_text/pages/*``` se kontext dovedností klíčových frází vyvolá jednou pro každou položku `pages` v kolekci. Výstup z dovednosti bude uzel pod přidruženým prvkem stránky. 

 Nyní byste měli být schopni se podívat na zbytek dovedností v dovednosti a vizualizovat, jak bude strom rozšíření nadále rostoucí s prováděním jednotlivých dovedností. Některé dovednosti, jako je například dovednost sloučení a kvalifikace Shaper, také vytvářejí nové uzly, ale používají data z existujících uzlů a nevytvářejí čisté nové rozšíření.

![strom obohacení po všech dovednostech](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Strom obohacení po všech dovednostech")

Barvy konektorů ve stromu výše označují, že rozšíření byly vytvořeny různými dovednostmi a uzly budou muset být řešeny individuálně a nebudou součástí objektu vráceného při výběru nadřazeného uzlu.

## <a name="save-enrichments-in-a-knowledge-store"></a>Uložení obohacení ve znalostní bázi Knowledge Store 

Dovednosti také definují znalostní bázi, ve kterém se vaše obohacené dokumenty dají promítnout jako tabulky nebo objekty. K uložení obohacených dat ve znalostní bázi můžete definovat sadu projekce obohaceného dokumentu. Další informace o službě Knowledge Store najdete [v tématu Co je znalostní úložiště v Azure Search?](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Projekce na průřezy

Při definování skupiny projekce tabulky lze jeden uzel ve stromu rozšíření rozřezat na více souvisejících tabulek. Pokud přidáte tabulku se zdrojovou cestou, která je podřízenou položkou existující projekce tabulky, výsledný podřízený uzel nebude podřízenosti existující projekce tabulky, ale bude promítnuta do nové tabulky související s tabulkou. Tato metoda dělení na řezy umožňuje definovat jeden uzel v Shaper dovednosti, která může být zdrojem pro všechny vaše projekce tabulky. 

### <a name="shaping-projections"></a>Tvarování projekce

Existují dva způsoby, jak definovat projekci. Shaper dovednosti můžete použít k vytvoření nového uzlu, který je kořenovým uzlem pro všechna rozšíření, která procházíte. Pak v projekcích byste měli odkazovat jenom na výstup Shaper dovednosti. V rámci samotné definice projekce můžete také vložit obrazec projekce.

Přístup Shaper je podrobnější než při vložené tvarování, ale zajišťuje, že všechny mutace stromu rozšíření jsou obsaženy v rámci dovedností a že výstupem je objekt, který lze znovu použít. Vložené tvarování vám umožňuje vytvořit tvar, který potřebujete, ale je anonymní objekt a je k dispozici pouze pro projekci, pro kterou je definována. Přístupy lze použít společně nebo samostatně. Dovednosti vytvořená v pracovním postupu na portálu obsahuje obojí. Používá shaperou dovednost pro projektové projekce, ale také používá včleněné tvarování k tomu, aby se v tabulce klíčových frází.

Chcete-li příklad zvětšit, můžete odebrat vložené tvarování a použít dovednost Shaper k vytvoření nového uzlu pro klíčové fráze. Chcete-li vytvořit tvar vytvořený do tří tabulek, konkrétně `hotelReviewsDocument` `hotelReviewsPages`,, a `hotelReviewsKeyPhrases`, jsou tyto dvě možnosti popsány v následujících částech.


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

Pomocí uzlu, který je definován `outputs` v předchozí části, teď můžeme použít funkci dělení `tableprojection` k projektové části uzlu do různých tabulek: `tableprojection`

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
  
Jedním z přístupů z obou metod je způsob, jakým jsou hodnoty z `"Keyphrases"` aplikace probíhají `"sourceContext"`pomocí. `"Keyphrases"` Uzel, který obsahuje kolekci řetězců, je podřízeným prvkem textu stránky. Vzhledem k tomu, že projekce vyžadují objekt JSON a stránka je primitivní (řetězec), `"sourceContext"` je použita k zabalení klíčové fráze do objektu s pojmenovanou vlastností. Tento postup umožňuje, aby se dokonce nezávisle promohl projektovat bez primitiv.

## <a name="next-steps"></a>Další kroky

V dalším kroku vytvoříte první dovednosti s dovednostmi rozpoznávání.

> [!div class="nextstepaction"]
> [Vytvořte svoje první dovednosti](cognitive-search-defining-skillset.md).
