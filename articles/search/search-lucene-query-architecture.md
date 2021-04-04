---
title: Fulltextový dotaz a Architektura modulu indexování (Lucene)
titleSuffix: Azure Cognitive Search
description: Podívá se na zpracování dotazů Lucene a koncepce pro fulltextové vyhledávání, které se týkají Azure Kognitivní hledání.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 50a1656fcb92d9777d4a9476ef2a4c1fd2f2efc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002744"
---
# <a name="full-text-search-in-azure-cognitive-search"></a>Fulltextové vyhledávání v Azure Kognitivní hledání

Tento článek je určen pro vývojáře, kteří potřebují hlubší přehled o tom, jak funkce Lucene fulltextového vyhledávání funguje v Azure Kognitivní hledání. V případě textových dotazů Azure Cognitive Search ve většině scénářů bezproblémově doručí očekávané výsledky, ale občas se může zobrazit výsledek, který se zdá být nějakým způsobem „mimo“. V takových situacích vám čtyři fáze provádění dotazů Lucene (parsování dotazu, lexikální analýza, porovnávání dokumentů, hodnocení) na pozadí můžou pomoct s identifikací konkrétních změn parametrů dotazu nebo konfigurace indexu, které zajistí požadovaný výsledek. 

> [!Note] 
> Azure Kognitivní hledání používá Lucene pro fulltextové vyhledávání, ale integrace Lucene není vyčerpávající. Selektivně zpřístupníme a rozšíříme funkce Lucene, aby byly povolené scénáře důležité pro Azure Kognitivní hledání. 

## <a name="architecture-overview-and-diagram"></a>Přehled architektury a diagram

Zpracování fulltextového vyhledávacího dotazu začíná analýzou textu dotazu za účelem extrakce hledaných výrazů. Vyhledávací modul používá index k načtení dokumentů s vyhovujícími podmínkami. Jednotlivé výrazy dotazu jsou někdy rozdělené a znovu zavedené do nových formulářů, aby převzaly širší síť nad tím, co by mohlo být považováno za možnou shodu. Sada výsledků pak bude seřazena podle skóre relevance přiřazené ke každému individuálnímu dokumentu, který odpovídá. Položky v horní části seřazeného seznamu se vrátí volající aplikaci.

Restateed, provádění dotazů má čtyři fáze: 

1. Analýza dotazů 
2. Lexikální analýza 
3. Načtení dokumentu 
4. Vyhodnocování 

Následující diagram znázorňuje komponenty, které se používají ke zpracování žádosti o vyhledávání. 

 ![Diagram architektury dotazů Lucene v Azure Kognitivní hledání][1]


| Klíčové komponenty | Funkční popis | 
|----------------|------------------------|
|**Analyzátory dotazů** | Jednotlivé výrazy dotazů z operátorů dotazu a vytvoření struktury dotazu (stromu dotazu), které se mají odeslat do vyhledávacího modulu. |
|**Analyzátory** | Provede lexikální analýzu pro výrazy dotazu. Tento proces může zahrnovat transformaci, odebrání nebo rozšiřování podmínek dotazu. |
|**Index** | Efektivní datová struktura, která se používá k ukládání a organizování vyhledávaných podmínek extrahovaných z indexovaných dokumentů. |
|**Vyhledávací modul** | Načte a zavede hodnocení dokumentů na základě obsahu obráceného indexu. |

## <a name="anatomy-of-a-search-request"></a>Anatomie požadavku hledání

Požadavek hledání je kompletní specifikace toho, co by mělo být vráceno v sadě výsledků dotazu. V nejjednodušším tvaru se jedná o prázdný dotaz bez jakýchkoli kritérií žádného druhu. Realističtější příklad obsahuje parametry, několik dotazů, které mohou být vymezeny na určitá pole, případně výraz filtru a pravidla řazení.  

V následujícím příkladu se nachází požadavek hledání, který můžete odeslat do služby Azure Kognitivní hledání pomocí [REST API](/rest/api/searchservice/search-documents).  

```
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
```

Pro tento požadavek vyhledávací modul provede následující akce:

1. Odfiltruje dokumenty, kde cena je minimálně $60 a menší než $300.
2. Spustí dotaz. V tomto příkladu se vyhledávací dotaz skládá ze frází a podmínek: `"Spacious, air-condition* +\"Ocean view\""` (uživatelé obvykle nezadávají interpunkci, ale včetně v příkladu, které nám umožňují vysvětlit, jak analyzátor zpracovává). Pro tento dotaz hledá vyhledávací modul pole Popis a název zadaný v `searchFields` dokumentu pro dokumenty, které obsahují "zobrazení oceánu", a navíc pro pojem "spacious", nebo na podmínky, které začínají předponou "Air-podmínka". `searchMode`Parametr se používá ke shodě s libovolným termínem (výchozí) nebo všemi nimi, pro případy, kdy není explicitně vyžadován termín ( `+` ).
3. Vyřadí výslednou sadu hotelů podle blízkosti daného geografického umístění a potom se vrátí volající aplikaci. 

Většina tohoto článku se týká zpracování *vyhledávacího dotazu*: `"Spacious, air-condition* +\"Ocean view\""` . Filtrování a řazení jsou mimo rozsah. Další informace najdete v [referenční dokumentaci rozhraní API pro hledání](/rest/api/searchservice/search-documents).

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fáze 1: analýza dotazů 

Jak je uvedeno, řetězec dotazu je první řádek požadavku: 

```
 "search": "Spacious, air-condition* +\"Ocean view\"", 
```

Analyzátor dotazů odděluje operátory (například `*` a `+` v příkladu) z vyhledávaných podmínek a dekonstruuje vyhledávací dotaz na *poddotazy* podporovaného typu: 

+ *termínový dotaz* pro samostatné podmínky (například spacious)
+ *dotaz fráze* pro citované výrazy (například zobrazení oceánu)
+ *dotaz na předponu* pro podmínky následované operátorem předpony `*` (jako je letecký stav)

Úplný seznam podporovaných typů dotazů naleznete v tématu [syntaxe dotazů Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) .

Operátory přidružené k poddotaz určují, zda má být dotaz "musí být" nebo "by měl být" splněn, aby bylo možné dokument považovat za shodu. Například `+"Ocean view"` je "musí" z důvodu `+` operátoru. 

Analyzátor dotazů přestrukturuje poddotazy do *stromu dotazu* (interní struktura představující dotaz), která je předána do vyhledávacího modulu. V první fázi analýzy dotazů vypadá strom dotazu takto.  

 ![Logický dotaz searchmode libovolný][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Podporované analyzátory: jednoduchý a úplný Lucene 

 Azure Kognitivní hledání zpřístupňuje dva různé jazyky dotazů `simple` (výchozí) a `full` . Nastavením `queryType` parametru s vaším požadavkem hledání sdělujete analyzátor dotazů, který jazyk dotazů zvolíte, aby věděl, jak interpretovat operátory a syntaxi. [Jednoduchý dotazovací jazyk](/rest/api/searchservice/simple-query-syntax-in-azure-search) je intuitivní a robustní, často vhodný k interpretaci vstupu uživatele, protože je bez zpracování na straně klienta. Podporuje operátory dotazů, které jsou známé z webových vyhledávačů. [Celý dotazovací jazyk pro Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search), který získáte nastavením `queryType=full` , rozšiřuje výchozí jednoduchý dotazovací jazyk přidáním podpory pro více operátorů a typů dotazů, jako jsou zástupné znaky, přibližné, regulární výrazy a dotazy v oboru polí. Například regulární výraz odeslaný v jednoduché syntaxi dotazu by byl interpretován jako řetězec dotazu, nikoli výraz. Příklad žádosti v tomto článku používá úplný dotazovací jazyk Lucene.

### <a name="impact-of-searchmode-on-the-parser"></a>Dopad searchMode na analyzátor 

Dalším parametrem žádosti o vyhledávání, který má vliv na analýzu, je `searchMode` parametr. Určuje výchozí operátor pro booleovské dotazy: Any (výchozí) nebo ALL.  

Když `searchMode=any` , což je výchozí hodnota, oddělovač mezer mezi spacious a Air je nebo ( `||` ), což znamená, že text ukázkového dotazu odpovídá: 

```
Spacious,||air-condition*+"Ocean view" 
```

Explicitní operátory, jako `+` v `+"Ocean view"` , jsou v konstruktoru logického dotazu jednoznačné (podmínky se *musí* shodovat). Méně zjevně je způsob, jak interpretovat zbývající podmínky: spacious a letecký stav. Vyhledá vyhledávací modul shody v zobrazení oceánu *a* v spacious *a* v klimatizačním prostředí? Nebo by mělo najít zobrazení oceánu plus *jednu* ze zbývajících podmínek? 

Ve výchozím nastavení ( `searchMode=any` ) vyhledávací modul předpokládá širší výklad. Buď *by se mělo* shodovat každé pole, odrážet sémantiku "nebo". Úvodní stromová struktura dotazu dříve ukázala, že dvě operace "by" měly být uvedeny jako výchozí.  

Předpokládejme, že jsme teď nastavili `searchMode=all` . V tomto případě je místo interpretováno jako operace "a". Každý z zbývajících podmínek musí být uveden v dokumentu, aby mohl být kvalifikován jako shoda. Výsledný vzorový dotaz by byl interpretován takto: 

```
+Spacious,+air-condition*+"Ocean view"
```

Upravený strom dotazu pro tento dotaz by byl následující, kde odpovídající dokument je průnik všech tří poddotazů: 

 ![Logický dotaz searchmode vše][3]

> [!Note] 
> Výběr možnosti `searchMode=any` přes `searchMode=all` je rozhodnutí, které se nejlépe dorazilo na základě spuštění reprezentativních dotazů. Uživatelé, kteří budou pravděpodobně zahrnovat operátory (společné při hledání úložišť dokumentů), mohou najít výsledky intuitivnější `searchMode=all` , pokud informují booleovské konstruktory dotazů. Další informace o souhře mezi `searchMode` operátory a najdete v tématu [Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search).

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fáze 2: lexikální analýza 

Lexikální analyzátory zpracovávají *dotazy* a dotazy na *fráze* po strukturování stromu dotazů. Analyzátor přijímá textové vstupy, které mu daný analyzátor přenáší, zpracovává text a poté odesílá zpětně vytvořené podmínky, které budou začleněny do stromu dotazu. 

Nejběžnější forma lexikální analýzy je *Lingvistická analýza* , která transformuje podmínky dotazu na základě pravidel specifických pro daný jazyk: 

* Zkrácení termínu dotazu na kořenový formulář slova 
* Odebrání nepodstatných slov (stopslova, například "," nebo "a" v angličtině) 
* Rozdělení složených slov na části komponent 
* Malé písmeno velkými písmeny na velké slovo 

Všechny tyto operace mají za následek mazání rozdílů mezi textovým vstupem poskytnutým uživatelem a podmínkami uloženými v indexu. Takové operace přecházejí mimo zpracování textu a vyžadují důkladné znalosti samotného jazyka. Pro přidání této vrstvy povědomí o jazykovém používání Azure Kognitivní hledání podporuje dlouhý seznam [analyzátorů jazyků](/rest/api/searchservice/language-support) od Lucene i od Microsoftu.

> [!Note]
> V závislosti na vašem scénáři se požadavky na analýzu můžou v rozsahu od minima vymezit. Můžete ovládat složitost lexikální analýzy výběrem jednoho z předdefinovaných analyzátorů nebo vytvořením vlastního [analyzátoru](/rest/api/searchservice/Custom-analyzers-in-Azure-Search). Analyzátory jsou vymezeny na prohledávatelné pole a jsou zadány jako součást definice pole. To vám umožní měnit lexikální analýzu pro každé pole. Neurčeno, používá se *standardní* analyzátor Lucene.

V našem příkladu je před analýzou počáteční strom dotazu výraz "spacious" s velkým písmenem "S" a čárkou, kterou analyzátor dotazů interpretuje jako součást termínu dotazu (čárka není považována za operátor dotazovacího jazyka).  

Když výchozí analyzátor zpracuje termín, bude se jednat o malý pohled na oceánu a spacious a znak čárky se odstraní. Upravený strom dotazů bude vypadat takto: 

 ![Logický dotaz s analyzovanými podmínkami][4]

### <a name="testing-analyzer-behaviors"></a>Chování analyzátoru testování 

Chování analyzátoru se dá testovat pomocí [rozhraní API pro analýzu](/rest/api/searchservice/test-analyzer). Zadejte text, který chcete analyzovat, aby se zobrazily informace o tom, jaké výrazy vygeneroval analyzátor. Například chcete-li zjistit, jak by standardní analyzátor zpracovával text "letecký stav", můžete vydat následující požadavek:

```json
{
    "text": "air-condition",
    "analyzer": "standard"
}
```

Standardní analyzátor přerušuje vstupní text do následujících dvou tokenů a přiřadí je k atributům, jako jsou počáteční a koncové posuny (používané pro zvýrazňování přístupů), a také na jejich pozici (používané pro shodu frází):

```json
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
```

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Výjimky pro lexikální analýzu 

Lexikální analýza se vztahuje pouze na typy dotazů, které vyžadují úplné podmínky – buď dotaz typu Term nebo fráze. Nevztahuje se na typy dotazů s neúplnými podmínkami – dotaz předpony, dotaz na zástupné znaky, regulární dotaz – nebo na dotaz s fuzzy logikou. Tyto typy dotazů, včetně předpony dotazu s termínem `air-condition*` v našem příkladu, se přidávají přímo do stromu dotazů a vycházejí z fáze analýzy. Jediná transformace prováděná na základě podmínek dotazu těchto typů je lowercasing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fáze 3: načtení dokumentu 

Načtení dokumentu odkazuje na hledání dokumentů s vyhovujícími podmínkami v indexu. Tato fáze je nejlépe pochopením příkladu. Pojďme začít s indexem hotelů, který má následující jednoduché schéma: 

```json
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
```

Dále předpokládáme, že tento rejstřík obsahuje následující čtyři dokumenty: 

```json
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
```

**Jak jsou indexovány výrazy**

Pro pochopení načítání vám pomůže několik základních informací o indexování. Jednotka úložiště je obrácený index, jeden pro každé prohledávatelné pole. V rámci obráceného indexu je seřazený seznam všech podmínek ze všech dokumentů. Každý termín se mapuje na seznam dokumentů, ve kterých se vyskytuje, jak je znázorněno v následujícím příkladu.

Pro vytváření podmínek v obráceném indexu provádí vyhledávací modul lexikální analýzu obsahu dokumentů, podobně jako při zpracování dotazu:

1. *Textové vstupy* se předávají analyzátoru, nižším použitam, konci interpunkce a tak dále, v závislosti na konfiguraci analyzátoru. 
2. *Tokeny* jsou výstupem lexikální analýzy.
3. Do indexu jsou přidány *výrazy* .

To je běžné, ale nevyžadují se pro použití stejných analyzátorů pro operace vyhledávání a indexování, aby podmínky dotazování vypadaly jako podmínky v indexu.

> [!Note]
> Azure Kognitivní hledání umožňuje zadat různé analyzátory pro indexování a hledání prostřednictvím dalších `indexAnalyzer` parametrů a `searchAnalyzer` parametrů polí. Pokud tento parametr nezadáte, `analyzer` použije se pro indexování i vyhledávání sada s vlastností.  

**Obrácený index pro příklady dokumentů**

Návrat do našeho příkladu pro pole **title** má obrácený index vypadat takto:

| Označení | Seznam dokumentů |
|------|---------------|
| atman | 1 |
| míčů | 2 |
| pokoji | 1, 3 |
| spadající | 4  |
| playa | 3 |
| uchýlíte | 3 |
| Retreat | 4 |

V poli title se pouze *Hotel* zobrazuje ve dvou dokumentech: 1, 3.

Pro pole **Popis** je index následující:

| Označení | Seznam dokumentů |
|------|---------------|
| letové | 3
| a | 4
| míčů | 1
| podmíněné | 3
| pohodlí | 3
| délku | 1
| ostrov | 2
| Kaua ʻ i | 2
| uložené | 2
| sever | 2
| spadající | 1, 2, 3
| z | 2
| on |2
| quiet | 4
| pokoje  | 1, 3
| secluded | 4
| pozemní | 2
| spacious | 1
| prostředek | 1, 2
| na | 1
| zobrazení | 1, 2, 3
| procházení | 1
| with | 3


**Vyhovující výrazy dotazu proti indexovaným podmínkám**

S ohledem na obrácené indexy výše se vrátíme k ukázkovému dotazu a zjistíme, jak se pro náš příklad dotazu našly vyhovující dokumenty. Zajistěte, aby výsledný strom dotazů vypadal takto: 

 ![Logický dotaz s analyzovanými podmínkami][4]

Během provádění dotazu jsou jednotlivé dotazy spouštěny proti prohledávatelným polím nezávisle. 

+ TermQuery "spacious", odpovídá dokumentu 1 (Hotel atman). 

+ PrefixQuery, "Air *", se neshodují s žádnými dokumenty. 

  Jedná se o chování, které někdy vychází z vývojářů. I když termín Air v dokumentu existuje, rozdělí se na dvě podmínky podle výchozího analyzátoru. Odvolání těchto dotazů předpon, které obsahují částečné výrazy, nejsou analyzovány. Proto se podmínky s předponou "Air-Condition" vyhledá v obráceném indexu a nenaleznou.

+ PhraseQuery, "zobrazení oceánu", vyhledá výrazy "oceánu" a "View" a v původním dokumentu zkontroluje blízkost podmínek. Dokumenty 1, 2 a 3 odpovídají tomuto dotazu v poli Popis. Dokument s oznámením 4 má výraz oceán v názvu, ale není považován za shodu, protože místo jednotlivých slov hledáte frázi "zobrazení oceánu". 

> [!Note]
> Vyhledávací dotaz se spustí nezávisle na všech prohledávatelných polích v indexu služby Azure Kognitivní hledání, pokud neomezíte nastavená pole s `searchFields` parametrem, jak je znázorněno v příkladu žádosti o vyhledávání. Vrátí se dokumenty, které se shodují v jakémkoli z vybraných polí. 

U každého dotazu, který se shoduje, jsou dokumenty, které se shodují, 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fáze 4: bodování  

Každému dokumentu v sadě výsledků hledání je přiřazeno skóre relevance. Funkce skóre relevance je určena pro větší rozsah dokumentů, které nejlépe odpovídají otázce uživatele, jak je vyjádřeno vyhledávacím dotazem. Skóre se vypočítá na základě statistických vlastností podmínek, které se shodují. V jádru vzorce bodování je [TF/IDF (pojem četnost – inverzní frekvence dokumentů)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). V dotazech, které obsahují vzácné a běžné podmínky, podporuje TF/IDF výsledky, které obsahují vzácná slova. Například v hypotetickém indexu se všemi články Wikipedii od dokumentů, které odpovídají dotazu *prezidenta*, jsou dokumenty odpovídající *prezidentovi* považovány za relevantnější než *dokumenty, které odpovídají.*


### <a name="scoring-example"></a>Příklad bodování

Odvolat tři dokumenty, které odpovídají našemu ukázkovému dotazu:

```
search=Spacious, air-condition* +"Ocean view"  
```

```json
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
```

V dokumentu 1 se shodoval dotaz, protože v poli Description se vyskytuje jak pojem *spacious* , tak i požadované *zobrazení v oceánu* . Následující dva dokumenty odpovídají pouze *zobrazení* fráze pro fráze. Je možné, že se skóre relevance pro dokument 2 a 3 liší, i když odpovídají dotazu stejným způsobem. Je to proto, že vzorec bodování má víc součástí, než jenom TF/IDF. V tomto případě byl dokumentu 3 přiřazeno trochu větší skóre, protože jeho popis je kratší. Přečtěte si [vzorec praktického bodování pro Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) , který vám pomůže pochopit, jak délka pole a další faktory mohou ovlivnit skóre relevance.

Některé typy dotazů (zástupný znak, prefix, regulární výraz) vždy přispívat k celkovému skóre dokumentu jako konstantní skóre. To umožňuje, aby se shody nalezené prostřednictvím rozšíření dotazu zahrnuly do výsledků, ale aniž by to ovlivnilo hodnocení. 

V tomto příkladu je znázorněna tato záležitost. Hledání pomocí zástupných znaků, včetně hledání předpon, jsou definicí nejednoznačná, protože vstup je částečný řetězec s potenciálními shodami s velmi velkým počtem různorodých podmínek (zvažte zadání "Tour *" s shodami nalezenými u "Tour", "Tourettes" a "Tourmaline"). Vzhledem k povaze těchto výsledků neexistuje způsob, jak rozumně odvodit, které výrazy jsou užitečnější než jiné. Z tohoto důvodu budeme ignorovat pojem četnosti při vyhodnocování výsledků v dotazech typů zástupného znaku, předpony a regulárního výrazu. V rámci vícestránkového požadavku vyhledávání, který obsahuje částečné a úplné výrazy, jsou výsledky z částečného vstupu začleněny s konstantním skóre, aby se předešlo posunu k potenciálně neočekávaným shodám.

### <a name="score-tuning"></a>Ladění skóre

Existují dva způsoby, jak ve službě Azure Kognitivní hledání naladit hodnocení relevance:

1. **Profily vyhodnocování** podporují dokumenty v seřazeném seznamu výsledků na základě sady pravidel. V našem příkladu můžeme zvážit, že se dokumenty, které odpovídají poli title, považují za relevantnější než dokumenty, které odpovídají poli Popis. Pokud má náš index pro každý Hotel pole s cenami, můžeme zvýšit úroveň dokumentů s nižší cenou. Přečtěte si další informace o tom, jak [Přidat profily vyhodnocování do indexu vyhledávání.](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Zvyšování termínů** (k dispozici pouze v úplné syntaxi dotazů Lucene) poskytuje operátor zvyšování `^` , který lze použít na jakoukoli část stromu dotazu. V našem příkladu se místo hledání předpony Air v  \* jednom z nich může vyhledat buď přesný pojem *Air* , nebo předpona, ale dokumenty, které odpovídají přesnému termínu, jsou seřazené výše, a to tak, že se zvýší na termínový dotaz: * klimatizační podmínka ^ 2 | | klimatizační znak * *. Přečtěte si další informace o [zvyšování podmínek](/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Bodování v distribuovaném indexu

Všechny indexy ve službě Azure Kognitivní hledání jsou automaticky rozdělené do několika horizontálních oddílů, což nám umožňuje rychlou distribuci indexu mezi více uzly během horizontálního navýšení nebo snížení kapacity. Při vydání žádosti o vyhledávání se vystavuje nezávisle na jednotlivých horizontálních oddílů. Výsledky z jednotlivých horizontálních oddílů se pak sloučí a seřadí podle skóre (Pokud není definované žádné jiné řazení). Je důležité si být vědomi, že váhy funkce pro vyhodnocování mají za následek dotazování frekvence jejich inverzních dokumentů na všech dokumentech v rámci horizontálních oddílů, ne napříč všemi horizontálních oddílů.

To znamená, že skóre relevance *může* být pro identické dokumenty odlišné, pokud se nacházejí v různých horizontálních oddílů. Naštěstí takové rozdíly, které mají za následek zmizení, se oproti počtu dokumentů v indexu zvětšují kvůli většímu rozdělení termínů. Není možné předpokládat, na které horizontálních oddílů se daný dokument umístí. Za předpokladu, že se klíč dokumentu nemění, se ale vždy přiřadí ke stejné horizontálních oddílů.

Obecně platí, že skóre dokumentu není nejlepším atributem pro řazení dokumentů, je-li důležité stabilita pořadí. Například při zadání dvou dokumentů se stejným skóre není nijak zaručeno, který z nich se zobrazí jako první v následných spuštěních stejného dotazu. Skóre dokumentu by mělo mít obecnou představu o významu dokumentu vzhledem k ostatním dokumentům v sadě výsledků.

## <a name="conclusion"></a>Závěr

Úspěšnost internetových vyhledávačů vyvolala očekávání pro fulltextové vyhledávání přes soukromá data. Pro téměř jakýkoli druh hledání teď očekáváme, že stroj bude rozumět našímu záměru, a to i v případě, že jsou podmínky špatně napsané nebo neúplné. Můžeme dokonce očekávat shody založené na téměř ekvivalentních termínech nebo synonymech, které ve skutečnosti nikdy neurčíte.

Z technického hlediska je fulltextové vyhledávání velmi složité, což vyžaduje sofistikovanou jazykovou analýzu a systematický přístup ke zpracování způsobem, který vyplní, rozbalí a transformuje dotaz, aby poskytoval relevantní výsledek. Vzhledem k podstatným složitostem existuje mnoho faktorů, které mohou ovlivnit výsledek dotazu. Z tohoto důvodu je důležité, abyste při pokusu o práci s neočekávanými výsledky zvýšili čas na pochopení mechanismu fulltextového vyhledávání.  

Tento článek prozkoumal fulltextové vyhledávání v kontextu Azure Kognitivní hledání. Doufáme, že vám poskytneme dostatečné množství pozadí, abychom mohli rozpoznat možné příčiny a řešení pro řešení běžných problémů s dotazy. 

## <a name="next-steps"></a>Další kroky

+ Sestavte vzorový index, vyzkoušejte si různé dotazy a zkontrolujte výsledky. Pokyny najdete v tématu [sestavení a dotazování indexu na portálu](search-get-started-portal.md#query-index).

+ Vyzkoušejte si další syntaxi dotazu v části ukázka [dokumentů](/rest/api/searchservice/search-documents#bkmk_examples) nebo v tématu [Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search) v Průzkumníkovi služby Search na portálu.

+ Zkontrolujte [profily vyhodnocování](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , pokud chcete ve vyhledávací aplikaci vyladit hodnocení.

+ Naučte se používat [lexikální analyzátory specifické pro jazyk](/rest/api/searchservice/language-support).

+ [Nakonfigurujte vlastní analyzátory](/rest/api/searchservice/custom-analyzers-in-azure-search) pro minimální zpracování nebo specializované zpracování konkrétních polí.

## <a name="see-also"></a>Viz také

[Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents) 

[Jednoduchá syntaxe dotazů](/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Úplná syntaxe dotazů Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Zpracování výsledků vyhledávání](./search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png