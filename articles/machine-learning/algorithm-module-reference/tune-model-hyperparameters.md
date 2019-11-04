---
title: Ladit parametry modelu
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul předparametrů ladění modelu ve službě Azure Machine Learning, aby se pro určení optimálního nastavení parametrů provádělo mazání parametrů v modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515652"
---
# <a name="tune-model-hyperparameters"></a>Ladit parametry modelu

Tento článek popisuje, jak použít modul [ladění modelu ladění](tune-model-hyperparameters.md) v Návrháři Azure Machine Learning (Preview) k určení optimálních parametrů pro daný model strojového učení. Modul sestaví a testuje více modelů pomocí různých kombinací nastavení a porovnává metriky pro všechny modely a získá tak kombinaci nastavení. 

*Parametr* terms a *parametr* může být matoucí. *Parametry* modelu jsou ty, které jste nastavili v podokně Vlastnosti. V podstatě tento modul provádí překládání *parametrů* v rámci zadaného nastavení parametrů a učí optimální sadu _parametrů_, které se mohou lišit pro každý konkrétní rozhodovací strom, datovou sadu nebo regresi. Proces vyhledávání optimální konfigurace se někdy označuje jako *ladění*. 

Modul podporuje následující metodu pro nalezení optimálního nastavení modelu. 

**Integrovaná výuka a ladění**: nakonfigurujete sadu parametrů, které se mají použít, a potom nechte modul iterovat více kombinací a přesnost měření, dokud nenajde "nejlepší" model. U většiny modulů učí se můžete rozhodnout, jaké parametry se mají změnit během procesu školení a které by měly zůstat pevné.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Tato metoda generuje školený model, který lze uložit pro opakované použití.  

### <a name="related-tasks"></a>Související úlohy

+ Než začnete s laděním, použijte výběr funkcí a určete sloupce nebo proměnné, které mají nejvyšší hodnotu informace.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak nakonfigurovat parametry modelu ladění  

Obecně se naučíte, že optimální parametry pro daný model strojového učení vyžadují výrazné vytvoření kanálu.

### <a name="train-a-model-using-a-parameter-sweep"></a>Výuka modelu pomocí úklidu parametrů  

Tato část popisuje, jak provést základní úklid parametrů, který navlakuje model pomocí modulu [ladění modelu předparametrů](tune-model-hyperparameters.md) .

1.  Do kanálu v Návrháři přidejte modul [parametry modelu ladění](tune-model-hyperparameters.md) .

2.  Připojit nevlakový model ke vstupu vlevo. 

3. Nastavte možnost **vytvořit režim Trainer** na **rozsah parametrů** a pomocí **Tvůrce rozsahu** určete rozsah hodnot, které se mají použít v Sweep parametru.  

    Téměř všechny moduly klasifikace a regrese podporují režim úklidu integrovaných parametrů. Pro ty, kteří nepodporují konfiguraci rozsahu parametrů, lze testovat pouze dostupné hodnoty parametrů.

    Můžete ručně nastavit hodnotu pro jeden nebo více parametrů a potom vymezit zbývající parametry. To může nějaký čas ušetřit.

4.  Přidejte datovou sadu, kterou chcete použít pro školení, a připojte ji ke střednímu vstupu [parametrů modelu ladění](tune-model-hyperparameters.md).  

    Pokud máte označenou datovou sadu, můžete ji připojit ke vstupnímu portu vpravo od sebe (**volitelná datová sada ověření**). To vám umožní měřit přesnost při výuce a ladění.

5.  V podokně **vlastnosti** u [parametrů modelu ladění](tune-model-hyperparameters.md)vyberte hodnotu pro **režim mazání parametrů**. Tato možnost určuje, jak jsou vybrány parametry.

    - **Celá mřížka**: Když vyberete tuto možnost, modul se cyklicky přeskočí do mřížky předdefinovaných systémem, aby vyzkoušel různé kombinace a identifikovala nejlepší informace. Tato možnost je užitečná v případech, kdy nevíte, co nejlepší nastavení parametrů může být a chcete vyzkoušet všechny možné kombinace hodnot.

    - **Náhodné čištění**: Když vyberete tuto možnost, modul náhodně vybere hodnoty parametrů v rozsahu definovaném systémem. Je nutné zadat maximální počet spuštění, které má modul spustit. Tato možnost je užitečná v případech, kdy chcete zvýšit výkon modelu pomocí metrik dle vašeho výběru, ale přesto šetřit výpočetní prostředky.

    Pokud zvolíte náhodné shrnutí, můžete zadat **maximální počet spuštění na náhodném**vynechání, což znamená, kolikrát by měl být model vyškolený, pomocí náhodné kombinace hodnot parametrů.

6.  U **sloupce popisek**Otevřete selektor sloupců a vyberte sloupec s jedním popiskem.

7.  **Maximální počet spuštění na náhodném čištění**: Pokud zvolíte náhodné rozčištění, můžete určit, kolikrát by měl být model vyškolený, pomocí náhodné kombinace hodnot parametrů.

    **Maximální počet spuštění v náhodné mřížce**: Tato možnost také určuje počet iterací v případě náhodného vzorkování hodnot parametrů, ale hodnoty nejsou vygenerované náhodně ze zadaného rozsahu. místo toho se vytvoří matice se všemi možnými kombinacemi hodnot parametrů a náhodným vzorkováním se vezme v matici. Tato metoda je efektivnější a méně náchylná k regionálnímu převzorkování nebo odvzorkování.

8.  Vyberte jednu metriku, která se použije při **seřazení** modelů.

    Když spustíte mazání parametrů, vypočítávají se všechny použitelné metriky pro daný typ modelu a vrátí se do sestavy **výsledků pro Shrnutí** . Pro regresní a klasifikační modely se používají samostatné metriky.

    Metrika, kterou zvolíte, však Určuje, jak jsou modely seřazeny. Pouze model nejvyšší úrovně, jak je zvolená metrika, je výstupem jako trained model, který se má použít pro bodování.

9.  V případě **náhodného osazení**zadejte číslo, které se má použít při inicializaci mazání parametru. 

10. Spuštění kanálu

## <a name="results-of-hyperparameter-tuning"></a>Výsledky ladění parametrů

Po dokončení školení:

+ Pokud chcete zobrazit sadu metrik přesnosti pro nejlepší model, klikněte pravým tlačítkem myši na modul, vyberte možnost **výsledky čištění**a pak vyberte **vizualizovat**.

    Všechny metriky přesnosti použitelné pro typ modelu jsou výstup, ale metrika, kterou jste vybrali pro řazení, určuje, který model se považuje za "nejlepší".

+ Pokud chcete model použít pro bodování v jiných kanálech, aniž byste museli opakovat proces ladění, klikněte pravým tlačítkem na výstup modelu a vyberte **Uložit jako trained model**. 


## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="how-a-parameter-sweep-works"></a>Jak funguje úklid parametrů

Tato část popisuje, jak funkce mazání parametrů funguje obecně, a jak se možnosti v tomto modulu pracují.

Při nastavování parametru Sweep definujete rozsah hledání, chcete-li použít buď konečný počet parametrů, který je vybrán náhodně, nebo podrobné vyhledávání nad definovaným prostorem parametrů.

+ **Náhodné čištění**: Tato možnost nastavila model pomocí nastaveného počtu iterací. 

     Zadáte rozsah hodnot, které se mají iterovat, a modul používá náhodně vybranou podmnožinu těchto hodnot.  Hodnoty se volí s náhradou, což znamená, že čísla dříve vybraná náhodně nejsou odebrána z fondu dostupných čísel. Proto možnost výběru libovolné hodnoty zůstane stejná napříč všemi průchody.  

+ **Celá mřížka**: možnost použití celé mřížky znamená pouze to, že každá kombinace je testována. Tato možnost se dá považovat za nejdůkladnější, ale vyžaduje to nejvíce času. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Řízení délky a složitosti školení

Iterace nad mnoha kombinacemi nastavení může být časově náročná, takže modul nabízí několik způsobů, jak omezit proces:

+ Omezení počtu iterací použitých k otestování modelu
+ Omezení prostoru parametrů
+ Omezte počet iterací i prostor parametru.

Doporučujeme vytvořit kanál s nastavením, abyste zjistili nejúčinnější způsob školení konkrétní datové sady a modelu.

### <a name="choosing-an-evaluation-metric"></a>Výběr metriky vyhodnocení

Sestava obsahující přesnost pro každý model je prezentována na konci, takže můžete zkontrolovat výsledky metriky. Pro všechny binární klasifikace modelů se používá jednotná sada metrik. pro všechny modely klasifikace s více třídami se používá přesnost a pro regresní modely se používá jiná sada metrik. Během školení ale musíte zvolit **jednu** metriku, která se použije v seřazení modelů generovaných během procesu optimalizace. Může se stát, že se nejlepší metrika liší v závislosti na vašem obchodním problému a na náklady na falešně pozitivní a falešně negativní.

#### <a name="metrics-used-for-binary-classification"></a>Metriky používané pro binární klasifikaci

-   **Přesnost** Poměr skutečných výsledků do celkového počtu případů.  

-   **Přesnost** Poměr skutečných výsledků k pozitivním výsledkům.  

-   **Odvolání** Zlomek všech správných výsledků u všech výsledků.  

-   **Skóre F** Míra, která vyvažuje přesnost a odvolání.  

-   **AUC** Hodnota, která představuje oblast pod křivkou, pokud jsou na ose x vykresleny falešně pozitivní hodnoty a na ose y jsou zobrazeny kladné hodnoty.  

-   **Průměrná ztráta protokolu** Rozdíl mezi dvěma distribucí pravděpodobnosti: hodnota true a ta v modelu.  

#### <a name="metrics-used-for-regression"></a>Metriky používané pro regresi

-   **Střední absolutní chyba** vypočítá průměrnou chybu v modelu, kde chyba znamená vzdálenost předpovězené hodnoty od hodnoty true. Často se zkracuje jako **Mae**.  

-   **Kořen průměrného čtvercového chyb** měří průměr čtverců chyb a pak získá kořen této hodnoty. Často zkráceně jako **RMSE**  

-   **Relativní absolutní chyba** představuje chybu jako procento hodnoty true.  

-   **Relativní kvadratická chyba** normalizuje celkový počet kvadratických chyb tím, že vydělí celkový počet kvadratických hodnot.  

-   **Koeficient určení** Jedno číslo, které určuje, jak dobře data vyhovují modelu. Hodnota One znamená, že model přesně odpovídá datům; hodnota nula znamená, že data jsou náhodná nebo jinak nelze přizpůsobit modelu. Často se označují jako **r<sup>2</sup>** , **r<sup>2</sup>** nebo **r-kvadrát**.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Moduly, které nepodporují čištění parametrů

Téměř všichni učí v Azure Machine Learning podporují křížové ověřování s integrovaným vyčištěním parametrů, které vám umožní vybrat parametry pro kanál s. Pokud se naučíte nastavit rozsah hodnot, můžete ho i nadále používat při křížovém ověřování. V tomto případě je pro oblast oblouku vybraná určitá Rozsah povolených hodnot. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 

