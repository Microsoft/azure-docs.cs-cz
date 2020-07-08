---
title: 'Vyhodnocení modelu: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul vyhodnocení modelu v Azure Machine Learning k měření přesnosti vyučeného modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/24/2020
ms.openlocfilehash: 0b7ca2654fb8b7bdcca6dcb5f2fd354a138f2fcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564364"
---
# <a name="evaluate-model-module"></a>Vyhodnotit modul modelu

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul použijte k měření přesnosti trained model. Poskytnete datovou sadu obsahující skóre vygenerované z modelu a modul **vyhodnocení modelu** vypočítá sadu standardních metrik vyhodnocení v oboru.
  
 Metriky vrácené **modelem vyhodnocení** závisí na typu modelu, který vyhodnocujete:  
  
-   **Modely klasifikace**    
-   **Regresní modely**  
-   **Modely clusteringu**  


> [!TIP]
> Pokud s hodnocením modelu začínáte, doporučujeme, aby se série videí Dr. Stephen Elston jako součást [strojového učení](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) od EdX. 


## <a name="how-to-use-evaluate-model"></a>Jak používat model vyhodnocení
1. Připojte výstup skóre výsledné **sady** výsledků [modelu skóre](./score-model.md) nebo výstup výsledné sady [dat ke clusterům](./assign-data-to-clusters.md) k levému vstupnímu portu pro **vyhodnocení modelu**. 
  > [!NOTE] 
  > Pokud k výběru části vstupní datové sady používáte moduly, jako je "vybrat sloupce v sadě dat", zajistěte, aby byl pro výpočet metriky, jako je AUC, přesnost pro binární klasifikaci nebo detekci anomálií k dispozici sloupec s popiskem skóre
  > Pro výpočet metriky pro klasifikaci a regresi ve více třídách existuje skutečný sloupec popisku, sloupec označené skóre.
  > Sloupec ' přiřazení ', sloupce ' DistancesToClusterCenter ne. X (X je těžiště index, v rozsahu od 0,..., počet centroids-1) existuje pro výpočet metrik pro clusteringu.

2. Volitelné Připojte výstup výsledné **sady** dat [modelu skóre](./score-model.md) nebo výstup výsledné sady výsledků do clusterů pro druhý model ke **správnému** vstupnímu portu pro **vyhodnocení modelu**. Můžete snadno porovnat výsledky dvou různých modelů se stejnými daty. Dva vstupní algoritmy by měly být stejného typu algoritmu. Nebo můžete porovnat skóre ze dvou různých spuštění přes stejná data s různými parametry.

    > [!NOTE]
    > Typ algoritmu odkazuje na třídu "klasifikace dvou tříd", "klasifikace s více třídami", "regrese", "clusteringu" pod "Machine Learning algoritmy". 

3. Odešlete kanál pro vygenerování skóre vyhodnocení.

## <a name="results"></a>Výsledky

Po spuštění **modelu vyhodnocení**vyberte modul a otevřete na pravé straně navigační panel **modelu hodnocení** .  Pak zvolte kartu **výstupy + protokoly** a na této kartě se v části **datové výstupy** zobrazí několik ikon.   Ikona **vizualizace** má pruhový graf s ikonou a je prvním způsobem, jak zobrazit výsledky.

Pokud připojíte datové sady ke vstupům **modelu vyhodnocení**, budou výsledky obsahovat metriky pro sadu dat nebo oba modely.
Model nebo data připojená k levému portu se zobrazí jako první v sestavě, za kterými následuje metrika pro datovou sadu nebo model připojený ke správnému portu.  

Například následující obrázek představuje porovnání výsledků ze dvou modelů clusteringu, které byly vytvořeny na stejných datech, ale s různými parametry.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Vzhledem k tomu, že se jedná o model clusteringu, výsledky hodnocení jsou jiné než v porovnání s výsledky ze dvou regresních modelů nebo porovnáním dvou modelů klasifikace. Ale celková prezentace je stejná. 

## <a name="metrics"></a>Metriky

Tato část popisuje metriky vracené pro konkrétní typy modelů, které jsou podporované pro použití s **vyhodnocením modelu**:

+ [modely klasifikace](#metrics-for-classification-models)
+ [Regresní modely](#metrics-for-regression-models)
+ [modely clusteringu](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Metriky pro modely klasifikace

Při vyhodnocování modelů klasifikace jsou hlášeny následující metriky.
  
-   **Přesnost** měření modelu klasifikace jako poměru skutečných výsledků do celkového počtu případů je dobrá.  
  
-   **Přesnost** je poměr skutečných výsledků pro všechny pozitivní výsledky.  
  
-   **Odvolání** je zlomek všech správných výsledků vrácených modelem.  
  
-   Hodnota **f-skore** je vypočítána jako vážený průměr přesnosti a odvolání mezi 0 a 1, kde ideální hodnota F-Skore je 1.  
  
-   **AUC** měří oblast pod křivkou vykreslenou se skutečnými klady na ose y a falešně pozitivních hodnot na ose x. Tato metrika je užitečná, protože poskytuje jedno číslo, které umožňuje porovnat modely různých typů.  
  
- **Průměrná ztráta protokolu** je jedno skóre, které slouží k vyjádření pokuty pro nesprávné výsledky. Počítá se jako rozdíl mezi dvěma distribucí pravděpodobnosti – true One a v modelu.  
  
- Ve službě **Training Log ztrát** je jedno skóre, které představuje výhodu klasifikátoru na náhodné předpovědi. Ztráta protokolu měří nejistotu modelu tím, že se porovnávají pravděpodobnosti, že se výstupy označují na známé hodnoty (v terénu). Chcete minimalizovat ztrátu protokolu pro model jako celek.

### <a name="metrics-for-regression-models"></a>Metriky pro regresní modely
 
Metriky vracené pro regresní modely jsou navržené k odhadu množství chyb.  Model se považuje za správný, pokud rozdíl mezi zjištěnými a předpovězenými hodnotami je malý. Nicméně na vzor zbytků (rozdíl mezi jedním předpokládaným bodem a odpovídající skutečnou hodnotou) vám může sdělit spoustu potenciálního posunu v modelu.  
  
 Následující metriky jsou hlášeny pro vyhodnocení regresních modelů.
  
- **Střední absolutní chyba (Mae)** měří způsob, jakým se předpovědi blíží skutečným výsledkům. Proto je nižší skóre lepší.  
  
- **Hlavní střední hodnota chyby (RMSE)** vytvoří jednu hodnotu, která shrnuje chybu v modelu. Tím, že umocnění rozdíl, metrika ignoruje rozdíl mezi předpověďmi a průběžnými předpověďmi.  
  
- **Relativní absolutní chyba (Rae)** je relativní absolutní rozdíl mezi očekávanou a skutečnou hodnotou; relativní vzhledem k tomu, že střední rozdíl je vydělený aritmetickým průměrem.  
  
- **Relativní kvadratická chyba (RSE)** podobně normalizuje celkovou kvadratickou chybu předpokládaných hodnot tak, že se vydělí celkovými kvadratickými chybami skutečných hodnot.  
  

  
- **Koeficient stanovitelnosti**, který se často označuje jako R<sup>2</sup>, představuje prediktivní sílu modelu jako hodnotu mezi 0 a 1. Nula znamená, že je model náhodný (vysvětluje nic); 1 znamená dokonalé přizpůsobení. Nicméně opatrnost by se měla použít při interpretaci hodnot R<sup>2</sup> , protože nízké hodnoty můžou být zcela normální a vysoké hodnoty můžou být podezřelé.

###  <a name="metrics-for-clustering-models"></a>Metriky pro modely clusteringu

Vzhledem k tomu, že se modely clusteringu výrazně liší od klasifikace a regrese modelů v mnoha ohledech, [vyhodnocení modelu](evaluate-model.md) také vrátí jinou sadu statistik pro modely clusteringu.  
  
 Statistika vracené pro model clusteringu popisuje, kolik datových bodů bylo přiřazeno ke každému clusteru, množství oddělení mezi clustery a o tom, jak pevně jsou datové body v rámci jednotlivých clusterů rozsvazcích.  
  
 Statistiky modelu clusteringu jsou průměrně vypočítány přes celou datovou sadu s dalšími řádky, které obsahují statistiku na cluster.  
  
Následující metriky jsou hlášeny pro vyhodnocení modelů clusteringu.
    
-   Skóre ve sloupci, **Průměrná vzdálenost k druhému středu**představuje způsob, jakým se v průměru blíží každý bod v clusteru, do centroids všech ostatních clusterů.   

-   Skóre ve sloupci, **Průměrná vzdálenost do centra clusterů**, představuje uzavření všech bodů v clusteru do těžiště tohoto clusteru.  
  
-   Sloupec **počet bodů** ukazuje, kolik datových bodů bylo přiřazeno ke každému clusteru, spolu s celkovým celkovým počtem datových bodů v jakémkoli clusteru.  
  
     Pokud je počet datových bodů přiřazených ke clusterům menší, než je celkový počet dostupných datových bodů, znamená to, že datové body nelze přiřadit ke clusteru.  
  
-   Skóre ve sloupci, **maximální vzdálenost do centra clusterů**, představuje maximální vzdálenost mezi každým bodem a těžištěem clusteru daného bodu.  
  
     Pokud je toto číslo vysoké, může to znamenat, že cluster je výrazně rozptýlený. Tuto statistiku byste měli projít společně s **průměrnou vzdáleností do centra clusterů** k určení rozprostření clusteru.   

-   **Kombinované skóre vyhodnocení** v dolní části každé části výsledků obsahuje průměrné skóre pro clustery vytvořené v tomto konkrétním modelu.  
  

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
