---
title: 'Dvoutřídní podpůrný vektorový stroj: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu **Dvoutřídní ho repromovacího stroje** v Azure Machine Learning vytvořit model, který je založený na algoritmu podpůrného vektorového počítače.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455804"
---
# <a name="two-class-support-vector-machine-module"></a>Dvoutřídní modul vektorového stroje pro podporu

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu, který je založen na algoritmu podpůrného vektorového počítače. 

Podpůrné vektorové stroje (SVM) jsou dobře prozkoumanou třídou metod učení pod dohledem. Tato konkrétní implementace je vhodná pro predikci dvou možných výsledků založených na spojitých nebo kategorických proměnných.

Po definování parametrů modelu trénování modelu pomocí trénovacích modulů a poskytnutí *tagované datové sady,* která obsahuje popisek nebo sloupec výsledků.

## <a name="about-support-vector-machines"></a>O podpůrných vektorových strojích

Podpůrné vektorové počítače patří mezi nejstarší algoritmy strojového učení a modely SVM byly použity v mnoha aplikacích, od načítání informací až po klasifikaci textu a obrázků. SVM lze použít pro klasifikace i regresní úkoly.

Tento model SVM je model učení pod dohledem, který vyžaduje označená data. V procesu školení algoritmus analyzuje vstupní data a rozpozná vzory v multi-dimenzionální funkce prostoru zvaného *hyperplane*.  Všechny vstupní příklady jsou reprezentovány jako body v tomto prostoru a jsou mapovány na výstupní kategorie tak, aby kategorie byly rozděleny co nejširší a vymazání mezery, jak je to možné.

Pro předpověď algoritmus SVM přiřadí nové příklady do jedné kategorie nebo jiné, mapování je do stejného prostoru. 

## <a name="how-to-configure"></a>Jak nakonfigurovat 

Pro tento typ modelu se doporučuje normalizovat datovou sadu před použitím k trénování třídění.
  
1.  Přidejte do kanálu modul **Dvoutřídní podpůrný vektorový stroj.**  
  
2.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty.  

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Zadáte určitý rozsah hodnot a trenér iterates přes více kombinací nastavení k určení kombinace hodnot, které vytváří nejlepší výsledek.

3.  Pro **počet iterací**zadejte číslo, které označuje počet iterací použitých při vytváření modelu.  
  
     Tento parametr lze použít k řízení kompromisu mezi rychlostí tréninku a přesností.  
  
4.  Do **pole Lambda**zadejte hodnotu, která se má použít jako hmotnost pro regularizaci L1.  
  
     Tento regularizační koeficient lze použít k vyladění modelu. Větší hodnoty penalizují složitější modely.  
  
5.  Vyberte možnost **Normalizovat funkce**, pokud chcete normalizovat funkce před trénování.
  
     Pokud použijete normalizaci, před trénincí jsou datové body vystředěny na střední hodnotu a zmenšeny tak, aby měly jednu jednotku směrodatné odchylky.
  
6.  Vyberte **možnost, Projekt na oblast jednotky**, chcete-li normalizovat koeficienty.
  
     Promítání hodnot do místa jednotky znamená, že před trénováním jsou datové body vystředěny na 0 a zmenšeny tak, aby měly jednu jednotku směrodatné odchylky.
  
7.  Do **pole Osiva náhodných čísel**zadejte celou hodnotu, kterou chcete použít jako osiva, pokud chcete zajistit reprodukovatelnost napříč spuštěními.  V opačném případě se jako osiva používá hodnota systémových hodin, což může mít za následek mírně odlišné výsledky napříč spuštěními.
  
9. Připojte označenou datovou sadu a jeden z [trénovacích modulů](module-reference.md):
  
    -   Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](train-model.md)
  
10. Odešlete potrubí.

## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

+ Chcete-li použít model pro vyhodnocování, přidejte modul **Modelu skóre** do kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 