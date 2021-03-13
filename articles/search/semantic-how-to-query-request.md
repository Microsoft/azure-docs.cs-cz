---
title: Vytvořit sémantický dotaz
titleSuffix: Azure Cognitive Search
description: Nastavte sémantický typ dotazu pro připojení modelů hloubkového učení k zpracování dotazů, odvození záměru a kontextu v rámci pořadí hledání a relevance.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9ff98a2613143474afd6041ccf52d4eb509d646b
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418874"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Vytvoření sémantického dotazu v Kognitivní hledání

> [!IMPORTANT]
> Sémantický typ dotazu je ve verzi Public Preview, který je k dispozici ve verzi Preview REST API a Azure Portal. Funkce ve verzi Preview se nabízejí tak, jak jsou, v části s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [dostupnost a ceny](semantic-search-overview.md#availability-and-pricing).

V tomto článku se dozvíte, jak formulovat požadavek hledání, který používá sémantické hodnocení. Požadavek vrátí sémantické titulky a volitelně [sémantické odpovědi](semantic-answers.md)a zvýrazní nejdůležitější výrazy a fráze.

Titulky a odpovědi jsou extrahovány pomocí textu v dokumentu hledání. Sémantický podsystém určuje, který obsah má vlastnosti titulku nebo odpovědi, ale nevytváří nové věty ani fráze. Z tohoto důvodu může obsah, který obsahuje vysvětlení nebo definice, fungovat nejlépe pro sémantické vyhledávání.

## <a name="prerequisites"></a>Požadavky

+ Vyhledávací služba na úrovni Standard (S1, S2, S3), která se nachází v jedné z těchto oblastí: Střed USA – sever, Západní USA, Západní USA 2, Východní USA 2, Severní Evropa, Západní Evropa. Pokud máte v jedné z těchto oblastí existující službu S1 nebo větší, můžete požádat o přístup bez nutnosti vytvářet novou službu.

+ Přístup ke službě sémantického vyhledávání ve verzi Preview: [registrace](https://aka.ms/SemanticSearchPreviewSignup)

+ Existující index vyhledávání, který obsahuje anglický obsah

+ Vyhledávací klient pro odesílání dotazů

  Vyhledávací klient musí v žádosti o dotaz podporovat rozhraní REST API pro náhled. Můžete použít [post](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)nebo kód, který jste změnili, aby se v rozhraní API verze Preview zavolala volání REST. Můžete také použít [Průzkumníka vyhledávání](search-explorer.md) v Azure Portal k odeslání sémantického dotazu.

+ [Požadavek na dotaz](/rest/api/searchservice/preview-api/search-documents) musí obsahovat sémantickou možnost a další parametry popsané v tomto článku.

## <a name="whats-a-semantic-query"></a>Co je sémantický dotaz?

V Kognitivní hledání dotaz je parametrizovaná žádost, která určuje zpracování dotazů a tvar odpovědi. *Sémantický dotaz* přidává parametry, které vyvolávají model pro sémantické přeřazení, který může vyhodnotit kontext a význam odpovídajících výsledků, zvýšit relevantnější shody na začátek a vracet sémantické odpovědi a popisky.

Následující žádost je zástupce minimálního sémantického dotazu (bez odpovědí).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Stejně jako u všech dotazů v Kognitivní hledání se požadavek zaměřuje na kolekci dokumentů jediného indexu. Sémantický dotaz dále projde stejnou sekvencí analýzy, analýzy, kontroly a bodování jako nesémantický dotaz. 

Rozdíl leží v relevanci a bodování. Jak je definováno v této verzi Preview, sémantický dotaz je, jehož *výsledky* jsou přeřazeny pomocí sémantického jazykového modelu, a to tak, aby bylo zajištěno, že se shodám, které považují za důležité pro sémantický rozsah, místo skóre přiřazených pomocí výchozího algoritmu řazení podobnosti.

Pouze prvních 50 shod od počátečních výsledků lze sémanticky seřadit a všechny popisky zahrnutí v odpovědi. Volitelně můžete zadat **`answer`** parametr pro požadavek na extrakci potenciální odpovědi. Další informace najdete v tématu [sémantické odpovědi](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Dotaz s využitím Průzkumníka služby Hledání

[Průzkumník služby Search](search-explorer.md) byl aktualizován tak, aby obsahoval možnosti pro sémantické dotazy. Tyto možnosti se zobrazí na portálu po získání přístupu k verzi Preview. Možnosti dotazu mohou umožňovat sémantické dotazy, searchFields a opravy pravopisu.

Do řetězce dotazu můžete také vložit požadované parametry dotazu.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Možnosti dotazů v Průzkumníkovi vyhledávání" border="true":::

## <a name="query-using-rest"></a>Dotaz pomocí REST

Pomocí [vyhledávacích dokumentů (REST Preview)](/rest/api/searchservice/preview-api/search-documents) formulujte požadavek programově.

Odpověď obsahuje popisky a zvýrazňování automaticky. Pokud chcete, aby odpověď zahrnovala opravy pravopisu nebo odpovědi, přidejte **`speller`** **`answers`** do žádosti volitelný parametr nebo.

V následujícím příkladu se k vytvoření žádosti sémantického dotazu se sémantickými odpověďmi a popisky používá pohostines-Sample-index:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Následující tabulka shrnuje parametry dotazu používané v sémantickém dotazu, abyste je mohli zobrazit komplexní. Seznam všech parametrů najdete v tématu [hledání dokumentů (REST Preview)](/rest/api/searchservice/preview-api/search-documents) .

| Parametr | Typ | Description |
|-----------|-------|-------------|
| queryType | Řetězec | Mezi platné hodnoty patří jednoduchá, plná a sémantická. Pro sémantické dotazy je požadována hodnota sémantika. |
| queryLanguage | Řetězec | Vyžaduje se pro sémantické dotazy. V současné době je implementována pouze "en-US". |
| searchFields | Řetězec | Seznam prohledávatelných polí oddělených čárkami. Volitelné, ale doporučené. Určuje pole, přes která probíhá sémantické hodnocení. </br></br>Na rozdíl od jednoduchých a úplných typů dotazů je pořadí, ve kterém jsou pole uvedena, určena přednostně. Další pokyny k používání najdete v části [Krok 2: set searchFields](#searchfields). |
| kontrolu pravopisu | Řetězec | Volitelný parametr, který není specifický pro sémantické dotazy, který opravuje nesprávně napsané výrazy předtím, než dosáhnou vyhledávacího modulu. Další informace najdete v tématu [Přidání opravy pravopisu do dotazů](speller-how-to-add.md). |
| zodpovídá |Řetězec | Volitelné parametry, které určují, zda jsou ve výsledku zahrnuty sémantické odpovědi. V současné době je implementována pouze "extrakce". Odpovědi lze nakonfigurovat tak, aby vracely maximálně pět. Výchozí hodnota je jedna. V tomto příkladu se zobrazuje počet tří odpovědí: "extrahovatelné \| count3". Další informace najdete v tématu o [vrácení sémantických odpovědí](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formulování žádosti

Tato část popisuje parametry dotazů, které jsou nezbytné pro sémantické vyhledávání.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Krok 1: nastavení queryType a queryLanguage

Do Rest přidejte následující parametry. Oba parametry jsou povinné.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage musí být konzistentní s jakýmkoli [analyzátorem jazyka](index-add-language-analyzers.md) přiřazeným k definicím polí ve schématu indexu. Pokud queryLanguage je "en-US", musí být všechny analyzátory jazyka také anglické variantou ("en. Microsoft" nebo "en. Lucene"). Žádné nezávislá analyzátory jazyka, jako je klíčové slovo nebo jednoduchá, nemají žádné konflikty s hodnotami queryLanguage.

Pokud v žádosti o dotaz používáte také [korekci pravopisu](speller-how-to-add.md), nastavení queryLanguage se použije stejně jako pravopisné, odpovědi a titulky. Pro jednotlivé části neexistuje žádné přepsání. 

Obsah ve vyhledávacím indexu může být tvořen v několika jazycích, takže vstup dotazu je pravděpodobně v jednom. Vyhledávací web nekontroluje kompatibilitu queryLanguage, Language Analyzer a jazyka, ve kterém se obsah skládá, a nezapomeňte jim zajistit jejich obor, aby nedocházelo k nesprávným výsledkům.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Krok 2: nastavení searchFields

Tento parametr je nepovinný v tom, že není k dispozici žádná chyba, pokud ho necháte, ale pro titulky a odpovědi se důrazně doporučuje zobrazit uspořádaný seznam polí.

Parametr searchFields slouží k identifikaci pasáží pro vyhodnocení "sémantické podobnosti" dotazem. V rámci verze Preview nedoporučujeme ponechání searchFields prázdné, protože model vyžaduje doporučení, která pole jsou pro zpracování nejdůležitější.

Pořadí searchFields je kritické. Pokud už používáte searchFields v existujících jednoduchých nebo úplných dotazech na Lucene, nezapomeňte tento parametr znovu navštívit a při přechodu na sémantický typ dotazu zkontrolovat pořadí polí.

Postupujte podle těchto pokynů, abyste zajistili optimální výsledky při zadání dvou nebo více searchFields:

+ V kolekcích zahrňte pouze pole řetězců a pole řetězců nejvyšší úrovně. Pokud se rozhodnete zahrnout neřetězcová pole nebo pole nižší úrovně v kolekci, nebude k dispozici žádná chyba, ale tato pole se nepoužijí v sémantickém hodnocení.

+ První pole by mělo být vždy stručné (například název nebo název), a to v ideálním 25 slovem.

+ Pokud má index pole adresy URL, které je text (lidské čitelnost, jako je například `www.domain.com/name-of-the-document-and-other-details` , a ne počítač zaměřený na například `www.domain.com/?id=23463&param=eis` ), umístěte ho do tohoto seznamu (nebo jako první, pokud neexistuje pole stručného názvu).

+ Použijte tato pole podle popisných polí, kde můžete najít odpověď na sémantické dotazy, jako je například hlavní obsah dokumentu.

Pokud je zadáno pouze jedno pole, použijte popisné pole, kde lze nalézt odpověď na sémantické dotazy, jako je například hlavní obsah dokumentu. Vyberte pole, které poskytuje dostatečný obsah. Aby bylo zajištěno včasné zpracování, pouze o tokenech 8 000 o společném obsahu searchFields podstupuje sémantické vyhodnocení a hodnocení.

#### <a name="step-3-remove-orderby-clauses"></a>Krok 3: odebrání klauzulí orderBy

Odeberte jakékoli klauzule orderBy, pokud existují v existující žádosti. Sémantické skóre se používá k seřazení výsledků a pokud zahrnete explicitní logiku řazení, vrátí se chyba HTTP 400.

#### <a name="step-4-add-answers"></a>Krok 4: Přidání odpovědí

Volitelně můžete přidat odpovědi, pokud chcete zahrnout další zpracování, které poskytuje odpověď. Odpovědi (a titulky) jsou extrahovány z pasáží nalezených v polích uvedených v searchFields. Nezapomeňte do searchFields zahrnout pole s bohatým obsahem, abyste získali nejlepší odpovědi v reakci. Další informace najdete v tématu [jak vracet sémantické odpovědi](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Krok 5: Přidání dalších parametrů

Nastavte všechny další parametry, které chcete v žádosti. Parametry, jako je třeba [Kontrola pravopisu](speller-how-to-add.md), [Výběr](search-query-odata-select.md)a počet, zlepšují kvalitu žádosti a čitelnosti odpovědi.

Volitelně můžete přizpůsobit styl zvýraznění aplikovaný na titulky. Popisky použijí formátování zvýraznění u klíčových pasáží v dokumentu, které shrnují odpověď. Výchozí formát je `<em>`. Pokud chcete určit typ formátování (například žluté pozadí), můžete nastavit highlightPreTag a highlightPostTag.

## <a name="evaluate-the-response"></a>Vyhodnocení odpovědi

Stejně jako u všech dotazů se odpověď skládá ze všech polí označených jako načístelné nebo pouze těch polí uvedených v parametru SELECT. Zahrnuje původní skóre významnosti a může také zahrnovat počet nebo dávkování výsledků v závislosti na tom, jak jste vyžádali požadavek.

V sémantickém dotazu má odpověď další prvky: nové sémanticky seřazené skóre relevance, popisky v prostém textu a s nejzajímavější a volitelně také odpověď.

V klientské aplikaci můžete vytvořit strukturu vyhledávací stránky tak, aby obsahovala popisek jako popis shody, nikoli celý obsah konkrétního pole. To je užitečné, když jsou jednotlivá pole moc zhuštěná na stránce s výsledky hledání.

Odpověď pro příklad dotazu výše vrací následující shodu jako horního výběru. Titulky se vrátí automaticky, s prostým textem a zvýrazněnými verzemi. Odpovědi jsou vynechány v příkladu, protože u tohoto konkrétního dotazu a corpus nelze určit jeden.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Další kroky

Pomocí počáteční sady výsledků odvoláte, aby se sémantické hodnocení a odpovědi vystavily. Všechny logiky, které zvyšují kvalitu počátečních výsledků, se přenesou na sémantické vyhledávání. V dalším kroku si Projděte funkce, které přispívají k počátečním výsledkům, včetně analyzátorů, které mají vliv na tokeny řetězců, vyhodnocovací profily, které mohou ladit výsledky a výchozí algoritmus pro relevanci.

+ [Analyzátory pro zpracování textu](search-analyzers.md)
+ [Podobnost a bodování v Kognitivní hledání](index-similarity-and-scoring.md)
+ [Přidání profilů vyhodnocování](index-add-scoring-profiles.md)
+ [Přehled sémantického hledání](semantic-search-overview.md)
+ [Přidat kontrolu pravopisu pro dotaz na výrazy](speller-how-to-add.md)
