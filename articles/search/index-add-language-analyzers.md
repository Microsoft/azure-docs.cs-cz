---
title: Přidejte jazykové analyzátory – Azure Search
description: Analýza textu ve více jazycích lexikální pro jiné než anglické jazykové dotazy a indexů ve službě Azure Search.
ms.date: 01/31/2019
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
ms.openlocfilehash: b5c562994c169a8c5d51ee31a9606c5c40162603
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008332"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Přidat do indexu Azure Search analyzátory jazyka

A *analyzátor jazyka* je konkrétní součást [fulltextového vyhledávacího modulu](https://docs.microsoft.com/azure/search/search-lucene-query-architecture) , který provádí lexikální analýzu použití jazykových pravidel cílový jazyk. Všechny prohledávatelné pole má `analyzer` vlastnost. Pokud indexu obsahuje přeložené řetězce, jako je například samostatnými poli pro angličtinu a čínský textu, můžete zadat jazykové analyzátory u každého pole pro přístup k bohaté jazykové možnosti těchto analyzátory.  

Služba Azure Search podporuje 35 Analyzátory se opírá o Lucene a 50 Analyzátory se opírá o zpracování technologie používané v Office a Bing proprietární Microsoft přirozeného jazyka.

## <a name="compare-language-analyzer-types"></a>Porovnání typů analyzátoru jazyka 

Někteří vývojáři dát přednost více do hloubky, jednoduchý, open source řešení Lucene. Analyzátory jazyka Lucene jsou rychlejší, ale Microsoft analyzátory mít rozšířené možnosti, jako je například lemmatizátor, word decompounding (v jazycích jako němčina, dánština, holandština, švédština, norština, estonština, dokončit, maďarština, slovenština) a entity rozpoznávání (adresy URL, e-mailů, data, čísla). Pokud je to možné měli byste spustit porovnání společnosti Microsoft a Lucene Analyzátory se rozhodnout, která z nich je lépe vyhovovat. 

Indexování s analyzátory Microsoft je v průměru dvakrát až třikrát pomalejší než jejich ekvivalenty Lucene, v závislosti na jazyku. Průměrná velikost dotazů by neměly výrazně ovlivnil výkon vyhledávání. 

### <a name="english-analyzers"></a>Anglickou analyzátory

Výchozí Analyzátor je standardní Lucene, což funguje dobře pro angličtinu, ale možná ne stejně jako anglické analyzátor Lucene společnosti nebo analyzátor anglické společnosti Microsoft. 
 
+ Anglickou analyzátor Lucene rozšiřuje standardní analyzátor. Zruší slova Přivlastňovací pád (na konci vaší), platí slovního rozboru podle slovního rozboru silné algoritmus a odebere anglické nevýznamová slova.  

+ Analyzátor anglické společnosti Microsoft provádí lemmatizátor místo slovního rozboru. To znamená, že dokáže zpracovat tvary slov tvary a nestandardní co mnohem lépe výsledkem relevantnější výsledky hledání 

 > [!Tip]
 > [Ukázka analyzátoru hledání](https://alice.unearth.ai/) poskytuje vedle sebe porovnání výsledků vytvářené standardní analyzátor Lucene, analyzátor Lucene pro angličtinu a procesor anglické přirozeného jazyka od Microsoftu. Pro každý vstupní hledání zadáte, výsledky z každé analyzátoru jsou zobrazeny v sousední podoken.

## <a name="analyzer-configuration"></a>Analyzátor konfigurace

Pro každé pole v definici indexu můžete nastavit `analyzer` vlastnost na název analyzátor, který určuje, který jazyk a dodavatele. Stejné Analyzátor se použijí při indexování a vyhledávání pro toto pole. Například můžete mít samostatnými poli pro angličtinu, francouzštinu a španělština hotelu popisy, které existovat vedle sebe ve stejném indexu.  

Použití **searchFields** parametr určíte, které pole specifické pro jazyk a hledejte proti v dotazech dotazu. Příklady dotazů, které obsahují vlastnost analyzátoru hledání dokumentů, můžete zkontrolovat. 

Další informace o vlastnosti indexu, naleznete v tématu [Create Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Další informace o analýze ve službě Azure Search najdete v tématu [analyzátory ve službě Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

## <a name="analyzer-list"></a>Seznam analyzátor  
 Níže je seznam podporovaných jazyků spolu s názvy analyzátor Lucene a Microsoft.  

|Jazyk|Název analyzátoru Microsoft|Název analyzátor Lucene|  
|--------------|-----------------------------|--------------------------|  
|arabština|ar.microsoft|ar.lucene|  
|Arménština||hy.lucene|  
|Bengálština|bn.microsoft||  
|Baskičtina||EU.lucene|  
|Bulharština|bg.microsoft|BG.lucene|  
|Katalánština|ca.microsoft|CA.lucene|  
|čínština (zjednodušená)|zh-Hans.microsoft|zh-Hans.lucene|  
|Tradiční čínština|zh-Hant.microsoft|zh-Hant.lucene|  
|Chorvatština|hr.microsoft||  
|Čeština|cs.microsoft|cs.lucene|  
|dánština|da.microsoft|da.lucene|  
|Holandština|nl.microsoft|NL.lucene|  
|Angličtina|en.microsoft|en.lucene|  
|Estonština|et.microsoft||  
|Finština|fi.microsoft|Fi.lucene|  
|Francouzština|fr.microsoft|FR.lucene|  
|Galicijština||GL.lucene|  
|Němčina|de.microsoft|de.lucene|  
|Řečtina|el.microsoft|El.lucene|  
|Gudžarátština|gu.microsoft||  
|Hebrejština|he.microsoft||  
|Hindština|hi.microsoft|Hi.lucene|  
|Maďarština|hu.microsoft|HU.lucene|  
|Islandština|is.microsoft||  
|Indonéština (Bahasa)|id.microsoft|ID.lucene|  
|Irština||GA.lucene|  
|italština|it.microsoft|IT.lucene|  
|Japonština|ja.microsoft|ja.lucene|  
|Kannadština|ka.microsoft||  
|Korejština|ko.microsoft|Ko.lucene|  
|Lotyština|lv.microsoft|LV.lucene|  
|Litevština|lt.microsoft||  
|Malajálamština|ml.microsoft||  
|Malajština (latinka)|ms.microsoft||  
|Maráthština|mr.microsoft||  
|norština|nb.microsoft|No.lucene|  
|Perština||fa.lucene|  
|polština|pl.microsoft|PL.lucene|  
|Portugalština (Brazílie)|pt-Br.microsoft|PT Br.lucene|  
|Portugalština (Portugalsko)|pt-Pt.microsoft|PT Pt.lucene|  
|Paňdžábština|pa.microsoft||  
|Rumunština|ro.microsoft|ro.lucene|  
|ruština|ru.microsoft|RU.lucene|  
|Srbština (cyrilice)|sr-cyrillic.microsoft||  
|Srbština (latinka)|sr-latin.microsoft||  
|Slovenština|sk.microsoft||  
|Slovinština|sl.microsoft||  
|Španělština|es.microsoft|ES.lucene|  
|švédština|sv.microsoft|Sv.lucene|  
|Tamilština|ta.microsoft||  
|Telugština|te.microsoft||  
|Thajština|th.microsoft|TH.lucene|  
|turečtina|tr.microsoft|TR.lucene|  
|Ukrajinština|uk.microsoft||  
|Urdština|ur.microsoft||  
|Vietnamština|vi.microsoft||  

 Všechny analyzátory s názvy opatřen poznámkou **Lucene** využívají [Apache Lucene jazykové analyzátory](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Další informace najdete v tématech  
 [Vytvořit Index &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [Třída AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Video: modul 7 Azure Search MVA prezentace](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

