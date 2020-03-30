---
title: 'Vlak SVD Doporučující: Modul Reference'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Train SVD Recommender v Azure Machine Learning trénovat Bayesian doporučující pomocí algoritmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 417ef716c391126982809574fc16f6681115cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477404"
---
# <a name="train-svd-recommender"></a>Trénování doporučovacího systému SVD

Tento článek popisuje, jak používat modul Train SVD Recommender v návrháři Azure Machine Learning (preview). Tento modul slouží k trénování modelu doporučení založeného na algoritmu rozkladu jedné hodnoty (SVD).  

Train SVD Recommender modul čte datovou sadu user-item-rating triples. Vrátí vyškolený SVD doporučující. Potom můžete použít trénovaný model předpovědět hodnocení nebo generovat doporučení pomocí skóre [SVD recommender](score-svd-recommender.md) modul.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Další informace o modelech doporučení a doporučující svd  

Hlavním cílem systému doporučení je doporučit *uživatelům* systému jednu nebo více *položek.* Příkladem položky může být film, restaurace, kniha nebo skladba. Uživatelem může být osoba, skupina osob nebo jiná entita s předvolbami položky.  

Existují dva hlavní přístupy k doporučující systémy: 

+ Přístup **založený na obsahu** využívá funkce pro uživatele i položky. Uživatelé mohou být popsány podle vlastností, jako je věk a pohlaví. Položky lze popsat vlastnostmi, jako je autor a výrobce. Typické příklady systémů doporučení založených na obsahu najdete na stránkách sociální tvorby dohazování. 
+ **Kolaborativní filtrování** používá pouze identifikátory uživatelů a položek. Získá implicitní informace o těchto entit ách z (řídké) matice hodnocení, kterou uživatelé k položkám. O uživateli se můžeme dozvědět z položek, které ohodnotil, a od ostatních uživatelů, kteří ohodnotili stejné položky.  

SVD doporučující používá identifikátory uživatelů a položek, a matice hodnocení dané uživateli položky. Je to *spolupráce doporučuje .* 

Další informace o doporučujícím pracovníkovi SVD naleznete v příslušném výzkumném dokumentu: [Maticové faktorizační techniky pro doporučující systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Jak nakonfigurovat Train SVD Recommender  

### <a name="prepare-data"></a>Příprava dat

Před použitím modulu musí být vstupní data ve formátu, který očekává model doporučení. Je vyžadována trénovací datová sada trojnásobků hodnocení uživatelských položek.

+ První sloupec obsahuje identifikátory uživatelů.
+ Druhý sloupec obsahuje identifikátory položek.
+ Třetí sloupec obsahuje hodnocení pro dvojici uživatelských položek. Hodnoty hodnocení musí být číselný typ.  

Datová sada **Hodnocení filmů** v návrháři Azure Machine Learning (vyberte **datové sady** a pak **ukázky)** ukazuje očekávaný formát:

![Hodnocení filmů](media/module/movie-ratings-dataset.png)

Z této ukázky vidíte, že jeden uživatel ohodnotil několik filmů. 

### <a name="train-the-model"></a>Trénování modelu

1.  Přidejte modul Train SVD Recommender do kanálu v návrháři a připojte jej k trénovacím datům.  
   
2.  V **poli Počet faktorů**zadejte počet faktorů, které se mají použít s doporučující osobou.  
    
    Každý faktor měří, jak moc uživatel souvisí s položkou. Počet faktorů je také dimenzionalita latentního faktorového prostoru. S rostoucím počtem uživatelů a položek je lepší nastavit větší počet faktorů. Ale pokud je číslo příliš velké, výkon může klesnout.
    
3.  **Počet iterací algoritmu doporučení** označuje, kolikrát by měl algoritmus zpracovat vstupní data. Čím vyšší je toto číslo, tím přesnější jsou předpovědi. Vyšší počet však znamená pomalejší trénink. Výchozí hodnota je 30.

4.  V **poli Míra učení**zadejte číslo mezi 0,0 a 2,0, které definuje velikost kroku pro učení.

    Rychlost učení určuje velikost kroku v každé iteraci. Pokud je velikost kroku příliš velká, můžete přestřelit optimální řešení. Pokud je velikost kroku příliš malá, školení trvá déle najít nejlepší řešení. 
  
5.  Odešlete potrubí.  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
