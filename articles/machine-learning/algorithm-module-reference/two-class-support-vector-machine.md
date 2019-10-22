---
title: 'Podpora dvou tříd – vektorový počítač: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul **vektorového počítače podpory dvou tříd** ve službě Azure Machine Learning Service k vytvoření modelu, který je založený na algoritmu podpory vektorového počítače.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c58e66070f137f9c5d713a45682afac5f30bbd1e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692609"
---
# <a name="two-class-support-vector-machine-module"></a>Modul Vector Machine Support pro dvě třídy

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Tento modul použijte k vytvoření modelu, který je založen na algoritmu podpory vektorového počítače. 

Podpora vektorových počítačů (SVMs) je dobře prohledaná třída studijních metod, které jsou pod dohledem. Tato konkrétní implementace je vhodná pro předpověď dvou možných výsledků, a to na základě souvislých nebo kategorií proměnných.

Po definování parametrů modelu proveďte výuku modelu pomocí školicích modulů a poskytněte *tagované datové sady* , která obsahuje sloupec popisku nebo výsledku.

## <a name="about-support-vector-machines"></a>O podpoře vektorových počítačů

Podpora vektorových počítačů je mezi nejstaršími algoritmy strojového učení a modely SVM se používají v mnoha aplikacích, od načtení informací do klasifikace textu a obrázku. SVMs lze použít pro úlohy klasifikace i regrese.

Tento model SVM je model výuky pod dohledem, který vyžaduje data s popisky. V procesu školení algoritmus analyzuje vstupní data a rozpoznává vzory v multidimenzionálním prostoru *funkce s názvem.*  Všechny vstupní příklady jsou reprezentovány jako body v tomto prostoru a jsou namapovány na výstupní kategorie takovým způsobem, že kategorie jsou rozděleny tak, jak jsou, a co nejpřesnější je vymazat.

Pro předpovědi SVM algoritmus přiřadí nové příklady do jedné kategorie nebo do druhé a namapuje je na stejné místo. 

## <a name="how-to-configure"></a>Jak nakonfigurovat 

Pro tento typ modelu doporučujeme datovou sadu normalizovat před jejím použitím k učení třídění.
  
1.  Přidejte modul **vektorového počítače podpory dvou tříd** do vašeho kanálu.  
  
2.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.  

3.  Pro **počet iterací**zadejte číslo, které označuje počet iterací použitých při sestavování modelu.  
  
     Tento parametr lze použít k řízení vzájemného obchodování mezi rychlostí a přesností školení.  
  
4.  Pro **výraz lambda**zadejte hodnotu, která se má použít jako váha pro pravidelnou práci s L1.  
  
     Tento koeficient depravidelnosti se dá použít k ladění modelu. Vyšší hodnoty postihují složitější modely.  
  
5.  Pokud chcete funkce normalizovat před školením, vyberte možnost **normalizovat funkce**.
  
     Použijete-li normalizaci, před školením budou datové body zarovnány na střední hodnotu a škálovat tak, aby měly jednu jednotku směrodatné odchylky.
  
6.  Chcete-li normalizovat koeficienty, vyberte možnost **projekt k police jednotky**.
  
     Při projekci hodnot do místa na disku znamená, že před školením jsou datové body zarovnány na střed 0 a zvětšeny tak, aby měly jednu jednotku směrodatné odchylky.
  
7.  V **počátečním čísle náhodného čísla**zadejte celočíselnou hodnotu, která se má použít jako počáteční hodnota pro zajištění reprodukovatelnosti napříč běhy.  V opačném případě se systémová hodnota hodin používá jako počáteční hodnota, což může vést ke vzniku mírně odlišných výsledků napříč běhy.
  
9. Připojte s popiskem datovou sadu a jeden z [školicích modulů](module-reference.md):
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](train-model.md) .
  

10. Spuštění kanálu

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete zobrazit souhrn parametrů modelu spolu s váhy funkcí zjištěnými ze školení, klikněte pravým tlačítkem myši na výstup [modelu vlaků](./train-model.md)a vyberte **vizualizovat**.

+ Chcete-li použít trained Models k vytvoření předpovědi, připojte školený model k modulu určení [skóre modelu](score-model.md) .


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 