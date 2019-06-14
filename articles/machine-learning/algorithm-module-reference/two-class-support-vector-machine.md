---
title: 'Two-Class Support Vector Machine: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití **Two-Class Support Vector Machine** modulu ve službě Azure Machine Learning k vytvoření modelu, který je založen na algoritmus podpůrného vektorového stroje.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027927"
---
# <a name="two-class-support-vector-machine-module"></a>Modul Two-Class Support Vector Machine

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k vytvoření modelu, který je založen na algoritmus podpůrného vektorového stroje. 

Podpora vektoru počítače (SVMs) jsou dobře prozkoumány třídy metod učení. Tato konkrétní implementace je vhodné k předpovědi dva možné výsledky na základě průběžné nebo kategorií proměnných.

Po definování parametry modelu, výuku modelu používání modulů školení a poskytnutím *označené datovou sadu* , který obsahuje popisek nebo výsledku sloupec.

## <a name="about-support-vector-machines"></a>O počítačích vektorové podpory

Support vector počítače jsou mezi nejdřívější algoritmů strojového učení a modely SVM se používají v mnoha aplikacích, od načítání informací o textových a obrázkových klasifikace. SVMs lze použít pro klasifikačních a regresních úlohy.

Tento model SVM je učení modelu, který vyžaduje označených datech. V procesu trénování algoritmus analyzuje vstupní data a rozpozná vzory v prostoru multidimenzionální funkce volá se, *hyperplane*.  Všechny vstupní příklady jsou reprezentovány ve formě bodů v tomto prostoru a jsou mapovány na výstup kategorií tak, že kategorie jsou rozdělené podle jako celé a zrušte mezera nejvíce.

Pro předpověď algoritmus SVM přiřadí nové příklady do jedné kategorie, nebo druhé, mapování do tohoto stejného místa. 

## <a name="how-to-configure"></a>Postup konfigurace 

Pro tento typ modelu se doporučuje normalizovat datové sady před použitím tak moct trénovat třídění.
  
1.  Přidat **Two-Class Support Vector Machine** modulu do experimentu.  
  
2.  Určení způsobu modelu zaškolení, tak, že nastavíte **režimu vytváření trainer** možnost.  
  
    -   **Jeden parametr**: Pokud víte, jak chcete provést konfiguraci modelu, můžete zadat konkrétní sadu hodnot jako argumenty.  

3.  Pro **počet iterací**, zadejte číslo, které označuje počet iterací používat při vytváření modelu.  
  
     Tento parametr lze použít k řízení kompromis mezi školení rychlost a přesnost.  
  
4.  Pro **Lambda**, zadejte hodnotu, která se použije jako váhu pro L1 regularizace.  
  
     Tento regularizace koeficient slouží k vyladění modelu. Vyšší hodnoty postihu složitější modelů.  
  
5.  Vyberte možnost, **normalizovat funkce**, pokud chcete normalizovat součásti. teprve potom školení.
  
     Pokud použijete normalizace před školení, datové body jsou zaměřené na střední a škálovat mít jednu jednotku směrodatnou odchylku.
  
6.  Vyberte možnost, **projektu na tvar koule jednotka**, koeficienty normalizace.
  
     Plánování hodnot do částí prostoru znamená, že před školení, datové body jsou zaměřené na 0 a škálovat mít jednu jednotku směrodatnou odchylku.
  
7.  V **náhodná počáteční hodnota čísla**, zadejte celočíselnou hodnotu pro použití jako základní hodnota, pokud chcete zajistit reprodukovatelnost během spuštění.  V opačném případě hodnota hodiny systému slouží jako počáteční hodnoty, což může vést mírně odlišné výsledky během spuštění.
  
9. Propojení s popiskem datovou sadu a jeden z [školicí moduly](module-reference.md):
  
    -   Pokud nastavíte **režimu vytváření trainer** k **jediný parametr**, použijte [trénování modelu](train-model.md) modulu.
  

10. Spusťte experiment.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit souhrn parametry modelu, společně s funkcí váhy získané při školení, klikněte pravým tlačítkem na výstup [Train Model](./train-model.md)a vyberte **vizualizovat**.

+ Pokud chcete použít k následné predikci trénované modely, připojte trénovaný model pro [Score Model](score-model.md) modulu.


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 