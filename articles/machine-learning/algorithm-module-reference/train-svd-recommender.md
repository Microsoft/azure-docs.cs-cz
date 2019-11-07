---
title: 'Doporučení pro výukový SVD: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak ve službě Azure Machine Learning použít modul doporučeného učení SVD, abyste mohli bayesovského rozhodování doporučit pomocí algoritmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 2019b752ab224abc244e471de3d427a77f7ed93a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716905"
---
# <a name="train-svd-recommender"></a>Doporučení pro výuku SVD

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul doporučených postupů pro vlak SVD (Preview). Tento modul slouží ke školení modelu doporučení založeného na algoritmu dekompozice s jednou hodnotou (SVD).  

Modul doporučení SVDu pro vlaky přečte datovou sadu trojí pro hodnocení položek. Vrátí SVD doporučení. Potom můžete pomocí vyškolený model předpovědět hodnocení nebo vygenerovat doporučení pomocí modulu [SVD doporučeného skóre](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Další informace o modelech doporučení a doporučení SVD  

Hlavním cílem systému doporučení je doporučit *uživatelům* systému jednu nebo více *položek* . Příkladem položky může být film, restaurace, kniha nebo písně. Uživatel může být osoba, skupina osob nebo jiná entita s preferencemi položek.  

Existují dva hlavní přístupy ke doporučeným systémům: 

+ Přístup **založený na obsahu** využívá funkce pro uživatele i položky. Uživatelé mohou být popsáni pomocí vlastností, jako je například věk a pohlaví. Položky lze popsat pomocí vlastností, jako je autor a výrobce. Typické příklady systémů doporučení založených na obsahu najdete na sociálních sítích Matchmaking. 
+ **Filtrování spolupráce** používá pouze identifikátory uživatelů a položek. Získá implicitní informace o těchto entitách z matice (zhuštěného) matice hodnocení daných uživateli na položky. O uživateli se můžeme dozvědět z položek, které ohodnotili, a od jiných uživatelů, kteří mají stejné položky.  

Doporučení SVD používá identifikátory uživatelů a položek a matice hodnocení, která jsou dána uživateli pro položky. Je to *doporučení pro spolupráci*. 

Další informace o doporučení SVD najdete v příslušném výzkumném dokumentu: [techniky faktoringu pro doporučované systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Postup konfigurace doporučení SVD pro vlak  

### <a name="prepare-data"></a>Příprava dat

Před použitím modulu musí být vstupní data ve formátu, který očekává model doporučení. Je třeba zadat školicí datovou sadu se třemi uživateli hodnocení položek.

+ První sloupec obsahuje identifikátory uživatelů.
+ Druhý sloupec obsahuje identifikátory položek.
+ Třetí sloupec obsahuje hodnocení páru uživatel-položka. Hodnoty hodnocení musí být číselného typu.  

Datová sada **hodnocení restaurace** v Návrháři Azure Machine Learning (výběr **uložených datových sad** a pak **ukázek**) předvádí očekávaný formát:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Z této ukázky vidíte, že jeden uživatel má hodnocení dvou různých restaurací. 

### <a name="train-the-model"></a>Trénování modelu

1.  Přidejte do kanálu modul doporučeného učení SVD v návrháři a připojte ho k školicím datům.  
   
2.  V poli **počet faktorů**určete počet faktorů, které se mají použít u doporučení.  
    
    Každý faktor měří, kolik uživatelů se s položkou týká. Počet faktorů je také dimenzionální prostor typu latentního faktoru. S rostoucím počtem uživatelů a položek je lepší nastavit větší počet faktorů. Pokud je ale příliš velký počet, může dojít k poklesu výkonu.
    
3.  **Počet iterací algoritmu doporučení** indikuje, kolikrát by měl algoritmus zpracovat vstupní data. Čím vyšší je toto číslo, tím přesnější je předpovědi. Vyšší číslo však znamená pomalejší školení. Výchozí hodnota je 30.

4.  Do pole **rychlost učení**zadejte číslo od 0,0 do 2,0, které definuje velikost kroku pro učení.

    Studijní frekvence určuje velikost kroku v každé iteraci. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než vyhledá nejlepší řešení. 
  
5.  Spuštění kanálu  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro službu Azure Machine Learning. 
