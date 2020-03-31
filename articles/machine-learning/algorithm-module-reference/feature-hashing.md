---
title: Odkaz na modul hash funkce
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Hashing funkcí v Azure Machine Learning featurize textová data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456246"
---
# <a name="feature-hashing-module-reference"></a>Odkaz na modul hash funkce

Tento článek popisuje modul zahrnutý v návrháři Azure Machine Learning (preview).

Pomocí modulu Hashing funkcí můžete transformovat datový proud anglického textu do sady funkcí celého čísla. Potom můžete předat tuto funkci hash nastavenou na algoritmus strojového učení pro trénování modelu analýzy textu.

Funkce hash funkce poskytované v tomto modulu je založena na rozhraní nimbusml. Další informace naleznete v tématu [NgramHash class](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Co je funkce hash?

Funkce hash funguje převedením jedinečných tokenů na celá čísla. Pracuje na přesné řetězce, které zadáte jako vstup a neprovádí žádné jazykové analýzy nebo předběžné zpracování. 

Vezměte například sadu jednoduchých vět, jako jsou tyto, následované skóre mínění. Předpokládejme, že chcete použít tento text k vytvoření modelu.

|Text uživatele|Mínění|
|--------------|---------------|
|Miloval jsem tuto knihu|3|
|Nenáviděl jsem tuto knihu|1|
|Tato kniha byla skvělá|3|
|Miluji knihy|2|

Interně modul Hashing funkce vytvoří slovník n-gramů. Například seznam bigrams pro tuto datovou sadu by bylo něco jako toto:

|Termín (bigrams)|Frequency|
|------------|---------------|
|Tato kniha|3|
|Miloval jsem|1|
|Nesnášel jsem to.|1|
|Miluji|1|

Můžete řídit velikost n-gramů pomocí **N-gramů** vlastnost. Pokud zvolíte bigrams, jsou také počítány unigrams. Slovník by také obsahovat jednotlivé termíny, jako jsou tyto:

|Termín (unigramy)|Frequency|
|------------|---------------|
|Kniha|3|
|I|3|
|books|1|
|Byl|1|

Po sestrojově slovníku modul Hashing funkce převede termíny slovníku na hodnoty hash. Potom vypočítá, zda byla funkce použita v každém případě. Pro každý řádek textových dat modul vypíše sadu sloupců, jeden sloupec pro každou funkci hashe.

Například po zahašování mohou sloupce funkcí vypadat přibližně takto:

|Rating|Funkce hašování 1|Funkce hašování 2|Funkce hašování 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Pokud je hodnota ve sloupci 0, řádek neobsahuje funkci hashe.
* Pokud je hodnota 1, řádek obsahoval funkci.

Zapisování funkcí umožňuje reprezentovat textové dokumenty o proměnné délce jako číselné vektory prvků stejné délky pro snížení dimenzionality. Pokud jste se pokusili použít textový sloupec pro školení tak, jak je, by bylo považováno za sloupec kategorických funkcí s mnoha odlišnými hodnotami.

Číselné výstupy také umožňují používat běžné metody strojového učení, včetně klasifikace, clusteringu a načítání informací. Vzhledem k tomu, že vyhledávací operace můžete použít trvalá hashe spíše než porovnání řetězců, získání funkce váhy je také mnohem rychlejší.

## <a name="configure-the-feature-hashing-module"></a>Konfigurace modulu Hash funkce

1.  Přidejte modul Hodnotit funkce do kanálu v návrháři.

1. Připojte datovou sadu obsahující text, který chcete analyzovat.

    > [!TIP]
    > Vzhledem k tomu, že funkce hash neprovádí lexikální operace, jako je například vyplývající nebo zkrácení, můžete někdy získat lepší výsledky předzpracovánítextu před použitím funkce hash. 

1. Nastavte **cílové sloupce** na textové sloupce, které chcete převést na funkce hashe. Mějte na paměti, že:

    * Sloupce musí být datový typ řetězce.
    
    * Výběr více sloupců textu může mít významný vliv na dimenzionalitu funkce. Například počet sloupců pro 10bitovou hash přejde z 1 024 pro jeden sloupec na 2 048 pro dva sloupce.

1. Pomocí **bitové velikosti hash** určete počet bitů, které se mají použít při vytváření tabulky hash.
    
    Výchozí velikost bitu je 10. Pro mnoho problémů je tato hodnota přiměřená. Možná budete potřebovat více místa, abyste se vyhnuli kolizím, v závislosti na velikosti slovníku n-gramů v textu školení.
    
1. Pro **N-gramů**zadejte číslo, které definuje maximální délku n-gramů, které chcete přidat do školicího slovníku. N-gram je posloupnost *n* slov, považovaných za jedinečnou jednotku.

    Pokud například zadáte 3, vytvoří se jednogramy, bigramy a trigramy.

1. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po dokončení zpracování modul vypíše transformovnou datovou sadu, ve které byl původní sloupec textu převeden na více sloupců. Každý sloupec představuje prvek v textu. V závislosti na tom, jak významný je slovník, může být výsledná datová sada velká:

|Název sloupce 1|Typ sloupce 2|
|-------------------|-------------------|
|UŽIVATELSKÝ TEXT|Původní sloupec dat|
|Sentiment|Původní sloupec dat|
|USERTEXT - Hashing funkce 1|Sloupec funkce hasí|
|USERTEXT - Hashing funkce 2|Sloupec funkce hasí|
|USERTEXT - Hashing funkce n|Sloupec funkce hasí|
|USERTEXT - Hashing funkce 1024|Sloupec funkce hasí|

Po vytvoření transformované datové sady ji můžete použít jako vstup do modulu Model vlaku.
 
## <a name="best-practices"></a>Osvědčené postupy

Následující doporučené postupy vám mohou pomoci maximálně využít modul Hashing funkcí:

* Před použitím funkce Hashing k předběžnému zpracování vstupního textu přidejte modul Předzpracování textu. 

* Přidáním modulu Vybrat sloupce za modul Hodnotit hodnotu prvku odeberte textové sloupce z výstupní datové sady. Po vygenerování funkcí hash nepotřebujete textové sloupce.
    
* Zvažte použití těchto možností předběžného zpracování textu, abyste zjednodušili výsledky a zlepšili přesnost:

    * Rozdělení slov
    * Zastavení odstranění slova
    * Normalizace případu
    * Odstranění interpunkce a speciálních znaků
    * Vyplývající  

Optimální sada metod předběžného zpracování, které se použijí v jakémkoli řešení, závisí na potřebě domény, slovní zásoby a podnikání. s daty, abyste zjistili, které metody zpracování textu jsou nejúčinnější.

## <a name="next-steps"></a>Další kroky
            
Podívejte se na [sadu modulů dostupných](module-reference.md) pro Azure Machine Learning 
