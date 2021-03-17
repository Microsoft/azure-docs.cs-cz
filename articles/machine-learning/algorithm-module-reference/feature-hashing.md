---
title: Reference k modulům hash funkcí
titleSuffix: Azure Machine Learning
description: Naučte se používat modul hashování funkcí v Návrháři Azure Machine Learning k zpracování textových dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420780"
---
# <a name="feature-hashing-module-reference"></a>Reference k modulům hash funkcí

Tento článek popisuje modul zahrnutý v Návrháři Azure Machine Learning.

K transformaci datového proudu anglického textu do sady celočíselných funkcí použijte modul hash funkcí. Tuto funkci s hodnotou hash můžete následně předat algoritmu strojového učení a naučit se model pro analýzu textu.

Funkce hash funkcí uvedená v tomto modulu vychází z rozhraní nimbusml. Další informace naleznete v tématu [Třída NgramHash](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>Co je funkce hashing funkcí?

Funkce hashing funkcí funguje převodem jedinečných tokenů na celá čísla. Funguje na přesných řetězcích, které zadáte jako vstup a neprovádí žádnou jazykovou analýzu ani předzpracování. 

Například proveďte sadu jednoduchých vět, jako třeba, a potom mínění skóre. Předpokládejme, že chcete použít tento text k vytvoření modelu.

|Uživatelský text|Mínění|
|--------------|---------------|
|Jsem mi blízký tuto knihu|3|
|Hated tuto knihu|1|
|Tato kniha byla Skvělé|3|
|Líbí se mi moje knihy|2|

Interně modul hashování funkcí vytvoří slovník n-gramů. Například seznam bigrams pro tuto datovou sadu by byl podobný tomuto:

|Termín (bigrams)|Frekvence|
|------------|---------------|
|Tato kniha|3|
|Jsem blízký|1|
|Hated|1|
|Líbí se mi|1|

Velikost n-gramů můžete řídit pomocí vlastnosti **n-gramů** . Zvolíte-li možnost bigrams, jsou vypočítány také unigrams. Slovník by měl také zahrnovat jednotlivé výrazy, jako jsou tyto:

|Termín (unigrams)|Frekvence|
|------------|---------------|
|účetních|3|
|I|3|
|books|1|
|vytvořen|1|

Po vytvoření slovníku modul hash funkcí převede výrazy slovníku na hodnoty hash. Pak vypočítá, zda byla funkce v každém případě použita. Pro každý řádek textových dat modul výstupuje sadu sloupců, jeden sloupec pro každou funkci s algoritmem hash.

Například po použití algoritmu hash mohou sloupce funkce vypadat přibližně takto:

|Rating|Hash – funkce 1|Funkce hashing 2|Funkce hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Pokud je hodnota ve sloupci 0, řádek neobsahuje funkci hash.
* Pokud je hodnota 1, řádek obsahuje funkci.

Funkce hashing funkcí umožňuje reprezentovat textové dokumenty s proměnlivou délkou jako vektory numerické funkce stejné délky, aby se snížila hodnota argumentu. Pokud jste se pokusili použít textový sloupec pro školení tak, jak je, bude považován za sloupec funkce kategorií s mnoha jedinečnými hodnotami.

Číselné výstupy také umožňují používat běžné metody strojového učení, včetně klasifikace, clusteringu a načítání informací. Vzhledem k tomu, že operace vyhledávání můžou použít celočíselnou hodnotu hash spíše než porovnávání řetězců, získání váhy funkcí je také mnohem rychlejší.

## <a name="configure-the-feature-hashing-module"></a>Konfigurace modulu hashování funkcí

1.  Přidejte modul hash funkcí do kanálu v návrháři.

1. Připojte datovou sadu obsahující text, který chcete analyzovat.

    > [!TIP]
    > Vzhledem k tomu, že funkce hashing funkcí neprovádí lexikální operace, jako je například odvozování nebo zkrácení, můžete někdy dosáhnout lepších výsledků předzpracováním textu před použitím funkce hashování funkcí. 

1. Nastavte **cílové sloupce** na textové sloupce, které chcete převést na funkce s algoritmem hash. Pamatujte na to, že:

    * Sloupce musí být řetězcového datového typu.
    
    * Výběr více textových sloupců může mít výrazný vliv na dimenzionální funkce. Například počet sloupců pro 10 bitů hodnoty hash přechází z 1 024 na jeden sloupec na 2 048 pro dva sloupce.

1. Použijte **algoritmus hashing bitsize** k určení počtu bitů, které se mají použít při vytváření zatřiďovací tabulky.
    
    Výchozí bitová velikost je 10. V případě mnoha problémů je tato hodnota dostačující. Je možné, že budete potřebovat více místa, abyste se vyhnuli kolizím, v závislosti na velikosti slovníku n gramů v školicím textu.
    
1. U **n-gramů** zadejte číslo definující maximální délku N-gramů, které se mají přidat do školicího slovníku. N-gram je sekvence *n* slov, která je považována za jedinečnou jednotku.

    Pokud například zadáte 3, unigrams, bigrams a trigrams, vytvoří se.

1. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení zpracování modul vypíše transformovaná datovou sadu, ve které byl původní textový sloupec převeden na více sloupců. Každý sloupec představuje funkci v textu. V závislosti na tom, jak velký je slovník, může být výsledná datová sada velká:

|Název sloupce 1|Typ sloupce 2|
|-------------------|-------------------|
|USERTEXT|Původní datový sloupec|
|MÍNĚNÍ|Původní datový sloupec|
|USERTEXT-hashing – funkce 1|Sloupec funkce s algoritmem hash|
|USERTEXT-hash – funkce 2|Sloupec funkce s algoritmem hash|
|USERTEXT-hash – funkce n|Sloupec funkce s algoritmem hash|
|USERTEXT-hashing – funkce 1024|Sloupec funkce s algoritmem hash|

Po vytvoření transformované datové sady můžete použít jako vstup do modulu vlakového modelu.
 
## <a name="best-practices"></a>Osvědčené postupy

Následující osvědčené postupy vám pomůžou využít modul hashování funkcí na maximum:

* Před použitím hashování funkcí k předběžnému zpracování vstupního textu přidejte modul textu před zpracování. 

* Přidejte modul Výběr sloupců za modul hashování funkcí, aby se odstranily textové sloupce z výstupní datové sady. Po vygenerování funkcí hash nepotřebujete textové sloupce.
    
* Zvažte použití těchto možností předzpracování textu, abyste zjednodušili výsledky a vylepšili přesnost:

    * Dělení slov
    * Zastavení odebrání slova
    * Normalizace případu
    * Odebrání interpunkčních znamének a speciálních znaků
    * Způsobenému výpadky proudu  

Optimální sada metod předběžného zpracování, která se má použít v jakémkoli řešení, závisí na doméně, slovníku a potřebě firmy. Podívejte se na kanály s daty a zjistěte, které metody zpracování textu jsou nejefektivnější.

## <a name="next-steps"></a>Další kroky
            
Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning