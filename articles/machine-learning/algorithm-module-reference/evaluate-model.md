---
title: 'Vyhodnocení modelu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul vyhodnocení modelu v Azure Machine Learning k měření přesnosti vyučeného modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: ddc6a35d5959a2d1f85c151a6f229223db9ba566
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497914"
---
# <a name="evaluate-model-module"></a>Vyhodnotit modul modelu

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul použijte k měření přesnosti trained model. Poskytnete datovou sadu obsahující skóre vygenerované z modelu a modul **vyhodnocení modelu** vypočítá sadu standardních metrik vyhodnocení v oboru.
  
 Metriky vrácené **modelem vyhodnocení** závisí na typu modelu, který vyhodnocujete:  
  
-   **Modely klasifikace**    
-   **Regresní modely**    



> [!TIP]
> Pokud s hodnocením modelu začínáte, doporučujeme, aby se série videí Dr. Stephen Elston jako součást [strojového učení](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) od EdX. 


Existují tři způsoby použití modulu **vyhodnocení modelu** :

+ Vygenerujte skóre z vašich školicích dat a vyhodnoťte model na základě těchto skóre.
+ Generování skóre modelu, ale porovnání těchto skóre s skóre na rezervované sadě testů
+ Porovnání skóre dvou různých, ale souvisejících modelů, pomocí stejné sady dat

## <a name="use-the-training-data"></a>Použití školicích dat

Pro vyhodnocení modelu musíte připojit datovou sadu, která obsahuje sadu vstupních sloupců a skóre.  Pokud nejsou k dispozici žádná jiná data, můžete použít původní datovou sadu.

1. Připojte výstup skóre výsledné **sady dat** [modelu skóre](./score-model.md) k vstupu **modelu vyhodnocení**. 
2. Klikněte na **vyhodnotit modul modelu** a spuštěním kanálu vygenerujte skóre vyhodnocení.

## <a name="use-testing-data"></a>Použití testovacích dat

Běžným scénářem ve službě Machine Learning je oddělení původní sady dat o školení a testování datových sad, použití [rozděleného](./split-data.md) modulu nebo [oddílu a vzorového](./partition-and-sample.md) modulu. 

1. Připojte výstup skóre výsledné **sady dat** [modelu skóre](score-model.md) k vstupu **modelu vyhodnocení**. 
2. Připojte výstup modulu rozdělených dat, který obsahuje testovací data, do pravého vstupu pro **vyhodnocení modelu**.
2. Klikněte na **vyhodnotit modul modelu** a vyberte **Spustit vybrané** pro vygenerování skóre vyhodnocení.

## <a name="compare-scores-from-two-models"></a>Porovnání skóre ze dvou modelů

K **vyhodnocení modelu**můžete také připojit druhou sadu výsledků.  Skóre můžou být sdílenou zkušební sadou, která má známé výsledky, nebo sadu výsledků z jiného modelu pro stejná data.

Tato funkce je užitečná, protože můžete snadno porovnat výsledky dvou různých modelů se stejnými daty. Nebo můžete porovnat skóre ze dvou různých spuštění přes stejná data s různými parametry.

1. Připojte výstup skóre výsledné **sady dat** [modelu skóre](score-model.md) k vstupu **modelu vyhodnocení**. 
2. Připojte výstup modulu určení skóre modelu pro druhý model na pravého vstupu **modelu vyhodnocení**.
3. Klikněte pravým tlačítkem na **vyhodnotit model**a vyberte **Spustit vybrané** a vygenerujte hodnocení vyhodnocení.

## <a name="results"></a>Výsledky

Po spuštění **modelu vyhodnocení**klikněte pravým tlačítkem myši na modul a výběrem **výsledků vyhodnocení** zobrazte výsledky. Můžete:

+ Uložte výsledky jako datovou sadu pro snazší analýzu pomocí dalších nástrojů.
+ Generování vizualizace v Návrháři

Pokud připojíte datové sady ke vstupům **modelu vyhodnocení**, budou výsledky obsahovat metriky pro sadu dat nebo oba modely.
Model nebo data připojená k levému portu se zobrazí jako první v sestavě, za kterými následuje metrika pro datovou sadu nebo model připojený ke správnému portu.  

Například následující obrázek představuje porovnání výsledků ze dvou modelů clusteringu, které byly vytvořeny na stejných datech, ale s různými parametry.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Vzhledem k tomu, že se jedná o model clusteringu, výsledky hodnocení jsou jiné než v porovnání s výsledky ze dvou regresních modelů nebo porovnáním dvou modelů klasifikace. Ale celková prezentace je stejná. 

## <a name="metrics"></a>Metriky

Tato část popisuje metriky vracené pro konkrétní typy modelů, které jsou podporované pro použití s **vyhodnocením modelu**:

+ [modely klasifikace](#bkmk_classification)
+ [Regresní modely](#bkmk_regression)

###  <a name="bkmk_classification"></a>Metriky pro modely klasifikace

Při vyhodnocování modelů klasifikace jsou hlášeny následující metriky. Pokud porovnáte modely, jsou seřazené podle metriky, kterou jste vybrali pro vyhodnocení.  
  
-   **Přesnost** měření modelu klasifikace jako poměru skutečných výsledků do celkového počtu případů je dobrá.  
  
-   **Přesnost** je poměr skutečných výsledků pro všechny pozitivní výsledky.  
  
-   **Odvolání** je zlomek všech správných výsledků vrácených modelem.  
  
-   Hodnota **f-skore** je vypočítána jako vážený průměr přesnosti a odvolání mezi 0 a 1, kde ideální hodnota F-Skore je 1.  
  
-   **AUC** měří oblast pod křivkou vykreslenou se skutečnými klady na ose y a falešně pozitivních hodnot na ose x. Tato metrika je užitečná, protože poskytuje jedno číslo, které umožňuje porovnat modely různých typů.  
  
- **Průměrná ztráta protokolu** je jedno skóre, které slouží k vyjádření pokuty pro nesprávné výsledky. Počítá se jako rozdíl mezi dvěma distribucí pravděpodobnosti – true One a v modelu.  
  
- Ve službě **Training Log ztrát** je jedno skóre, které představuje výhodu klasifikátoru na náhodné předpovědi. Ztráta protokolu měří nejistotu modelu tím, že se porovnávají pravděpodobnosti, že se výstupy označují na známé hodnoty (v terénu). Chcete minimalizovat ztrátu protokolu pro model jako celek.

##  <a name="bkmk_regression"></a>Metriky pro regresní modely
 
Metriky vracené pro regresní modely jsou obecně navržené k odhadu množství chyb.  Model se považuje za správný, pokud rozdíl mezi zjištěnými a předpovězenými hodnotami je malý. Nicméně na vzor zbytků (rozdíl mezi jedním předpokládaným bodem a odpovídající skutečnou hodnotou) vám může sdělit spoustu potenciálního posunu v modelu.  
  
 Následující metriky jsou hlášeny pro vyhodnocení regresních modelů. Při porovnávání modelů jsou seřazeny podle metriky, kterou jste vybrali pro vyhodnocení.  
  
- **Střední absolutní chyba (Mae)** měří způsob, jakým se předpovědi blíží skutečným výsledkům. Proto je nižší skóre lepší.  
  
- **Hlavní střední hodnota chyby (RMSE)** vytvoří jednu hodnotu, která shrnuje chybu v modelu. Tím, že umocnění rozdíl, metrika ignoruje rozdíl mezi předpověďmi a průběžnými předpověďmi.  
  
- **Relativní absolutní chyba (Rae)** je relativní absolutní rozdíl mezi očekávanou a skutečnou hodnotou; relativní vzhledem k tomu, že střední rozdíl je vydělený aritmetickým průměrem.  
  
- **Relativní kvadratická chyba (RSE)** podobně normalizuje celkovou kvadratickou chybu předpokládaných hodnot tak, že se vydělí celkovými kvadratickými chybami skutečných hodnot.  
  
- **Střední hodnota nula (MZOE)** označuje, zda byla předpověď správná nebo ne.  Jinými slovy: `ZeroOneLoss(x,y) = 1`, když `x!=y`; jinak `0`.
  
- **Koeficient stanovitelnosti**, který se často označuje jako R<sup>2</sup>, představuje prediktivní sílu modelu jako hodnotu mezi 0 a 1. Nula znamená, že je model náhodný (vysvětluje nic); 1 znamená dokonalé přizpůsobení. Nicméně opatrnost by se měla použít při interpretaci hodnot R<sup>2</sup> , protože nízké hodnoty můžou být zcela normální a vysoké hodnoty můžou být podezřelé.
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 