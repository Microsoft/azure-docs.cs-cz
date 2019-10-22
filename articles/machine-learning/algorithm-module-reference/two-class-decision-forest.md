---
title: 'Rozhodovací doménová struktura se dvěma třídami: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat modul pro rozhodovací doménovou strukturu dvou tříd ve službě Azure Machine Learning k vytvoření modelu Machine Learning na základě algoritmu rozhodovací doménové struktury.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 243f1774069f048d0e8a1ce11e3ac42e4e73f58b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693627"
---
# <a name="two-class-decision-forest-module"></a>Modul doménové struktury se dvěma třídami

Tento článek popisuje modul vizuálního rozhraní (Preview) pro službu Azure Machine Learning.

Pomocí tohoto modulu můžete vytvořit model strojového učení založený na algoritmu rozhodovacích doménových struktur.  

Rozhodovací doménové struktury jsou rychlé a pod dohledem. Tento modul je dobrý volbou, pokud chcete předpovědět cíl s maximálním počtem dvou výsledků. 

## <a name="understanding-decision-forests"></a>Porozumění rozhodovacím strukturám

Tento algoritmus rozhodovací doménové struktury je vzdělávací metoda kompletování, která je určená pro úlohy klasifikace. Metody kompletování jsou založené na obecné zásadě, která se spíše nespoléhá na jeden model, můžete získat lepší výsledky a obecnější model tím, že vytvoříte několik souvisejících modelů a zkombinujete je nějakým způsobem. Obecně platí, že modely kompletování poskytují lepší pokrytí a přesnost než jednotlivé rozhodovací stromy. 

Existuje mnoho způsobů, jak vytvořit jednotlivé modely a kombinovat je do kompletu. Tato konkrétní implementace rozhodovací doménové struktury funguje tak, že sestaví více rozhodovacích stromů a pak bude **hlasovat** pro nejoblíbenější výstupní třídu. Hlasovací je jedna z lepších známých metod pro generování výsledků v modelu kompletu. 

+ Je vytvořeno mnoho jednotlivých stromů klasifikace, pomocí celé datové sady, ale různé (obvykle náhodné) počáteční body. To se liší od přístupu k náhodné doménové struktuře, ve kterém jednotlivé rozhodovací stromy můžou použít jenom náhodnou část dat nebo funkcí.
+ V každém stromu stromu rozhodovací doménové struktury je výstupem nenormalizovaného frekvenčního histogramu popisků. 
+ Agregační proces sečte Tyto histogramy a normalizuje výsledek pro získání "pravděpodobnosti" pro každý popisek. 
+ V konečném rozhodnutí kompletu budou mít stromy s jistotou vysoké předpovědi větší váhu.

Obecné rozhodovací stromy mají pro úlohy klasifikace mnoho výhod:
  
- Můžou zachytit hranice nelineárního rozhodování.
- Můžete vyškolit a odhadnout spoustu dat, protože jsou efektivní při výpočtech a využití paměti.
- Výběr funkcí je integrovaný do procesů školení a klasifikace.  
- Stromy mohou pojmout data o velikosti a množství funkcí.  
- Jsou to modely nevyužívající parametry, což znamená, že mohou zpracovávat data s proměnlivými distribucí. 

Jednoduché rozhodovací stromy ale mohou overfit data a jsou méně generalizované než struktury stromů.

Další informace najdete v tématu o [rozhodovacích doménových strukturách](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Jak nakonfigurovat
  
1.  Přidejte modul pro **rozhodovací doménovou strukturu dvou tříd** do kanálu v Azure Machine Learning a otevřete podokno **vlastnosti** daného modulu. 

    Modul najdete v části **Machine Learning**. Rozbalte položku **Initialize**a pak **klasifikaci**.  
  
2.  V případě **metody převzorkování**vyberte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z **zavazadla** nebo **replikace**.  
  
    -   Zaznamenání **do zavazadla**se také označuje jako *agregace Bootstrap*. V této metodě se každý strom vypěstuje na novém vzorku, který vytvořil náhodným vzorkováním původní datové sady s náhradou, dokud nebudete mít datovou velikost původní.  
  
         Výstupy modelů jsou kombinovány *hlasováním*, což je forma agregace. Každý strom v doménové struktuře rozhodnutí o klasifikaci má za následek nenormalizovaný frekvenční histogram popisků. Agregaci je sečíst Tyto histogramy a normalizovat a získat tak "pravděpodobnost" pro každý popisek. Tímto způsobem budou mít stromy s důvěrou vysoké předpovědi větší váhu v konečném rozhodnutí kompletu.  
  
         Další informace najdete v tématu Wikipedii pro agregaci Bootstrap.  
  
    -   **Replikace: v**rámci replikace je každý strom vyškolen na přesně stejných vstupních datech. Určení, které dělicí predikát se používá pro každý uzel stromu, zůstane náhodné a stromy budou odlišné.   
  
3.  Určete, jak chcete model vyškolet nastavením možnosti **vytvořit Trainer režim** .  
  
    -   **Jediný parametr**: Pokud víte, jak chcete model konfigurovat, můžete zadat konkrétní sadu hodnot jako argumenty.
  
4.  V poli **počet rozhodovacích stromů**zadejte maximální počet rozhodovacích stromů, které lze v kompletu vytvořit. Vytvořením dalších rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale zvýší se čas školení.  
  
    > [!NOTE]
    >  Tato hodnota také určuje počet stromů zobrazených při vizualizaci výukového modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu 1. Může však být vytvořen pouze jeden strom (strom s počáteční sadou parametrů) a žádné další iterace.
  
5.  Pro **maximální hloubku rozhodovacích stromů**zadejte číslo, abyste omezili maximální hloubku rozhodovacího stromu. Zvýšení hloubky stromové struktury může zvýšit přesnost, a to na riziko nějakého přeložení a zvýšené doby školení.
  
6.  Pro **počet náhodných rozdělení na uzel**zadejte počet rozdělení, který se má použít při sestavování jednotlivých uzlů stromu. *Rozdělení* znamená, že funkce v každé úrovni stromu (uzlu) jsou náhodně děleny.
  
7.  Pro **minimální počet vzorků na uzel listu**Určete minimální počet případů, které jsou nutné k vytvoření libovolného uzlu terminálu (list) ve stromu.
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například výchozí hodnota 1, dokonce i jeden případ, může způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, musí školicí data obsahovat alespoň pět případů splňujících stejné podmínky.  
  
8.  Vyberte možnost **Povolení neznámých hodnot pro funkce kategorií** a vytvořte skupinu pro neznámé hodnoty v rámci školicích nebo ověřovacích sad. Model může být pro známé hodnoty méně přesný, ale může poskytovat lepší předpovědi pro nové (neznámé) hodnoty. 

     Pokud zrušíte výběr této možnosti, může model přijímat pouze hodnoty, které jsou obsaženy v školicích datech.
  
9. Připojte s popiskem datovou sadu a jeden z [školicích modulů](module-reference.md):  
  
    -   Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijte modul [vlakového modelu](./train-model.md) .  
  
    
## <a name="results"></a>Výsledky

Po dokončení školení:

+ Chcete-li zobrazit strom, který byl vytvořen při každé iteraci, klikněte pravým tlačítkem na výstup modulu [vlakového modelu](./train-model.md) a vyberte **vizualizovat**.
  
    Kliknutím na jednotlivé stromové struktury přejdete k podrobnostem o rozdělení a uvidíte pravidla pro každý uzel.

+ Snímek modelu uložíte tak, že kliknete pravým tlačítkem na výstup **trained model** a vyberete **Uložit model**. Uložený model není aktualizován při následných spuštěních kanálu.

+ Chcete-li použít model pro bodování, přidejte modul určení **skóre modelu** do kanálu.


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 