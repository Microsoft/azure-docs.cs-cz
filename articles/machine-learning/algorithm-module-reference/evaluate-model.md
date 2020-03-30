---
title: 'Vyhodnotit model: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Vyhodnotit model v Azure Machine Learning měřit přesnost trénovaného modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456400"
---
# <a name="evaluate-model-module"></a>Vyhodnocovat modelový modul

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k měření přesnosti trénovaného modelu. Poskytujete datovou sadu obsahující skóre generovaná z modelu a modul **Vyhodnotit model** vypočítá sadu standardních metrik hodnocení.
  
 Metriky vrácené **hodnotit model** závisí na typu modelu, který vyhodnocujete:  
  
-   **Klasifikační modely**    
-   **Regresní modely**  
-   **Clustering modely**  


> [!TIP]
> Pokud s hodnocením modelu teknete, doporučujeme sérii videí dr. Stephena Elstona jako součást [kurzu strojového učení](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) od edxu. 


Modul **Vyhodnotit model** lze použít třemi způsoby:

+ Vygenerujte skóre nad trénovacími daty a vyhodnoťte model na základě těchto skóre.
+ Generovat skóre na modelu, ale porovnat tyto skóre skóre na vyhrazené testovací sady
+ Porovnání skóre pro dva různé, ale související modely pomocí stejné sady dat

## <a name="use-the-training-data"></a>Použití trénovacích dat

Chcete-li vyhodnotit model, musíte připojit datovou sadu, která obsahuje sadu vstupních sloupců a skóre.  Pokud nejsou k dispozici žádná další data, můžete použít původní datovou sadu.

1. Připojte výstup **datové sady Scored** [modelu skóre](./score-model.md) ke vstupu vyhodnocení **modelu**. 
2. Klikněte na **Vyhodnotit** modul modelu a spusťte kanál a vygenerujte skóre vyhodnocení.

## <a name="use-testing-data"></a>Použití testovacích dat

Běžným scénářem ve strojovém učení je oddělení původní sady dat do trénovacích a testovacích datových sad pomocí modulu [Split](./split-data.md) nebo modulu [Oddíl a ukázka.](./partition-and-sample.md) 

1. Připojte výstup **datové sady Scored** [modelu skóre](score-model.md) ke vstupu vyhodnocení **modelu**. 
2. Připojte výstup modulu Split Data, který obsahuje testovací data, k pravému vstupu **vyhodnocení modelu**.
2. Klikněte na **Vyhodnotit** modul modelu a vyberte **Spustit vybranou,** chcete-li generovat hodnocení.

## <a name="compare-scores-from-two-models"></a>Porovnání skóre ze dvou modelů

Můžete také připojit druhou sadu skóre **vyhodnotit model**.  Skóre může být sdílená sada hodnocení, která má známé výsledky, nebo sada výsledků z jiného modelu pro stejná data.

Tato funkce je užitečná, protože můžete snadno porovnat výsledky ze dvou různých modelů na stejná data. Nebo můžete porovnat skóre ze dvou různých spuštění přes stejná data s různými parametry.

1. Připojte výstup **datové sady Scored** [modelu skóre](score-model.md) ke vstupu vyhodnocení **modelu**. 
2. Připojte výstup modulu Model skóre pro druhý model k pravému vstupu **vyhodnotit model**.
3. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po spuštění **vyhodnotit model**, klikněte pravým tlačítkem myši na modul a **vyberte visualizovat výsledky vyhodnocení** zobrazíte výsledky.

Pokud připojíte datové sady k oběma vstupům **vyhodnotit model**, výsledky budou obsahovat metriky pro obě sady dat nebo oba modely.
Model nebo data připojená k levému portu jsou uvedena nejprve v sestavě, následovaná metrikami pro datovou sadu nebo modelem připojeným k pravému portu.  

Například následující obrázek představuje porovnání výsledků ze dvou modelů clusteringu, které byly postaveny na stejných datech, ale s různými parametry.  

![Porovnání2Modelů](media/module/evaluate-2-models.png)  

Vzhledem k tomu, že se jedná o model clusteringu, výsledky hodnocení se liší, než když porovnáte skóre ze dvou regresních modelů nebo porovnáte dva klasifikační modely. Celková prezentace je však stejná. 

## <a name="metrics"></a>Metriky

Tato část popisuje metriky vrácené pro konkrétní typy modelů podporovaných pro použití s **vyhodnocením modelu**:

+ [klasifikační modely](#metrics-for-classification-models)
+ [regresní modely](#metrics-for-regression-models)
+ [clustering modely](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriky pro klasifikační modely

Následující metriky jsou hlášeny při hodnocení klasifikační modely.
  
-   **Přesnost** měří dobrotu klasifikačního modelu jako podíl skutečných výsledků na celkových případech.  
  
-   **Přesnost** je podíl skutečných výsledků na všech pozitivních výsledcích.  
  
-   **Odvolání** je zlomek všech správných výsledků vrácených modelem.  
  
-   **F-skóre** se vypočítá jako vážený průměr přesnosti a odvolání mezi 0 a 1, kde je ideální hodnota F-skóre 1.  
  
-   **AUC** měří plochu pod křivkou vykreslenou se skutečnými pozitivy na ose y a falešnými poplachy na ose x. Tato metrika je užitečná, protože poskytuje jedno číslo, které umožňuje porovnat modely různých typů.  
  
- **Průměrná ztráta protokolu** je jediné skóre, které se používá k vyjádření trestu za nesprávné výsledky. Vypočítá se jako rozdíl mezi dvěma rozděleními pravděpodobnosti – skutečným a rozdělením v modelu.  
  
- **Ztráta protokolu školení** je jediné skóre, které představuje výhodu třídění přes náhodné předpověď. Ztráta protokolu měří nejistotu modelu porovnáním pravděpodobností, které vydělá, se známými hodnotami (základní pravda) v popiscích. Chcete minimalizovat ztrátu protokolu pro model jako celek.

### <a name="metrics-for-regression-models"></a>Metriky pro regresní modely
 
Metriky vrácené pro regresní modely jsou navrženy tak, aby odhadly množství chyb.  Model se považuje za vhodný pro data dobře, pokud je rozdíl mezi pozorovanými a předpovídanouhodnotami malý. Nicméně, při pohledu na vzor zbytků (rozdíl mezi jedním předpokládaným bodem a jeho odpovídající skutečnou hodnotou) vám může říct hodně o potenciálním zkreslení v modelu.  
  
 Následující metriky jsou hlášeny pro vyhodnocení regresní modely.
  
- **Průměrná absolutní chyba (MAE)** měří, jak blízko jsou předpovědi ke skutečným výsledkům; tak, nižší skóre je lepší.  
  
- **Kořenová střední kvadratická chyba (RMSE)** vytvoří jednu hodnotu, která shrnuje chybu v modelu. Srovnat rozdíl, metrika ignoruje rozdíl mezi over-prediction a under-prediction.  
  
- **Relativní absolutní chyba (RAE)** je relativní absolutní rozdíl mezi očekávanými a skutečnými hodnotami; relativní, protože střední rozdíl je dělen aritmetickým průměrem.  
  
- **Relativní kvadratózní chyba (RSE)** podobně normalizuje celkovou kvadračovou chybu předpovídaných hodnot vydělením celkovou kvadračnou chybou skutečných hodnot.  
  

  
- **Determinační koeficient**, často označovaný jako R<sup>2</sup>, představuje prediktivní sílu modelu jako hodnotu mezi 0 a 1. Nula znamená, že model je náhodný (nic nevysvětluje); 1 znamená, že je perfektní fit. Při interpretaci hodnot R<sup>2</sup> je však třeba dbát opatrnosti, protože nízké hodnoty mohou být zcela normální a vysoké hodnoty mohou být podezřelé.

###  <a name="metrics-for-clustering-models"></a>Metriky pro clustering modely

Vzhledem k tomu, že clustering modely výrazně liší od klasifikace a regrese modely v mnoha [ohledech, Vyhodnotit model](evaluate-model.md) také vrátí jinou sadu statistik pro clustering modely.  
  
 Statistiky vrácené pro model clusteringu popisují, kolik datových bodů bylo přiřazeno jednotlivým clusterům, množství oddělení mezi clustery a jak pevně jsou datové body seskupeny v rámci každého clusteru.  
  
 Statistiky pro model clusteringu jsou zprůměrovány v celé datové sadě, s dalšířádky obsahující statistiky na clusteru.  
  
Následující metriky jsou hlášeny pro vyhodnocení clustering modely.
    
-   Skóre ve sloupci **Průměrná vzdálenost k jinému středu**představují, jak blízko je v průměru každý bod v clusteru centroidům všech ostatních klastrů.   

-   Skóre ve sloupci **Průměrná vzdálenost ke středu clusteru**představují blízkost všech bodů v clusteru k centroidu tohoto clusteru.  
  
-   Sloupec **Počet bodů zobrazuje,** kolik datových bodů bylo přiřazeno jednotlivým clusterům, spolu s celkovým počtem datových bodů v libovolném clusteru.  
  
     Pokud je počet datových bodů přiřazených clusterům menší než celkový počet dostupných datových bodů, znamená to, že datové body nelze clusteru přiřadit.  
  
-   Skóre ve sloupci **Maximální vzdálenost k Centru clusteru**představují součet vzdáleností mezi jednotlivými body a centroidem clusteru tohoto bodu.  
  
     Pokud je toto číslo vysoké, může to znamenat, že cluster je široce rozptýlený. Tuto statistiku byste měli zkontrolovat společně s **centrem průměrná vzdálenost do clusteru,** abyste určili dvojstránky clusteru.   

-   **Kombinované hodnocení** skóre v dolní části každé části výsledků uvádí průměrné skóre pro clustery vytvořené v daném modelu.  
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 