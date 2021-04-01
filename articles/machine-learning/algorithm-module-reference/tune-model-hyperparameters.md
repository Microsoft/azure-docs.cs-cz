---
title: Ladění hyperparametrů modelů
titleSuffix: Azure Machine Learning
description: K optimalizaci parametrů Hyper-v můžete použít modul ladění modelu ladění v návrháři.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420627"
---
# <a name="tune-model-hyperparameters"></a>Ladění hyperparametrů modelů

Tento článek popisuje, jak v Návrháři Azure Machine Learning použít modul předparametrů ladění modelu. Cílem je určit optimální parametry pro model strojového učení. Modul vytváří a testuje více modelů pomocí různých kombinací nastavení. Porovnává metriky pro všechny modely a získá kombinace nastavení. 

*Parametr* terms a *parametr* může být matoucí. *Parametry* modelu jsou ty, které jste nastavili v pravém podokně modulu. V podstatě tento modul provádí v rámci zadaného nastavení parametrů *parametr Sweep* . Učí optimální sadu _parametrů_, které se mohou lišit pro každý konkrétní rozhodovací strom, datovou sadu nebo regresi. Proces vyhledávání optimální konfigurace se někdy označuje jako *ladění*. 

Modul podporuje následující metodu pro nalezení optimálního nastavení modelu: *integrovaný vlak a ladění.* V této metodě nakonfigurujete sadu parametrů, které se mají použít. Pak se modul umožní iterovat více kombinacemi. Modul měří přesnost, dokud nenajde "nejlepší" model. U většiny modulů učí se můžete rozhodnout, jaké parametry se mají změnit během procesu školení a které by měly zůstat pevné.

V závislosti na tom, jak dlouho chcete proces ladění spustit, se můžete rozhodnout, že všechny kombinace vyzkoušíte. Nebo můžete proces zkrátit tak, že vytvoříte mřížku kombinací parametrů a otestujete náhodnou podmnožinu pro mřížku parametrů.

Tato metoda generuje školený model, který lze uložit pro opakované použití.  

> [!TIP] 
> Můžete provést související úlohu. Než začnete s laděním, použijte výběr funkcí a určete sloupce nebo proměnné, které mají nejvyšší hodnotu informace.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak nakonfigurovat parametry modelu ladění  

Výuka optimálních parametrů pro model strojového učení vyžaduje výrazné použití kanálů.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Výuka modelu pomocí úklidu parametrů  

Tato část popisuje, jak provést základní úklid parametrů, který navlakuje model pomocí modulu ladění modelu předparametrů.

1.  Do kanálu v Návrháři přidejte modul parametry modelu ladění.

2.  Připojit nevlakový model ke vstupu vlevo. 

    > [!NOTE] 
    > **Laditelné parametry modelu** se můžou připojovat jenom k vestavěným modulům algoritmu strojového učení a nemůžou podporovat vlastní model sestavený v **Vytvoření modelu Pythonu**.


3.  Přidejte datovou sadu, kterou chcete použít pro školení, a připojte ji ke střednímu vstupu parametrů modelu ladění.  

    Pokud máte označenou datovou sadu, můžete ji připojit ke vstupnímu portu vpravo od sebe (**volitelná datová sada ověření**). To vám umožní měřit přesnost při výuce a ladění.

4.  V pravém panelu modelových parametrů ladění vyberte hodnotu pro **režim mazání parametrů**. Tato možnost určuje, jak jsou vybrány parametry.

    - **Celá mřížka**: Když vyberete tuto možnost, modul se cyklicky přeskočí do mřížky předdefinovaných systémem, aby vyzkoušel různé kombinace a identifikovala nejlepší informace. Tato možnost je užitečná v případě, že nevíte, co nejlepší nastavení parametrů může být a chcete vyzkoušet všechny možné kombinace hodnot.

    - **Náhodné čištění**: Když vyberete tuto možnost, modul náhodně vybere hodnoty parametrů v rozsahu definovaném systémem. Je nutné zadat maximální počet spuštění, které má modul spustit. Tato možnost je užitečná, když chcete zvýšit výkon modelu pomocí metrik dle vašeho výběru, ale přesto šetřit výpočetní prostředky.    

5.  V případě **sloupce popisek** Otevřete selektor sloupců a vyberte sloupec s jedním popiskem.

6.  Vyberte počet spuštění:

    - **Maximální počet spuštění na náhodném čištění**: Pokud zvolíte náhodné rozčištění, můžete určit, kolikrát by měl být model vyškolený, pomocí náhodné kombinace hodnot parametrů.

7.  Pro **hodnocení** vyberte jednu metriku, která se má použít pro řazení modelů.

    Když spustíte parametr Sweep, modul vypočítá všechny použitelné metriky pro typ modelu a vrátí je do sestavy **výsledků pro Shrnutí** . Modul používá pro regresní a klasifikační modely samostatné metriky.

    Metrika, kterou zvolíte, však Určuje, jak jsou modely seřazeny. Pouze model nejvyšší úrovně, jak je zvolená metrika, je výstupem jako trained model, který se má použít pro bodování.

8.  V případě **náhodného osazení** zadejte číslo, které se má použít ke spuštění čištění parametru. 

9. Odešlete kanál.

## <a name="results-of-hyperparameter-tuning"></a>Výsledky ladění parametrů

Po dokončení školení:

+ Pokud chcete zobrazit výsledky Sweep, můžete buď kliknout pravým tlačítkem na modul a pak vybrat **vizualizovat**, nebo kliknout pravým tlačítkem myši na levý výstupní port modulu k vizualizaci.

    **Výsledky Sweep** obsahují všechny parametry a metriky s přesností, které se vztahují na typ modelu a metrika, kterou jste vybrali pro řazení, určuje, který model se považuje za "nejlepší".

+ Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy + protokoly** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.


## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobné informace o implementaci a tipy.

### <a name="how-a-parameter-sweep-works"></a>Jak funguje úklid parametrů

Při nastavování parametru Sweep můžete definovat rozsah hledání. Hledání může používat konečný počet parametrů náhodně. Nebo se může jednat o důkladné vyhledávání v rámci definovaného prostoru parametru.

+ **Náhodné čištění**: Tato možnost nastavila model pomocí nastaveného počtu iterací. 

  Zadáte rozsah hodnot, které se mají iterovat, a modul používá náhodně vybranou podmnožinu těchto hodnot. Hodnoty se volí s náhradou, což znamená, že čísla dříve vybraná náhodně nejsou odebrána z fondu dostupných čísel. Takže možnost výběru jakékoli hodnoty zůstane v rámci všech průchodů stejná.  

+ **Celá mřížka**: možnost použití celé mřížky znamená, že každá kombinace je testována. Tato možnost je nejdůkladnější, ale vyžaduje nejvíce času. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Řízení délky a složitosti školení

Iterace nad mnoha kombinacemi nastavení může být časově náročná, takže modul nabízí několik způsobů, jak omezit proces:

+ Omezte počet iterací použitých k otestování modelu.
+ Omezte prostor parametru.
+ Omezte počet iterací i prostor parametru.

Doporučujeme vytvořit kanál s nastavením, abyste zjistili nejúčinnější způsob školení konkrétní datové sady a modelu.

### <a name="choosing-an-evaluation-metric"></a>Výběr metriky vyhodnocení

Na konci testování model prezentuje sestavu, která obsahuje přesnost pro každý model, abyste mohli zkontrolovat výsledky metrik:

- Pro všechny binární klasifikace modelů se používá jednotná sada metrik.
- Pro všechny modely klasifikace s více třídami se používá přesnost.
- Pro regresní modely se používá jiná sada metrik. 

Během školení ale musíte zvolit *jednu* metriku, která se použije v seřazení modelů generovaných během procesu optimalizace. Může se stát, že se nejlepší metrika liší v závislosti na vašem obchodním problému a na náklady na falešně pozitivní a falešně negativní.

#### <a name="metrics-used-for-binary-classification"></a>Metriky používané pro binární klasifikaci

-   **Přesnost** je poměr skutečných výsledků do celkového počtu případů.  

-   **Přesnost** je poměr skutečných výsledků k pozitivním výsledkům.  

-   **Odvolání** je zlomek všech správných výsledků pro všechny výsledky.  

-   **F-skore** je míra, která vyvažuje přesnost a odvolání.  

-   **AUC** je hodnota, která představuje oblast pod křivkou, pokud jsou na ose x vykresleny falešně pozitivní hodnoty a na ose y jsou zobrazeny kladné hodnoty.  

-   **Průměrná ztráta protokolu** je rozdíl mezi dvěma distribucí pravděpodobnosti: hodnota true a ta v modelu.  

#### <a name="metrics-used-for-regression"></a>Metriky používané pro regresi

-   **Střední absolutní chyba** vypočítá průměr všech chyb v modelu, kde *Chyba* znamená vzdálenost předpovězené hodnoty od hodnoty true. Často se zkracuje jako *Mae*.  

-   **Kořen průměrného čtvercového chyb** měří průměr čtverců chyb a pak získá kořen této hodnoty. Často se zkracuje jako *RMSE*.  

-   **Relativní absolutní chyba** představuje chybu jako procento hodnoty true.  

-   **Relativní kvadratická chyba** normalizuje celkový počet kvadratických chyb tak, že se vydělí celkovými čtvercovými chybami předpokládaných hodnot.  

-   **Koeficientem stanovitelnosti** je jedno číslo, které určuje, jak dobře data vyhovují modelu. Hodnota One znamená, že model přesně odpovídá datům. Hodnota nula znamená, že data jsou náhodná nebo jinak nelze přizpůsobit modelu. Často se označuje jako *r <sup>2</sup>*, *r <sup>2</sup>* nebo *r-kvadrát*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduly, které nepodporují Sweep parametrů

Téměř všichni učí v Azure Machine Learning podporují křížové ověřování s integrovaným vyčištěním parametrů, které vám umožní vybrat parametry pro kanál s. Pokud se naučíte nastavit rozsah hodnot, můžete ho i nadále používat při křížovém ověřování. V tomto případě je pro oblast oblouku vybraná Rozsah povolených hodnot. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

