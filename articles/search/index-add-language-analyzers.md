---
title: Přidat analyzátory jazyka do polí řetězců
titleSuffix: Azure Cognitive Search
description: Vícejazyčná analýza textu pro jiné než anglické dotazy a indexy v Azure Kognitivní hledání.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283144"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Přidání analyzátorů jazyka do polí řetězců v indexu Azure Kognitivní hledání

*Analyzátor jazyka* je konkrétní typ [analyzátoru textu](search-analyzers.md) , který provádí lexikální analýzu pomocí jazykových pravidel cílového jazyka. Každé prohledávatelné pole má vlastnost **Analyzer** . Pokud váš index obsahuje přeložené řetězce, jako jsou například samostatná pole pro angličtinu a čínský text, mohli byste zadat jazykové analyzátory pro každé pole, abyste měli přístup k bohatě lingvistických funkcím těchto analyzátorů.  

Azure Kognitivní hledání podporuje analyzátory 35 na základě Lucene a 50 analyzátory zajištěné technologií Microsoft pro zpracování přirozeného jazyka, která se používá v Office a Bingu.

## <a name="comparing-analyzers"></a>Porovnávání analyzátorů

Někteří vývojáři můžou preferovat známé, jednoduché a open source řešení Lucene. Analyzátory jazyka Lucene jsou rychlejší, ale analyzátory Microsoftu mají pokročilé možnosti, jako je lemmatizátor nebo předzpracování, wordová odkódování (v jazycích, jako je němčina, dánština, holandština, švédština, norština, finština, práce, maďarština, slovenština) a rozpoznávání entit (adresy URL, e-maily, data, čísla). Pokud je to možné, měli byste při rozhodování o tom, který z nich nejlépe vyhovuje, spustit porovnání analyzátorů Microsoftu a Lucene. 

Indexování pomocí analyzátorů Microsoft Analyzer se v závislosti na jazyku dokončí na průměrně dvakrát až třikrát pomaleji než jejich ekvivalenty Lucene. Pro dotazy na průměrnou velikost by nemělo být významně ovlivněný výkon hledání. 

### <a name="english-analyzers"></a>Analyzátory angličtiny

Výchozím analyzátorem je standardní Lucene, který funguje dobře pro angličtinu, ale ne i pro angličtinu nebo analyzátor od Microsoftu pro angličtinu. 
 
+ Analyzátor angličtiny pro Lucene v angličtině rozšiřuje standardní analyzátor. Odebere possessives (koncovou) z slov, aplikuje odvozování na Porter lemmatizátor Algorithm a odebere anglická slova o stopách.  

+ Microsoft English Analyzer vykonává lemmatizátor nebo předzpracování místo odvozování. To znamená, že může zpracovávat inflected a nepravidelně větší množství slov, což má za následek relevantnější výsledky hledání. 

## <a name="configuring-analyzers"></a>Konfigurace analyzátorů

Analyzátory jazyka se používají tak, jak jsou. Pro každé pole v definici indexu můžete nastavit vlastnost **Analyzer** na název analyzátoru, který určuje jazyk a jazykové sady (Microsoft nebo Lucene). Stejný analyzátor bude použit při indexování a hledání daného pole. Můžete mít například samostatná pole pro anglická, francouzská a španělská označení hotelu, která existují vedle sebe ve stejném indexu.

> [!NOTE]
> Není možné použít jiný analyzátor jazyka při indexování, než je doba dotazu v poli. Tato funkce je vyhrazena pro [vlastní analyzátory](index-add-custom-analyzers.md). Z tohoto důvodu, pokud se pokusíte nastavit vlastnosti **searchAnalyzer** nebo **indexAnalyzer** na název analyzátoru jazyka, REST API vrátí chybovou odpověď. Místo toho je nutné použít vlastnost **Analyzer** .

Pomocí parametru dotazu **searchFields** určete, které pole pro konkrétní jazyk chcete v dotazech vyhledat. Můžete zkontrolovat příklady dotazů, které obsahují vlastnost Analyzer v [dokumentu hledání](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Další informace o vlastnostech indexu najdete v tématu [Create index &#40;Azure Kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Další informace o analýze v Azure Kognitivní hledání najdete v tématu [analyzátory v azure kognitivní hledání](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Seznam analyzátoru jazyka 
 Níže je uvedený seznam podporovaných jazyků společně s názvy Lucene a Microsoft Analyzer.  

|Jazyk|Název programu Microsoft Analyzer|Název analyzátoru Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabština|ar. Microsoft|ar. Lucene|  
|Arménština||HY. Lucene|  
|Bengálština|BN. Microsoft||  
|Baskičtina||EU. Lucene|  
|Bulharština|BG. Microsoft|BG. Lucene|  
|Katalánština|CA. Microsoft|CA. Lucene|  
|Čínština (zjednodušená)|zh-Hans. Microsoft|zh-Hans. Lucene|  
|Čínština (tradiční)|zh-Hant. Microsoft|zh-Hant. Lucene|  
|Chorvatština|HR. Microsoft||  
|Čeština|cs. Microsoft|cs. Lucene|  
|Dánština|da. Microsoft|da. Lucene|  
|Nizozemština|NL – Microsoft|NL – Lucene|  
|Angličtina|EN. Microsoft|EN. Lucene|  
|Estonština|et. Microsoft||  
|Finština|Fi. Microsoft|Fi. Lucene|  
|Francouzština|fr. Microsoft|fr. Lucene|  
|Galicijština||HK. Lucene|  
|Němčina|de. Microsoft|de. Lucene|  
|Řečtina|El. Microsoft|El. Lucene|  
|Gudžarátština|Gu. Microsoft||  
|Hebrejština|IT. Microsoft||  
|Hindština|Dobrý den. Microsoft|Dobrý den. Lucene|  
|Maďarština|hu. Microsoft|hu. Lucene|  
|Islandština|je. Microsoft||  
|Indonéština (Bahasa)|ID. Microsoft|ID. Lucene|  
|Irština||GA. Lucene|  
|Italština|IT. Microsoft|IT. Lucene|  
|Japonština|Ja. Microsoft|Ja. Lucene|  
|Kannadština|KN. Microsoft||  
|Korejština|Ko. Microsoft|Ko. Lucene|  
|Lotyština|Lotyšsko. Microsoft|Lotyšsko. Lucene|  
|Litevština|lt. Microsoft||  
|Malajalámština|ml. Microsoft||  
|Malajština (latinka)|MS. Microsoft||  
|Maráthština|Mr. Microsoft||  
|Norština|NB. Microsoft|Ne. Lucene|  
|Perština||FA. Lucene|  
|Polština|pl. Microsoft|pl. Lucene|  
|Portugalština (Brazílie)|pt-br. Microsoft|pt-br. Lucene|  
|portugalština (Portugalsko)|pt-PT. Microsoft|pt-PT. Lucene|  
|Paňdžábština|PA. Microsoft||  
|Rumunština|ro. Microsoft|ro. Lucene|  
|Ruština|ru. Microsoft|ru. Lucene|  
|Srbština (cyrilice)|SR-cyrilice. Microsoft||  
|Srbština (latinka)|SR-Latin. Microsoft||  
|Slovenština|sk. Microsoft||  
|Slovinština|SL. Microsoft||  
|Španělština|ES. Microsoft|ES. Lucene|  
|Švédština|sv. Microsoft|sv. Lucene|  
|Tamilština|Ta. Microsoft||  
|Telugština|te. Microsoft||  
|Thajština|th. Microsoft|th. Lucene|  
|Turečtina|TR. Microsoft|TR. Lucene|  
|Ukrajinština|Spojené království. Microsoft||  
|Urdština|vaše společnost. Microsoft||  
|Vietnamština|VI. Microsoft||  

 Všechny analyzátory s názvy popsanými pomocí **Lucene** jsou napájené z [analyzátorů jazyka Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Viz také  

+ [Vytvoření indexu &#40;Azure Kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Deanalýza třídy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

