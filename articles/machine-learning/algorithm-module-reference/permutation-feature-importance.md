---
title: 'Permutace – funkce důležitost: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak používat modul důležitost funkcí permutace v Azure Machine Learning k výpočtu skóre důležitosti funkcí funkcí, s ohledem na vyškolený model a testovací datovou sadu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: ecf739ea838ad3742612eab7a1b42ac8c8ac455a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905261"
---
# <a name="permutation-feature-importance"></a>Důležitost funkce permutací

Tento článek popisuje, jak použít modul důležitost funkcí permutace v Návrháři Azure Machine Learning k výpočtu sady výsledků důležitosti funkcí pro vaši datovou sadu. Tato skóre vám pomůžou určit nejlepší funkce pro použití v modelu.

V tomto modulu jsou hodnoty funkcí náhodně přemístěné, vždy v jednom sloupci. Výkon modelu je měřen před a po. Můžete zvolit jednu ze standardních metrik pro měření výkonu.

Skóre, které modul vrací, představuje *změnu* výkonu pro vyškolený model po permutaci. Důležité funkce jsou obvykle citlivější na proces náhodného zpracování, takže budou mít výsledek vyšší důležitost. 

Tento článek poskytuje přehled funkce permutace, její teoretickou a její aplikace ve službě Machine Learning: [důležitost funkce permutace](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Použití funkce permutace důležitost

Generování sady výsledků funkcí vyžaduje, abyste měli už vyškolený model i testovací datovou sadu.  

1.  Přidejte modul důležitost funkce permutace do vašeho kanálu. Tento modul můžete najít v kategorii **výběru funkcí** . 

2.  Připojte k levému vstupu vyškolený model. Model musí být regresní model nebo klasifikační model.  

3.  U správného vstupu připojte datovou sadu. V takovém případě vyberte, který se liší od datové sady, kterou jste použili pro školení modelu. Tato datová sada se používá pro hodnocení na základě trained model. Používá se také k vyhodnocení modelu po změně hodnot vlastností.  

4.  V případě **náhodného osazení**zadejte hodnotu, která se má použít jako počáteční hodnota pro náhodnost. Pokud zadáte 0 (výchozí), vygeneruje se číslo na základě systémových hodin.

     Hodnota počáteční hodnoty je volitelná, ale měli byste zadat hodnotu, pokud chcete reprodukovatelnost napříč běhy stejného kanálu.  

5.  Pro **metriku pro měření výkonu**vyberte jednu metriku, která se má použít, když počítáte kvalitu modelu po permutaci.  

     Azure Machine Learning Designer podporuje následující metriky v závislosti na tom, zda vyhodnocujete model klasifikace nebo regrese:  

    -   **Classification**

        Přesnost, přesnost, odvolání  

    -   **Regrese**

        Přesnost, odvolání, Přibližná absolutní chyba, hlavní střední hodnota chyby, relativní absolutní chyba, relativní navýšení chyby, koeficient stanovitelnosti  

     Podrobnější popis těchto metrik vyhodnocení a způsobu jejich výpočtu najdete v tématu [vyhodnocení modelu](evaluate-model.md).  

6.  Odešlete kanál.  

7.  Modul vytvoří výstup seznamu sloupců funkcí a skóre, která jsou k nim přidružená. Seznam je seřazen v sestupném pořadí podle skóre.  


##  <a name="technical-notes"></a>Technické poznámky

Význam funkce permutace funguje tak, že náhodně změní hodnoty každého sloupce funkce, jeden sloupec v daném okamžiku. Pak vyhodnotí model. 

Klasifikace, kterou modul poskytuje, se často liší od těch, které získáte z [výběru funkcí založených na filtrech](filter-based-feature-selection.md). Výběr funkce založený na filtrech vypočítá skóre *před* vytvořením modelu. 

Důvodem rozdílu je, že důležitost funkcí permutace neměří přidružení mezi funkcí a cílovou hodnotou. Místo toho zachycuje, jaký vliv má každá funkce na předpovědi z modelu.
  
## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 
