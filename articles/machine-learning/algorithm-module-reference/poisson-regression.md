---
title: 'Poissonova regrese: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul Poissonova regrese v Návrháři Azure Machine Learning k vytvoření Poissonova regresního modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420695"
---
# <a name="poisson-regression"></a>Poissonova regrese

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Tento modul slouží k vytvoření Poissonova regresního modelu v kanálu. Funkce POISSON regrese je určena pro předpověď číselných hodnot, obvykle počítá. Proto byste měli použít tento modul k vytvoření regresního modelu pouze v případě, že hodnoty, které se pokoušíte odhadnout, odpovídají následujícím podmínkám:

- Proměnná odpovědi má [Poissonova rozdělení](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Počet nemůže být záporný. Pokud se pokusíte použít s negativními popisky, metoda se nezdařila.

- Poissonova rozdělení je diskrétní distribuce; proto nemá smysl použít tuto metodu s neúplnými čísly.

> [!TIP]
> Pokud Váš cíl není počet, je pravděpodobné, že funkce POISSON regrese není vhodnou metodou. Vyzkoušejte [v Návrháři jiné moduly regrese](./module-reference.md#machine-learning-algorithms). 

Po nastavení metody regrese je nutné proškolit model pomocí datové sady obsahující příklady hodnoty, kterou chcete předpovědět. K vytvoření předpovědi se pak dá použít trained model.

## <a name="more-about-poisson-regression"></a>Další informace o Poissonova regresi

Poissonova regrese je speciální typ regresní analýzy, který se obvykle používá pro počty modelů. Například funkce POISSON regrese by byla užitečná v těchto scénářích:

- Modelování počtu studených přidružených k letů v letadle

- Odhad počtu volání záchranné služby během události

- Projektování počtu dotazů zákazníků následně po povýšení

- Vytváření pohotovostních tabulek

Vzhledem k tomu, že proměnná Response má Poissonova rozdělení, model vytváří různé předpoklady pro data a rozdělení pravděpodobnosti, než je například regrese s minimálními čtverci. Poissonova modely by proto měly být interpretovány jinak než u ostatních regresních modelů.

## <a name="how-to-configure-poisson-regression"></a>Jak nakonfigurovat funkce POISSON regrese

1. Přidejte modul **Poissonova regrese** do vašeho kanálu v návrháři. Tento modul můžete najít v části **Machine Learning algoritmy** v kategorii **regrese** .

2. Přidejte datovou sadu, která obsahuje školicí data správného typu. 

    Doporučujeme použít [normalizovaná data](normalize-data.md) k normalizaci vstupní datové sady před jejím použitím ke školení regresor.

3. V pravém podokně modulu **Poissonova regrese** určete, jak chcete, aby byl model vyškolený, nastavením možnosti **vytvořit Trainer režim** .  
  
    - **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, poskytněte konkrétní sadu hodnot jako argumenty.
  
    - **Rozsah parametrů**: Pokud si nejste jistí, co nejlépe vyhovuje parametrům, udělejte s použitím modulu [ladění modelu](tune-model-hyperparameters.md) modulů parametrů. Trainer projde více hodnot, které zadáte, abyste našli optimální konfiguraci.
  
4. **Tolerance optimalizace**: zadejte hodnotu, která definuje interval tolerance během optimalizace. Čím nižší hodnota, tím pomalejší a přesnější přizpůsobení.

5. **Váha pravidelného využití L1** a **váha pro pravidelnosti L2**: hodnoty typu, které se mají použít pro práci s L1 a L2. Při provádění se *přidávají omezení* algoritmu týkající se aspektů modelu, které jsou nezávisle na školicích datech. Často se používá k tomu, aby se zabránilo přebudování. 

    - Pravidelnáace L1 je užitečná v případě, že cílem je mít model, který je co možná zhuštěný.

        Pravidelná práce s L1 je prováděna odečtením váhy pro daný vektor váhy od výrazu ztráty, který se snaží minimalizovat. Norma L1 je dobrým odhadem pro normu L0, což je počet nenulových souřadnic.

    - Pravidelná navýšení L2 brání v tom, aby se jedna souřadnice ve velikosti vektoru rozrůsta příliš daleko. Pravidelné vyvážení L2 je užitečné, pokud cílem je model s malým celkovým hmotností.

    V tomto modulu můžete použít kombinaci L1 a L2 regularizations. Kombinováním L1 a L2 můžete na základě velikosti hodnot parametrů ukládat pokutu. Tento nástroj se snaží minimalizovat pokutu při kompromisech s minimalizací ztráty.

    Dobré diskuzi o L1 a L2 najdete v tématu [L1 a L2 pro Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Velikost paměti pro L-BFGS**: zadejte velikost paměti, která se má vyhradit pro přizpůsobení modelu a optimalizaci.

     L-BFGS je specifická metoda pro optimalizaci založená na algoritmu Broyden – Fletcher – Goldfarb-Shanno (BFGS). Metoda používá k výpočtu směru dalšího kroku omezené množství paměti (L).

     Změnou tohoto parametru můžete ovlivnit počet minulých pozic a přechodů, které jsou uloženy pro výpočet dalšího kroku.

7. Připojte datovou sadu školení a nevýukový model k jednomu z školicích modulů: 

    - Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .

    - Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, použijte modul [ladit model s parametry](tune-model-hyperparameters.md) .

    > [!WARNING]
    > 
    > - Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se pouze první hodnota v seznamu rozsah parametrů.
    > 
    > - Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.
    > 
    > - Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

8.  Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek školicího modelu, vyberte modul školení a pak přepněte na kartu **výstupy + protokoly** v pravém panelu. Klikněte na ikonu pro **datovou sadu registru**.  Uložený model můžete najít jako modul ve stromu modulu. 

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.