---
title: 'Podpora dvou tříd – vektorový počítač: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul **vektorového počítače podpory dvou tříd** v Azure Machine Learning k vytvoření modelu, který je založený na algoritmu podpory vektorového počítače.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 1dc2f8bdf6ed2823b44d25bdf65766b1f7ae060c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907666"
---
# <a name="two-class-support-vector-machine-module"></a>Modul Vector Machine Support pro dvě třídy

Tento článek popisuje modul v Návrháři Azure Machine Learning.

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

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [ladit model parametrů](tune-model-hyperparameters.md) . Poskytnete určitou škálu hodnot a Trainer iterovat více kombinací nastavení a určí kombinaci hodnot, které vytvářejí nejlepší výsledek.

3.  Pro **počet iterací**zadejte číslo, které označuje počet iterací použitých při sestavování modelu.  
  
     Tento parametr lze použít k řízení vzájemného obchodování mezi rychlostí a přesností školení.  
  
4.  Pro **výraz lambda**zadejte hodnotu, která se má použít jako váha pro pravidelnou práci s L1.  
  
     Tento koeficient depravidelnosti se dá použít k ladění modelu. Vyšší hodnoty postihují složitější modely.  
  
5.  Pokud chcete funkce normalizovat před školením, vyberte možnost **normalizovat funkce**.
  
     Použijete-li normalizaci, před školením budou datové body zarovnány na střední hodnotu a škálovat tak, aby měly jednu jednotku směrodatné odchylky.
  
6.  Chcete-li normalizovat koeficienty, vyberte možnost **projekt k police jednotky**.
  
     Při projekci hodnot do místa na disku znamená, že před školením jsou datové body zarovnány na střed 0 a zvětšeny tak, aby měly jednu jednotku směrodatné odchylky.
  
7.  V **počátečním čísle náhodného čísla**zadejte celočíselnou hodnotu, která se má použít jako počáteční hodnota pro zajištění reprodukovatelnosti napříč běhy.  V opačném případě se systémová hodnota hodin používá jako počáteční hodnota, což může vést ke vzniku mírně odlišných výsledků napříč běhy.
  
9. Propojit s popiskem datovou sadu a vyškolit model:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.
  
10. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení školení:

+ Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

+ Chcete-li použít model pro bodování, přidejte modul určení **skóre modelu** do kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 