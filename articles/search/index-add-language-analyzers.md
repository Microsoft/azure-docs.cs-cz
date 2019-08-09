---
title: Přidat analyzátory jazyka – Azure Search
description: Vícejazyčná analýza textu pro jiné než anglické dotazy a indexy v Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: 88d229d33f549755479d7e1c7cf012d0391bccbb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881512"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Přidat analyzátory jazyka do indexu Azure Search

*Analyzátor jazyka* je konkrétní typ [analyzátoru textu](search-analyzers.md) , který provádí lexikální analýzu pomocí jazykových pravidel cílového jazyka. Každé prohledávatelné pole má vlastnost **Analyzer** . Pokud váš index obsahuje přeložené řetězce, jako jsou například samostatná pole pro angličtinu a čínský text, mohli byste zadat jazykové analyzátory pro každé pole, abyste měli přístup k bohatě lingvistických funkcím těchto analyzátorů.  

Azure Search podporuje analyzátory 35 na základě Lucene a 50 analyzátory zajištěné technologií Microsoft pro zpracování přirozeného jazyka, která se používá v Office a Bingu.

## <a name="comparing-analyzers"></a>Porovnávání analyzátorů

Někteří vývojáři můžou preferovat známé, jednoduché a open source řešení Lucene. Analyzátory jazyka Lucene jsou rychlejší, ale analyzátory Microsoftu mají pokročilé možnosti, jako je lemmatizátor nebo předzpracování, wordová odkódování (v jazycích, jako je němčina, dánština, holandština, švédština, norština, finština, práce, maďarština, slovenština) a entita. rozpoznávání (adresy URL, e-maily, data, čísla). Pokud je to možné, měli byste při rozhodování o tom, který z nich nejlépe vyhovuje, spustit porovnání analyzátorů Microsoftu a Lucene. 

Indexování pomocí analyzátorů Microsoft Analyzer se v závislosti na jazyku dokončí na průměrně dvakrát až třikrát pomaleji než jejich ekvivalenty Lucene. Pro dotazy na průměrnou velikost by nemělo být významně ovlivněný výkon hledání. 

### <a name="english-analyzers"></a>Analyzátory angličtiny

Výchozím analyzátorem je standardní Lucene, který funguje dobře pro angličtinu, ale ne i pro angličtinu nebo analyzátor od Microsoftu pro angličtinu. 
 
+ Analyzátor angličtiny pro Lucene v angličtině rozšiřuje standardní analyzátor. Odebere possessives (koncovou) z slov, aplikuje odvozování na Porter lemmatizátor Algorithm a odebere anglická slova o stopách.  

+ Microsoft English Analyzer vykonává lemmatizátor nebo předzpracování místo odvozování. To znamená, že dokáže zvládnout inflected a nepravidelně formulované wordové formy mnohem lepší, což vede k relevantnějším výsledkům vyhledávání. 

## <a name="configuring-analyzers"></a>Konfigurace analyzátorů

Analyzátory jazyka se používají tak, jak jsou. Pro každé pole v definici indexu můžete nastavit vlastnost **Analyzer** na název analyzátoru, který určuje jazyk a jazykové sady (Microsoft nebo Lucene). Stejný analyzátor bude použit při indexování a hledání daného pole. Můžete mít například samostatná pole pro anglická, francouzská a španělská označení hotelu, která existují vedle sebe ve stejném indexu. Alternativně můžete místo **analyzátoru**použít **indexAnalyzer** a **searchAnalyzer** pro různá pravidla analýzy v době indexování a dobu dotazování. 

Pomocí parametru dotazu **searchFields** určete, které pole pro konkrétní jazyk chcete v dotazech vyhledat. Můžete zkontrolovat příklady dotazů, které obsahují vlastnost Analyzer v [dokumentu hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Další informace o vlastnostech indexu naleznete v tématu [Create index &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Další informace o analýze v Azure Search najdete v tématu [analyzátory v Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Seznam analyzátoru jazyka 
 Níže je uvedený seznam podporovaných jazyků společně s názvy Lucene a Microsoft Analyzer.  

|Jazyk|Název programu Microsoft Analyzer|Název analyzátoru Lucene|  
|--------------|-----------------------------|--------------------------|  
|arabština|ar.microsoft|ar. Lucene|  
|Arménština||HY. Lucene|  
|Bengálština|bn.microsoft||  
|Baskičtina||EU. Lucene|  
|Bulharština|bg.microsoft|BG. Lucene|  
|Katalánština|ca.microsoft|CA. Lucene|  
|Zjednodušená čínština|zh-Hans.microsoft|zh-Hans. Lucene|  
|Tradiční čínština|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Chorvatština|hr.microsoft||  
|Čeština|cs.microsoft|cs. Lucene|  
|dánština|da.microsoft|da. Lucene|  
|Holandština|nl.microsoft|NL – Lucene|  
|Angličtina|en.microsoft|EN. Lucene|  
|Estonština|et.microsoft||  
|Finština|fi.microsoft|Fi. Lucene|  
|Francouzština|fr.microsoft|fr. Lucene|  
|Galicijština||HK. Lucene|  
|Němčina|de.microsoft|de. Lucene|  
|Řečtina|el.microsoft|El. Lucene|  
|Gudžarátština|gu.microsoft||  
|Hebrejština|he.microsoft||  
|Hindština|hi.microsoft|Dobrý den. Lucene|  
|Maďarština|hu.microsoft|hu. Lucene|  
|Islandština|is.microsoft||  
|Indonéština (Bahasa)|id.microsoft|ID. Lucene|  
|Irština||GA. Lucene|  
|italština|it.microsoft|IT. Lucene|  
|Japonština|ja.microsoft|Ja. Lucene|  
|Kannadština|kn.microsoft||  
|Korejština|ko.microsoft|Ko. Lucene|  
|Lotyština|lv.microsoft|Lotyšsko. Lucene|  
|Litevština|lt.microsoft||  
|Malajálamština|ml.microsoft||  
|Malajština (latinka)|ms.microsoft||  
|Maráthština|mr.microsoft||  
|norština|nb.microsoft|Ne. Lucene|  
|Perština||FA. Lucene|  
|polština|pl.microsoft|pl. Lucene|  
|Portugalština (Brazílie)|pt-Br.microsoft|pt-br. Lucene|  
|Portugalština (Portugalsko)|pt-Pt.microsoft|pt-PT. Lucene|  
|Paňdžábština|pa.microsoft||  
|Rumunština|ro.microsoft|ro. Lucene|  
|ruština|ru.microsoft|ru. Lucene|  
|Srbština (cyrilice)|sr-cyrillic.microsoft||  
|Srbština (latinka)|sr-latin.microsoft||  
|Slovenština|sk.microsoft||  
|Slovinština|sl.microsoft||  
|Španělština|es.microsoft|ES. Lucene|  
|švédština|sv.microsoft|sv. Lucene|  
|Tamilština|ta.microsoft||  
|Telugština|te.microsoft||  
|Thajština|th.microsoft|th. Lucene|  
|turečtina|tr.microsoft|TR. Lucene|  
|Ukrajinština|uk.microsoft||  
|Urdština|ur.microsoft||  
|Vietnamština|vi.microsoft||  

 Všechny analyzátory s názvy popsanými pomocí **Lucene** jsou napájené z [analyzátorů jazyka Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Viz také:  
 [Vytvoření služby &#40;Index Azure Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [Deanalýza třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Video: modul 7 Azure Search prezentace MVA](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

