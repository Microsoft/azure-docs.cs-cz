---
title: Ladění hyperparametrů modelů
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Tune Model Hyperparameters v Azure Machine Learning provést parametr sweep na modelu k určení optimální nastavení parametrů.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064164"
---
# <a name="tune-model-hyperparameters"></a>Ladění hyperparametrů modelů

Tento článek popisuje, jak používat modul Tune Model Hyperparameters v návrháři Azure Machine Learning (preview). Cílem je určit optimální hyperparametry pro model strojového učení. Modul vytvoří a testuje více modelů pomocí různých kombinací nastavení. Porovnává metriky ve všech modelech, aby získal kombinace nastavení. 

Termíny *parametr* a *hyperparametr* může být matoucí. *Parametry* modelu jsou to, co nastavíte v pravém podokně modulu. V podstatě tento modul provádí *zatažení parametrů* přes zadané nastavení parametrů. Učí optimální sadu _hyperparameters_, které se mohou lišit pro každý konkrétní rozhodovací strom, datovou sadu nebo regresní metodu. Proces hledání optimální konfigurace se někdy nazývá *ladění*. 

Modul podporuje následující metodu pro nalezení optimálního nastavení pro model: *integrovaný vlak a ladění.* V této metodě nakonfigurujete sadu parametrů, které chcete použít. Potom nechte modul iterate přes více kombinací. Modul měří přesnost, dokud nenajde "nejlepší" model. U většiny modulů studujících si můžete vybrat, které parametry by měly být během tréninkového procesu změněny a které by měly zůstat pevné.

V závislosti na tom, jak dlouho má být proces ladění spuštěn, se můžete rozhodnout vyčerpávajícím způsobem otestovat všechny kombinace. Nebo můžete zkrátit proces vytvořením mřížky kombinací parametrů a testováním randomizované podmnožiny mřížky parametrů.

Tato metoda generuje trénovaný model, který můžete uložit pro opakované použití.  

> [!TIP] 
> Můžete provést související úkol. Než začnete ladit, použijte výběr prvků k určení sloupců nebo proměnných, které mají nejvyšší informační hodnotu.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Jak nakonfigurovat Hyperparametry tune modelu  

Učení optimální hyperparametry pro model strojového učení vyžaduje značné využití kanálů.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Trénování modelu pomocí tažení parametrů  

Tato část popisuje, jak provést základní parametr sweep, který trénuje model pomocí tune model hyperparameters modulu.

1.  Přidejte modul Tune Model Hyperparameters do kanálu v návrháři.

2.  Připojte netrénovaný model k vstupu zcela vlevo. 



4.  Přidejte datovou sadu, kterou chcete použít pro trénování, a připojte ji k prostřednímu vstupu hyperparametrů tune modelu.  

    Volitelně, pokud máte tagovanou datovou sadu, můžete ji připojit k vstupnímu portu zcela vpravo (**Volitelná ověřovací datová sada**). To vám umožní měřit přesnost při tréninku a ladění.

5.  V pravém panelu technologie Naladit model hyperparametrů zvolte hodnotu pro **režim zametací parametry**. Tato možnost určuje způsob výběru parametrů.

    - **Celá mřížka**: Když vyberete tuto možnost, modul se smyčkouje přes mřížku předdefinovanou systémem, aby vyzkoušel různé kombinace a identifikoval nejlepšího studenta. Tato možnost je užitečná, pokud nevíte, jaké by mohlo být nejlepší nastavení parametrů, a chcete vyzkoušet všechny možné kombinace hodnot.

    - **Náhodné tažení**: Když vyberete tuto možnost, modul náhodně vybere hodnoty parametrů v rozsahu definovaném systémem. Je nutné zadat maximální počet spuštění, které má modul provést. Tato možnost je užitečná, pokud chcete zvýšit výkon modelu pomocí metriky podle vašeho výběru, ale stále šetřit výpočetní prostředky.    

6.  Ve **sloupci Popisek**otevřete volič sloupců a zvolte sloupec s jedním popiskem.

7.  Zvolte počet spuštění:

    1. **Maximální počet spuštění při náhodném tažení**: Pokud zvolíte náhodné tažení, můžete určit, kolikrát má být model trénován, pomocí náhodné kombinace hodnot parametrů.

8.  V **části Pořadí**zvolte jednu metriku, která se použije pro hodnocení modelů.

    Když spustíte tažení parametrů, modul vypočítá všechny použitelné metriky pro typ modelu a vrátí je v **sestavě výsledků tažení.** Modul používá samostatné metriky pro regresní a klasifikační modely.

    Vybraná metrika však určuje, jak budou modely seřazeny. Pouze top model, podle pořadí podle zvolené metriky, je výstup jako trénovaný model pro vyhodnocování.

9.  Do **režimu Náhodné osiva**zadejte číslo, které se má použít pro spuštění tažení parametru. 

10. Odešlete potrubí.

## <a name="results-of-hyperparameter-tuning"></a>Výsledky hyperparametrikala

Po dokončení školení:

+ Chcete-li zobrazit sadu metrik přesnosti pro nejlepší model, klepněte pravým tlačítkem myši na modul a vyberte příkaz **Visualize**.

    Výstup zahrnuje všechny metriky přesnosti, které se vztahují k typu modelu, ale metrika, kterou jste vybrali pro hodnocení určuje, který model je považován za "nejlepší".

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.


## <a name="technical-notes"></a>Technické poznámky

Tato část obsahuje podrobnosti implementace a tipy.

### <a name="how-a-parameter-sweep-works"></a>Jak funguje tažení parametrů

Při nastavování tažení parametrů definujete rozsah hledání. Při hledání může být náhodně vybrán konečný počet parametrů. Nebo to může být vyčerpávající vyhledávání přes prostor parametrů, který definujete.

+ **Náhodné tažení**: Tato možnost trénuje model pomocí nastaveného počtu iterací. 

  Zadáte rozsah hodnot, které chcete iterat, a modul použije náhodně vybranou podmnožinu těchto hodnot. Hodnoty jsou vybrány s náhradou, což znamená, že čísla dříve zvolená náhodně nejsou odebrána z fondu dostupných čísel. Takže šance na výběr jakékoli hodnoty zůstává stejná ve všech průchodech.  

+ **Celá mřížka**: Možnost použít celou mřížku znamená, že každá kombinace je testována. Tato možnost je nejdůkladnější, ale vyžaduje nejvíce času. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Řízení délky a složitosti školení

Iterace v mnoha kombinacích nastavení může být časově náročná, takže modul poskytuje několik způsobů, jak omezit proces:

+ Omezte počet iterací použitých k testování modelu.
+ Omezte prostor parametru.
+ Omezte počet iterací i prostor parametru.

Doporučujeme, abyste s nastavením určili nejefektivnější metodu trénování na konkrétní datové sadě a modelu.

### <a name="choosing-an-evaluation-metric"></a>Výběr metriky hodnocení

Na konci testování model zobrazí sestavu, která obsahuje přesnost pro každý model, takže si můžete prohlédnout výsledky metriky:

- Jednotná sada metrik se používá pro všechny binární klasifikační modely.
- Přesnost se používá pro všechny vícetřídní klasifikační modely.
- Pro regresní modely se používá jiná sada metrik. 

Během školení však musíte zvolit *jednu* metriku, která se použije při hodnocení modelů, které jsou generovány během procesu ladění. Možná zjistíte, že nejlepší metrika se liší v závislosti na vašem obchodním problému a ceně falešných poplachů a falešných negativů.

#### <a name="metrics-used-for-binary-classification"></a>Metriky používané pro binární klasifikaci

-   **Přesnost** je podíl skutečných výsledků na celkových případech.  

-   **Přesnost** je podíl skutečných výsledků na pozitivních výsledcích.  

-   **Odvolání** je zlomek všech správných výsledků nad všemi výsledky.  

-   **F-skóre** je míra, která vyvažuje přesnost a odvolání.  

-   **AUC** je hodnota, která představuje oblast pod křivkou, když jsou falešně pozitivní výsledky vykresleny na ose x a skutečné pozitivy jsou vykresleny na ose y.  

-   **Průměrná ztráta protokolu** je rozdíl mezi dvěma rozděleními pravděpodobnosti: true one a jeden v modelu.  

#### <a name="metrics-used-for-regression"></a>Metriky používané pro regresi

-   **Průměrná absolutní chyba** zprůměruje všechny chyby v modelu, kde *chyba* znamená vzdálenost předpokládané hodnoty od skutečné hodnoty. Často se zkracuje jako *MAE*.  

-   **Kořen střední kvadratických chyb** měří průměr druhých mocnin chyb a pak převezme kořen této hodnoty. To je často zkráceně *RMSE*.  

-   **Relativní absolutní chyba** představuje chybu jako procento skutečné hodnoty.  

-   **Relativní kvadratická chyba** normalizuje celkovou kvadračnou chybu vydělením celkovou kvadratou chybou předpovídaných hodnot.  

-   **Koeficient stanovení** je jediné číslo, které označuje, jak dobře data odpovídají modelu. Hodnota jednoho znamená, že model přesně odpovídá datům. Nulová hodnota znamená, že data jsou náhodná nebo jinak nemohou být vhodná pro model. To je často *nazýváno r<sup>2</sup>*, *R<sup>2</sup>*, nebo *r-squared*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduly, které nepodporují zametat parametr

Téměř všichni studenti v Azure Machine Learning podporují křížové ověřování pomocí integrovaného zametání parametrů, které vám umožní vybrat parametry, se kterými se chcete s nimi datovat. Pokud žák nepodporuje nastavení rozsahu hodnot, můžete jej stále používat při křížovém ověřování. V tomto případě je pro tažení vybrán rozsah povolených hodnot. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

