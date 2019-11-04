---
title: 'Permutace – funkce důležitost: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se, jak používat modul důležitost funkcí permutace ve službě Azure Machine Learning k výpočtu důležitosti funkcí pro funkce permutace na základě toho, který vyškolený model a testovací datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517940"
---
# <a name="permutation-feature-importance"></a>Funkce permutace – důležitost

Tento článek popisuje, jak použít modul [důležitost funkcí permutace](permutation-feature-importance.md) v Návrháři Azure Machine Learning (Preview) k výpočtu sady výsledků důležitosti funkcí pro datovou sadu. Tato skóre vám pomůžou určit nejlepší funkce pro použití v modelu.

V tomto modulu jsou hodnoty funkcí náhodně přemístěné, jeden sloupec v čase a výkon modelu se měří před a po. Můžete zvolit jednu ze standardních metrik, které jsou k dispozici pro měření výkonu.

Skóre, které modul vrací, představuje **změnu** výkonu pro vyškolený model po permutaci. Důležité funkce jsou obvykle citlivější na proces náhodného zpracování a výsledkem bude vyšší důležitost. 

Tento článek poskytuje dobrý obecný přehled důležitosti funkcí permutace, teoreticky a jeho aplikací ve strojovém učení: [Permutace – důležitost funkcí](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Použití funkce permutace důležitost

Aby bylo možné vygenerovat sadu výsledků funkcí, je nutné mít již vyškolený model i testovací datovou sadu.  

1.  Přidejte modul **důležitost funkce permutace** do vašeho kanálu. Tento modul můžete najít v kategorii **výběru funkcí** . 

2.  Připojte k levému vstupu vyškolený model. Model musí být regresní model nebo model klasifikace.  

3.  Ve správném vstupu připojte datovou sadu, nejlépe tu, která se liší od datové sady použité pro školení modelu. Tato datová sada se používá pro hodnocení založené na škole modelu a pro vyhodnocení modelu po změně hodnot funkcí.  

4.  V případě **náhodného osazení**zadejte hodnotu, která se má použít jako počáteční hodnota pro náhodnost. Pokud zadáte 0 (výchozí), vygeneruje se číslo na základě systémových hodin.

     Hodnota počáteční hodnoty je volitelná, ale měli byste zadat hodnotu, pokud chcete reprodukovatelnost napříč běhy stejného kanálu.  

5.  Pro **metriku pro měření výkonu**vyberte jednu metriku, která se použije při výpočtu kvality modelu po permutaci.  

     Azure Machine Learning Designer podporuje následující metriky v závislosti na tom, zda vyhodnocujete model klasifikace nebo regrese:  

    -   **Klasifikace**

        Přesnost, přesnost, odvolání, průměrná ztráta protokolu  

    -   **Nevýhody**

        Přesnost, odvolání, Přibližná absolutní chyba, hlavní střední hodnota chyby, relativní absolutní chyba, relativní navýšení chyby, koeficient stanovitelnosti  

     Podrobnější popis těchto metrik vyhodnocení a jejich výpočtu najdete v tématu [vyhodnocení modelu](evaluate-model.md).  

6.  Spuštění kanálu  

7.  Modul vytvoří výstup seznamu sloupců funkcí a skóre, která jsou k nim přidružená, seřazené podle skóre, sestupné.  


##  <a name="technical-notes"></a>Technické poznámky

V této části najdete podrobné informace o implementaci, tipy a odpovědi na nejčastější dotazy.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Jak to dělá porovnání s jinými metodami výběru funkcí?

Funkce permutace: význam funguje náhodným změnou hodnot každého sloupce funkce, jednoho sloupce v čase a následným vyhodnocením modelu. 

Klasifikace funkce permutace je často odlišná od těch, které dostanete z [výběru funkce založeného na filtru](filter-based-feature-selection.md), který počítá skóre **před** vytvořením modelu. 

Důvodem je to, že důležitost funkcí permutace neměří přidružení mezi funkcí a cílovou hodnotou, ale místo toho zachycuje, jak velká mají jednotlivé funkce v předpovědi z modelu.
  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 
