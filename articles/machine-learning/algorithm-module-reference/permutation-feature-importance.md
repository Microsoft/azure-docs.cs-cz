---
title: 'Důležitost funkce permutace: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Důležitost funkce permutace v Azure Machine Learning vypočítat skóre důležitosti funkce funkce, vzhledem k trénovaného modelu a testovací datové sadě.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456059"
---
# <a name="permutation-feature-importance"></a>Důležitost funkce permutací

Tento článek popisuje, jak použít modul Důležitost funkce permutace v návrháři Azure Machine Learning (preview) k výpočtu sady skóre důležitosti funkce pro vaši datovou sadu. Tato skóre slouží k určení nejlepší funkce pro použití v modelu.

V tomto modulu jsou hodnoty prvků náhodně zamíchány, po jednom sloupci. Výkon modelu se měří před a po. Můžete zvolit jednu ze standardních metrik pro měření výkonu.

Skóre, které modul vrátí představují *změnu* výkonu trénovaného modelu po permutaci. Důležité funkce jsou obvykle citlivější na proces míchání, takže budou mít za následek vyšší důležitost skóre. 

Tento článek obsahuje přehled funkce permutace, jeho teoretického základu a jeho aplikací ve strojovém učení: [Význam funkce permutace](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Jak používat důležitost funkce permutace

Generování sady výsledků funkcí vyžaduje, abyste měli již trénovaný model a také testovací datovou sadu.  

1.  Přidejte do kanálu modul Důležitost funkce permutace. Tento modul naleznete v kategorii **Výběr funkcí.** 

2.  Připojte trénovaný model k levému vstupu. Model musí být regresní model nebo klasifikační model.  

3.  Na pravém vstupu připojte datovou sadu. Pokud možno vyberte ten, který se liší od datové sady, kterou jste použili pro trénování modelu. Tato datová sada se používá pro vyhodnocování na základě trénovaného modelu. Používá se také pro vyhodnocení modelu po změně hodnot prvku.  

4.  U **náhodného osiva**zadejte hodnotu, která se má použít jako osivo pro randomizaci. Pokud zadáte 0 (výchozí), číslo je generováno na základě systémových hodin.

     Hodnota osiva je volitelná, ale měli byste zadat hodnotu, pokud chcete reprodukovatelnost napříč spuštěními stejného kanálu.  

5.  Pro **metriku pro měření výkonu**vyberte jednu metriku, která se má použít při výpočtu kvality modelu po permutaci.  

     Azure Machine Learning návrhář podporuje následující metriky, v závislosti na tom, zda vyhodnocujete model klasifikace nebo regrese:  

    -   **Classification**

        Přesnost, přesnost, odvolání  

    -   **Regrese**

        Přesnost, odvolání, střední absolutní chyba, střední kvadratická chyba, relativní absolutní chyba, relativní kvadratická chyba, koeficient stanovení  

     Podrobnější popis těchto metrik hodnocení a jejich výpočtu najdete v [tématu Vyhodnocení modelu](evaluate-model.md).  

6.  Odešlete potrubí.  

7.  Modul vypíše seznam sloupců funkcí a skóre s nimi spojené. Seznam je seřazen v sestupném pořadí podle skóre.  


##  <a name="technical-notes"></a>Technické poznámky

Důležitost funkce permutace funguje tak, že náhodně mění hodnoty každého sloupce prvku, po jednom sloupci. Potom vyhodnotí model. 

Pořadí, které modul poskytuje, se často liší od hodnocení, které získáte z [výběru funkcí založených na filtru](filter-based-feature-selection.md). Výběr funkce založený na filtru vypočítá skóre *před* vytvořením modelu. 

Důvodem rozdílu je, že důležitost funkce permutace neměří přidružení mezi funkcí a cílovou hodnotou. Místo toho zachycuje, jak velký vliv má každá funkce na předpovědi z modelu.
  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
