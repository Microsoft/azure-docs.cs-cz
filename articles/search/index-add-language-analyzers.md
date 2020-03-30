---
title: Přidání analyzátorů jazyků do řetězcových polí
titleSuffix: Azure Cognitive Search
description: Vícejazyčná lexikální textová analýza pro neanglické dotazy a indexy v Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283144"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Přidání analyzátorů jazyků do řetězcových polí v indexu Azure Cognitive Search

Analyzátor *jazyka* je specifický typ [analyzátoru textu,](search-analyzers.md) který provádí lexikální analýzu pomocí jazykových pravidel cílového jazyka. Každé prohledávatelné pole má vlastnost **analyzátoru.** Pokud index obsahuje přeložené řetězce, například samostatná pole pro anglický a čínský text, můžete zadat analyzátory jazyka v každém poli pro přístup k bohatým jazykovým možnostem těchto analyzátorů.  

Azure Cognitive Search podporuje 35 analyzátorů podporovaných Lucene a 50 analyzátorů podporovaných proprietární technologií zpracování přirozeného jazyka Microsoftu používanou v Office a Bingu.

## <a name="comparing-analyzers"></a>Porovnání analyzátorů

Někteří vývojáři mohou dát přednost známější, jednoduché, open-source řešení Lucene. Analyzátory jazyka Lucene jsou rychlejší, ale analyzátory Microsoft mají pokročilé funkce, jako je lemmatizace, decompounding slov (v jazycích, jako je němčina, dánština, holandština, švédština, norština, estonština, povrch, maďarština, slovenština) a entita (adresy URL, e-maily, data, čísla). Pokud je to možné, měli byste spustit porovnání analyzátorů Microsoft a Lucene, abyste se rozhodli, který z nich je vhodnější. 

Indexování pomocí analyzátorů Microsoft je v průměru dvakrát až třikrát pomalejší než jejich ekvivalenty Lucene, v závislosti na jazyku. Výkon hledání by neměl být významně ovlivněn pro dotazy průměrné velikosti. 

### <a name="english-analyzers"></a>Anglické analyzátory

Výchozí analyzátor je Standard Lucene, který funguje dobře pro angličtinu, ale možná ne tak dobře jako luceneův anglický analyzátor nebo anglický analyzátor společnosti Microsoft. 
 
+ Luceneho anglický analyzátor rozšiřuje standardní analyzátor. Odstraňuje přivlastňovací (koncové 's) ze slov, platí vyplývající vyplývající podle Porter vyplývající algoritmus a odstraňuje anglické stop slova.  

+ Microsoft anglický analyzátor provádí lemmatization místo vyplývající. To znamená, že dokáže mnohem lépe zpracovat skloňované a nepravidelné tvary slov, což vede k relevantnějším výsledkům vyhledávání 

## <a name="configuring-analyzers"></a>Konfigurace analyzátorů

Analyzátory jazyků se používají tak, jak jsou. Pro každé pole v definici indexu můžete nastavit vlastnost **analyzátoru** na název analyzátoru, který určuje jazyk a lingvistický zásobník (Microsoft nebo Lucene). Stejný analyzátor bude použit při indexování a hledání tohoto pole. Můžete mít například samostatná pole pro popisy hotelů angličtina, francouzština a španělština, které existují vedle sebe ve stejném indexu.

> [!NOTE]
> Není možné použít jiný analyzátor jazyka v době indexování než v době dotazu pro pole. Tato funkce je vyhrazena pro [vlastní analyzátory](index-add-custom-analyzers.md). Z tohoto důvodu pokud se pokusíte nastavit **searchAnalyzer** nebo **indexAnalyzer** vlastnosti na název analyzátoru jazyka, rozhraní REST API vrátí odpověď na chybu. Místo toho je nutné použít vlastnost **analyzátoru.**

Pomocí parametru dotazu **searchFields** určete, které pole specifické pro jazyk, proti kterému chcete ve svých dotazech vyhledávat. Příklady dotazů, které zahrnují vlastnost analyzátoru, můžete zkontrolovat ve [vyhledávacích dokumentech](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Další informace o vlastnostech indexu najdete [v tématu Vytvoření indexu &#40;rozhraní REST API ](https://docs.microsoft.com/rest/api/searchservice/create-index)pro azure cognitive search&#41;. Další informace o analýze v Azure Cognitive Search najdete [v tématu Analyzátory v Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Seznam analyzátorů jazyků 
 Níže je seznam podporovaných jazyků spolu s Názvy analyzátorů Lucene a Microsoft.  

|Jazyk|Název analyzátoru Společnosti Microsoft|Lucene Analyzer Jméno|  
|--------------|-----------------------------|--------------------------|  
|Arabština|ar.microsoft|ar.lucene|  
|Arménština||hy.lucen|  
|Bangla|bn.microsoft||  
|Baskičtina||eu.lucene|  
|Bulharština|bg.microsoft|bg.lucene|  
|Katalánština|ca.microsoft|ca.lucene|  
|Čínština (zjednodušená)|zh-Hans.microsoft|zh-Hans.lucene|  
|Čínština (tradiční)|zh-Hant.microsoft|zh-Hant.lucene|  
|Chorvatština|hohr.microsoft||  
|Čeština|cs.microsoft|cs.lucene|  
|Dánština|da.microsoft|da.lucene|  
|Nizozemština|nl.microsoft|nl.lucene|  
|Angličtina|cs.microsoft|cs.lucene|  
|Estonština|et.microsoft||  
|Finština|fi.microsoft|fi.lucene|  
|Francouzština|fr.microsoft|fr.lucene|  
|Galicijština||gl.lucen|  
|Němčina|de.microsoft|de.lucene|  
|Řečtina|el.microsoft|el.lucene|  
|Gudžarátština|gu.microsoft||  
|Hebrejština|he.microsoft||  
|Hindština|hi.microsoft|hi.lucene|  
|Maďarština|hu.microsoft|hu.lucene|  
|Islandština|soubor is.microsoft||  
|Indonéština (Bahasa)|id.microsoft|id.lucene|  
|Irština||ga.lucene|  
|Italština|it.microsoft|it.lucene|  
|Japonština|ja.microsoft|ja.lucene|  
|Kannadština|kn.microsoft||  
|Korejština|ko.microsoft|ko.lucene|  
|Lotyština|Lv.microsoft|lv.lucene|  
|Litevština|Lt.microsoft||  
|Malajalámština|ml.microsoft||  
|Malajština (latinka)|ms.microsoft||  
|Maráthština|mr.microsoft||  
|Norština|nb.microsoft|č.lucene|  
|Perština||fa.lucene|  
|Polština|pl.microsoft|pl.lucene|  
|Portugalština (Brazílie)|pt-Br.microsoft|pt-Br.lucene|  
|portugalština (Portugalsko)|pt-Pt.microsoft|pt-Pt.lucene|  
|Paňdžábština|pa.microsoft||  
|Rumunština|ro.microsoft|ro.lucene|  
|Ruština|ru.microsoft|ru.lucene|  
|Srbština (cyrilice)|sr-cyrillic.microsoft||  
|Srbština (latinka)|sr-latin.microsoft||  
|Slovenština|sk.microsoft||  
|Slovinština|sl.microsoft||  
|Španělština|es.microsoft|es.lucene|  
|Švédština|sv.microsoft|sv.lucene|  
|Tamilština|ta.microsoft||  
|Telugština|te.microsoft||  
|Thajština|th.microsoft|th.lucene|  
|Turečtina|tr.microsoft|tr.lucene|  
|Ukrajinština|uk.microsoft||  
|Urdština|soubor vaše.microsoft||  
|Vietnamština|vi.microsoft||  

 Všechny analyzátory se jmény s anotovanými **Lucene** jsou poháněny [jazykovými analyzátory Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Viz také  

+ [Vytvoření indexu &#40;rozhraní REST API pro kognitivní vyhledávání Azure&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Třída AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

