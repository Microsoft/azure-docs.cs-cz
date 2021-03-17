---
title: 'Více třídou rozhodovacího stromu s zvýšením úrovně: Reference k modulu'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul více tříd pro rozhodovací strom s více třídami v Azure Machine Learning k vytvoření klasifikátoru pomocí popisků dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905306"
---
# <a name="multiclass-boosted-decision-tree"></a>Posílený rozhodovací strom s několika třídami

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení, který je založený na algoritmu zesílených rozhodovacích stromů.

Posílený rozhodovací strom je metoda učení kompletu, ve které druhý strom opravuje chyby prvního stromu, třetí strom pro chyby první a druhé stromy a tak dále. Předpovědi jsou založené na kompletu stromů dohromady.

## <a name="how-to-configure"></a>Jak nakonfigurovat 

Tento modul vytváří nevlakový model klasifikace. Vzhledem k tomu, že klasifikace je metoda učení pod dohledem, potřebujete *datovou sadu s popiskem* , která obsahuje sloupec popisku s hodnotou pro všechny řádky.

Tento typ modelu můžete vyškolit pomocí [modelu vlakové soupravy](././train-model.md). 

1.  Přidejte do vašeho kanálu modul pro **zvýšení rozhodovacího stromu s více třídami** .

1.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .

    + **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.
    
    + **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  

1. **Maximální počet ponechání na stromovou strukturu** omezuje maximální počet uzlů terminálů (opustí), které je možné vytvořit v jakémkoli stromu.
    
    Zvýšením této hodnoty můžete zvýšit velikost stromu a dosáhnout vyšší přesnosti, a to na riziko přebudování a delší dobu školení.
  
1. **Minimální počet vzorků na uzel na list** určuje počet případů vyžadovaných k vytvoření libovolného uzlu terminálu (list) ve stromu.  

    Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.

1. **Studijní frekvence** definuje velikost kroku při učení. Zadejte číslo od 0 do 1.

    Studijní frekvence určuje, jak rychle nebo pomalu se učí sblížení s optimálním řešením. Pokud je velikost kroku moc velká, můžete optimální řešení vyhodnotit. Pokud je velikost kroku příliš malá, školení trvá déle, než se sblížení dostanou na nejlepší řešení.

1. **Počet vytvořených stromů** označuje celkový počet rozhodovacích stromů, které se mají v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete získat lepší pokrytí, ale čas školení se zvýší.

1. **Náhodné číslo semen** volitelně nastaví nezáporné celé číslo, které se použije jako hodnota náhodného základu. Určení počáteční hodnoty zajišťuje reprodukovatelnost v různých spuštěních, která mají stejná data a parametry.  

    Náhodné osazení je ve výchozím nastavení nastaveno na 42. Po sobě jdoucí běhy s různými náhodnými osivy můžou mít různé výsledky.

1. Výukové modely:

    + Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, připojíte tagované datové sady a modul [vlakového modelu](train-model.md) .  
  
    + Pokud nastavíte **režim vytvořit Trainer** na **rozsah parametrů**, připojíte s příznakovou datovou sadu a provedete model pomocí [předparametrů ladit model](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Pokud předáte rozsah parametru pro [vlakový model](train-model.md), použije se jenom výchozí hodnota v seznamu jednoho parametru.  
    > 
    > Pokud předáte jednu sadu hodnot parametrů do modulu [předparametrů modelu ladění](tune-model-hyperparameters.md) , když očekává rozsah nastavení pro každý parametr, ignoruje hodnoty a použije výchozí hodnoty pro učit se.  
    > 
    > Pokud vyberete možnost **rozsahu parametrů** a zadáte jednu hodnotu pro libovolný parametr, tato jediná hodnota, kterou jste zadali, se použije v celém oblouku, a to i v případě, že se jiné parametry mění v rozsahu hodnot.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
