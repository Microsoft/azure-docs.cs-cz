---
title: 'Doporučení pro výukový SVD: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul SVD, který je doporučený pro výuku ve službě Azure Machine Learning, ke školení bayesovského rozhodování doporučení pomocí algoritmu SVD.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3b86d77470a4f3d4fe5b005e562a8adae21f8bc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515665"
---
# <a name="train-svd-recommender"></a>Doporučení pro výuku SVD

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul **doporučených postupů pro vlak SVD** (Preview). Tento modul použijte k vytvoření výukového modelu doporučení založeného na algoritmu **SVD** (s jednou hodnotou dekompozice).  

Modul **doporučení SVDu pro vlaky** přečte datovou sadu trojí pro hodnocení položek. Vrátí SVD doporučení. Potom můžete pomocí vyškolený model předpovědět hodnocení nebo vygenerovat doporučení pomocí modulu [SVD doporučeného skóre](score-svd-recommender.md) .  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Další informace o modelech doporučení a doporučení SVD  

Hlavním cílem systému doporučení je doporučit *uživatelům* systému jednu nebo více *položek* . Příkladem položky může být film, restaurace, kniha nebo písně. Uživatel může být osoba, skupina osob nebo jiná entita s preferencemi položek.  

Existují dva hlavní přístupy ke doporučeným systémům. 

+ Prvním je přístup **založený na obsahu** , který využívá funkce pro uživatele a položky. Uživatelé mohou být popsáni pomocí vlastností, jako jsou věk a pohlaví, a položky mohou být popsány pomocí vlastností, jako je autor a výrobce. Typické příklady systémů doporučení založených na obsahu najdete na sociálních sítích Matchmaking. 
+ Druhý přístup je **filtrování spolupráce**, které používá pouze identifikátory uživatelů a položek a získává implicitní informace o těchto entitách z matice (zhuštěného) matice hodnocení, které jsou dány uživateli k položkám. Můžeme se dozvědět o uživateli z položek, které ohodnotili, a od jiných uživatelů, kteří mají stejné položky.  

Doporučení SVD používá identifikátory uživatelů a položek a matice hodnocení, která jsou dána uživateli pro položky. Je to **doporučení pro spolupráci**. 

Další informace o doporučení SVD najdete v příslušném výzkumném dokumentu: [techniky faktoringu pro doporučované systémy](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Postup konfigurace doporučení SVD pro vlak  

+ [Příprava školicích dat](#prepare-data)
+ [Trénování modelu](#train-the-model)

### <a name="prepare-data"></a>Příprava dat

Předtím, než se pokusíte použít modul, je nezbytné, aby vaše data byla ve formátu očekávaném modelem doporučení. Je třeba zadat školicí datovou sadu se **třemi uživateli hodnocení položek** .

#### <a name="required-dataset-of-user-item-ratings"></a>Požadovaná datová sada – hodnocení položek uživatelů

Je důležité, aby vstupní data použitá pro školení obsahovala správný typ dat ve správném formátu: 

+ První sloupec musí obsahovat identifikátory uživatele.
+ Druhý sloupec musí obsahovat identifikátory položek.
+ Třetí sloupec obsahuje hodnocení páru uživatel-položka. Hodnoty hodnocení musí být číselného typu.  

                                                                                                                                                                                                           

Datová sada **hodnocení restaurace** v Návrháři Azure Machine Learning (klikněte na **uložené datové sady** a pak na **ukázky**) předvádí očekávaný formát:

|UserID|placeID|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Z této ukázky vidíte, že jeden uživatel má hodnocení dvou různých restaurací. 

### <a name="train-the-model"></a>Trénování modelu

1.  Přidejte do kanálu modul **doporučeného učení SVD** v návrháři a připojte ho k školicím datům.  
   
2.  V poli **počet faktorů**zadejte číslo určující počet faktorů, které se mají použít s doporučeným nástrojem.  
    
    Každý faktor měří, kolik uživatele s položkou souvisí. Počet faktorů je také dimenzionální prostor typu latentního faktoru. S počtem zvýšení počtu uživatelů a položek je lepší nastavit větší počet faktorů. Pokud je ale příliš velký počet, může výkon snížit.
    
3.  **Počet iterací algoritmu doporučení**určuje, kolikrát by měl algoritmus zpracovat vstupní data. Čím vyšší je toto číslo, tím přesnější je předpovědi; školení je však pomalejší. Výchozí hodnota je 30.

4.  Do pole **rychlost učení**zadejte číslo mezi 0,0 a 2,0, které definuje velikost kroku při učení.

    Studijní frekvence určuje velikost kroku provedeného v každé iteraci. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení. 
  
5.  Spuštění kanálu  


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
