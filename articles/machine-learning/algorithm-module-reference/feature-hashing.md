---
title: 'Hodnoty hash funkcí: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul hash funkcí ve službě Azure Machine Learning k zpracování textových dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 78d01cf071faed312773ebf12c75e7e6e5596e71
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210842"
---
# <a name="feature-hashing"></a>Hodnoty hash funkcí

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

K transformaci datového proudu anglického textu do sady celočíselných funkcí použijte modul **hash funkcí** . Tuto funkci s hodnotou hash můžete následně předat algoritmu strojového učení a naučit se model pro analýzu textu.

Funkce hash funkcí uvedená v tomto modulu vychází z rozhraní nimbusml. Další informace naleznete v tématu [Třída NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Co je funkce hashing funkcí

Funkce hashing funkcí funguje převodem jedinečných tokenů na celá čísla. Funguje na přesných řetězcích, které zadáte jako vstup a neprovádí žádnou jazykovou analýzu ani předzpracování. 

Například proveďte sadu jednoduchých vět, jako třeba, a potom mínění skóre. Předpokládejme, že chcete použít tento text k vytvoření modelu.

|USERTEXT|MÍNĚNÍ|
|--------------|---------------|
|Jsem mi blízký tuto knihu|3|
|Hated tuto knihu|1|
|Tato kniha byla Skvělé|3|
|Líbí se mi moje knihy|2|

Interně modul **hashování funkcí** vytvoří slovník n-gramů. Například seznam bigrams pro tuto datovou sadu by byl podobný tomuto:

|TERMÍN (bigrams)|OPAKOVÁNÍ|
|------------|---------------|
|Tato kniha|3|
|Jsem blízký|1|
|Hated|1|
|Líbí se mi|1|

Velikost n-gramů můžete řídit pomocí vlastnosti **n-gramů** . Zvolíte-li možnost bigrams, jsou vypočítány také unigrams. Slovník by měl také zahrnovat jednotlivé výrazy, jako jsou tyto:

|Termín (unigrams)|OPAKOVÁNÍ|
|------------|---------------|
|účetních|3|
|I|3|
|příruček|1|
|byl|1|

Po vytvoření slovníku modul **hash funkcí** převede výrazy slovníku na hodnoty hash. Pak vypočítá, zda byla funkce v každém případě použita. Pro každý řádek textových dat modul výstupuje sadu sloupců, jeden sloupec pro každou funkci s algoritmem hash.

Například po použití algoritmu hash mohou sloupce funkce vypadat přibližně takto:

|Hodnocení|Hash – funkce 1|Funkce hashing 2|Funkce hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Pokud je hodnota ve sloupci 0, řádek neobsahuje funkci hash.
* Pokud je hodnota 1, řádek obsahuje funkci.

Funkce hashing funkcí umožňuje reprezentovat textové dokumenty s proměnlivou délkou jako číselné vektory funkcí stejné délky, aby bylo možné omezit dimenzionální omezení. Pokud jste se pokusili použít textový sloupec pro školení tak, jak je, bude považován za sloupec funkce kategorií s mnoha jedinečnými hodnotami.

Číselné výstupy také umožňují používat běžné metody strojového učení, včetně klasifikace, clusteringu a načítání informací. Vzhledem k tomu, že operace vyhledávání můžou použít celočíselnou hodnotu hash spíše než porovnávání řetězců, získání váhy funkcí je také mnohem rychlejší.

## <a name="configure-feature-hashing"></a>Konfigurace funkce hashing funkcí

1.  Přidejte modul **hash funkcí** do experimentu v vizuálním rozhraní.

1. Připojte datovou sadu obsahující text, který chcete analyzovat.

    > [!TIP]
    > Vzhledem k tomu, že funkce hashing funkcí neprovádí lexikální operace, jako je například odvozování nebo zkrácení, můžete někdy dosáhnout lepších výsledků, protože před použitím funkce hashování funkcí je nutné předzpracování textu. 

1. Nastavte **cílové sloupce** na textové sloupce, které chcete převést na funkce s algoritmem hash. 

    * Sloupce musí být řetězcového datového typu.
    
    * Výběr více textových sloupců může mít výrazný vliv na dimenzionální funkce. Například počet sloupců pro 10 bitů hodnoty hash přechází z 1024 na jeden sloupec na 2048 pro dva sloupce.

1. Použijte **algoritmus hashing bitsize** k určení počtu bitů, které se mají použít při vytváření zatřiďovací tabulky.
    
    Výchozí bitová velikost je 10. V případě mnoha problémů je tato hodnota dostačující. Je možné, že budete potřebovat více místa, abyste se vyhnuli kolizím v závislosti na velikosti "slovníku výukového textu n gramů".
    
1. U **N-gramů**zadejte číslo definující maximální délku N-gramů, které se mají přidat do školicího slovníku. N-gram je sekvence *n* slov, která je považována za jedinečnou jednotku.

    Pokud například zadáte 3, unigrams, bigrams a trigrams, vytvoří se.

1. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení zpracování modul vypíše transformovaná datovou sadu, ve které byl původní textový sloupec převeden na více sloupců, z nichž každá představuje funkci v textu. V závislosti na tom, jak velký je slovník, může být výsledná datová sada velká:

|Název sloupce 1|Typ sloupce 2|
|-------------------|-------------------|
|USERTEXT|Původní datový sloupec|
|MÍNĚNÍ|Původní datový sloupec|
|USERTEXT-hashing – funkce 1|Sloupec funkce s algoritmem hash|
|USERTEXT-hash – funkce 2|Sloupec funkce s algoritmem hash|
|USERTEXT-hash – funkce n|Sloupec funkce s algoritmem hash|
|USERTEXT-hashing – funkce 1024|Sloupec funkce s algoritmem hash|

Po vytvoření transformované datové sady můžete použít jako vstup do modulu vlakového modelu.
 
### <a name="best-practices"></a>Osvědčené postupy

Následující osvědčené postupy vám pomůžou využít modul **hashování funkcí** na maximum:

* Před použitím **hashování funkcí** k předběžnému zpracování vstupního textu přidejte modul **textu před zpracování** . 

* Přidejte modul **Výběr sloupců** za modul **hashování funkcí** , aby se odstranily textové sloupce z výstupní sady dat. Po vygenerování funkcí hash nepotřebujete textové sloupce.
    
* Zvažte použití těchto možností předzpracování textu, abyste zjednodušili výsledky a vylepšili přesnost:

    * Dělení slov
    * Zastavit odebrání slova
    * Normalizace případu
    * Odebrání interpunkčních znamének a speciálních znaků
    * Způsobenému výpadky proudu  

Optimální sada předzpracovaných metod, které se mají použít v jednotlivých řešeních, závisí na doménách, slovnících a obchodních potřebách. Experimentujte s daty a zjistěte, které metody zpracování textu jsou nejefektivnější.

## <a name="next-steps"></a>Další postup
            
Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
