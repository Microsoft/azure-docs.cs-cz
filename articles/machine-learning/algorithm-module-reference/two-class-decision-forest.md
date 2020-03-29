---
title: 'Dvoutřídní rozhodovací doménová struktura: Odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Two-Class Decision Forest v Azure Machine Learning vytvořit model strojového učení založený na algoritmu rozhodovacích doménových struktur.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916706"
---
# <a name="two-class-decision-forest-module"></a>Modul dvoutřídního rozhodovacího lesa

Tento článek popisuje modul v návrháři Azure Machine Learning (preview).

Tento modul slouží k vytvoření modelu strojového učení založeného na algoritmu rozhodovacích doménových struktur.  

Rozhodovací lesy jsou rychlé, pod dohledem soubormodely. Tento modul je dobrou volbou, pokud chcete předpovědět cíl s maximálně dvěma výsledky. 

## <a name="understanding-decision-forests"></a>Principy rozhodovacích doménových struktur

Tento algoritmus rozhodovací lesy je metoda učení souboru určená pro klasifikační úkoly. Ensemble metody jsou založeny na obecném principu, že spíše než spoléhat na jeden model, můžete získat lepší výsledky a obecnější model vytvořením více souvisejících modelů a jejich kombinací nějakým způsobem. Obecně platí, že modely souborů poskytují lepší pokrytí a přesnost než stromy s jedním rozhodnutím. 

Existuje mnoho způsobů, jak vytvořit jednotlivé modely a kombinovat je v souboru. Tato konkrétní implementace rozhodnutí lesa pracuje tím, že staví více rozhodovacích stromů a pak **hlasování** o nejoblíbenější výstupní třídy. Hlasování je jednou z nejznámějších metod pro generování výsledků v souborovém modelu. 

+ Mnoho jednotlivých klasifikačních stromů jsou vytvořeny pomocí celé datové sady, ale různé (obvykle randomizované) výchozí body. To se liší od přístupu náhodné doménové struktury, ve kterém jednotlivé rozhodovací stromy mohou používat pouze některé randomizované části dat nebo funkcí.
+ Každý strom v rozhodovací doménové struktuře vynese nenormalizovaný frekvenční histogram popisků. 
+ Proces agregace sčítá tyto histogramy a normalizuje výsledek získat "pravděpodobnosti" pro každý popisek. 
+ Stromy, které mají vysokou predikci důvěru bude mít větší váhu v konečném rozhodnutí souboru.

Rozhodovací stromy obecně mají mnoho výhod pro klasifikační úkoly:
  
- Mohou zachytit nelineární hranice rozhodnutí.
- Můžete trénovat a předpovídat na velké množství dat, protože jsou efektivní při výpočtu a využití paměti.
- Výběr funkcí je integrován do procesů školení a klasifikace.  
- Stromy mohou pojmout hlučná data a mnoho funkcí.  
- Jedná se o neparametrické modely, což znamená, že mohou zpracovávat data s různými distribucemi. 

Jednoduché rozhodovací stromy se však mohou na data přizpůsobit a jsou méně generalizovatelné než stromové soubory.

Další informace naleznete v [tématu Decision Forests](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Jak nakonfigurovat
  
1.  Přidejte modul **Dvoutřídní rozhodovací doménové struktury** do kanálu v Azure Machine Learning a otevřete podokno **Vlastnosti** modulu. 

    Modul najdete v části **Machine Learning**. Rozbalte **inicializovat**a potom **klasifikaci**.  
  
2.  V **případě metody převzorkování**zvolte metodu použitou k vytvoření jednotlivých stromů.  Můžete si vybrat z **pytlování** nebo **replikovat**.  
  
    -   **Pytlování**: Pytlování se také nazývá *bootstrap agregace*. V této metodě je každý strom pěstován na nové vzorku, vytvořené náhodným vzorkováním původní datové sady s nahrazením, dokud nemáte datovou sadu velikost originálu.  
  
         Výstupy modelů jsou kombinovány *hlasováním*, což je forma agregace. Každý strom v rozhodovací oblasti klasifikace doménové struktury výstupy nenormalizované frekvence histogram popisků. Agregace je součet těchto histogramů a normalizovat získat "pravděpodobnosti" pro každý popisek. Tímto způsobem budou mít stromy, které mají vysokou predikci důvěry, větší váhu v konečném rozhodnutí souboru.  
  
         Další informace naleznete v položce Wikipedie pro agregaci Bootstrap.  
  
    -   **Replikovat**: Při replikaci je každý strom trénován na přesně stejná vstupní data. Určení, který rozdělený predikát se používá pro každý uzel stromu zůstává náhodný a stromy budou rozmanité.   
  
3.  Určete, jak má být model trénovaný, nastavením **možnosti Vytvořit režim trenažéru.**  
  
    -   **Jeden parametr**: Pokud víte, jak chcete nakonfigurovat model, můžete zadat určitou sadu hodnot jako argumenty.

    -   **Rozsah parametrů**: Pokud si nejste jisti nejlepšími parametry, můžete najít optimální parametry pomocí modulu [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Zadáte určitý rozsah hodnot a trenér iterates přes více kombinací nastavení k určení kombinace hodnot, které vytváří nejlepší výsledek.
  
4.  Do **pole Počet rozhodovacích stromů**zadejte maximální počet rozhodovacích stromů, které lze v souboru vytvořit. Vytvořením více rozhodovacích stromů můžete potenciálně získat lepší pokrytí, ale doba tréninku se zvyšuje.  
  
    > [!NOTE]
    >  Tato hodnota také určuje počet stromů zobrazených při vizualizaci trénovaného modelu. Pokud chcete zobrazit nebo vytisknout jeden strom, můžete nastavit hodnotu na 1. Však pouze jeden strom může být vytvořena (strom s počáteční sadou parametrů) a žádné další iterace jsou prováděny.
  
5.  Do **pole Maximální hloubka stromů rozhodnutí**zadejte číslo, které omezí maximální hloubku libovolného rozhodovacího stromu. Zvýšení hloubky stromu může zvýšit přesnost, s rizikem nadměrného vybavení a prodloužení doby tréninku.
  
6.  Do **pole Počet náhodných rozdělení na uzel**zadejte počet rozdělení, které se mají použít při vytváření jednotlivých uzlů stromu. *Rozdělení* znamená, že prvky v každé úrovni stromu (uzlu) jsou náhodně rozděleny.
  
7.  Pro **minimální počet vzorků na uzel listu**, uveďte minimální počet případů, které jsou nutné k vytvoření libovolného koncového uzlu (listu) ve stromu.
  
     Zvýšením této hodnoty zvýšíte prahovou hodnotu pro vytváření nových pravidel. Například s výchozí hodnotou 1 může i jeden případ způsobit vytvoření nového pravidla. Pokud zvýšíte hodnotu na 5, data školení by musel obsahovat alespoň pět případů, které splňují stejné podmínky.  
  
8.  Vyberte možnost **Povolit neznámé hodnoty pro kategorické funkce** a vytvořte skupinu pro neznámé hodnoty v trénovacích nebo ověřovacích sadách. Model může být méně přesné pro známé hodnoty, ale může poskytnout lepší předpovědi pro nové (neznámé) hodnoty. 

     Pokud tuto volbu zrušíte, model může přijmout pouze hodnoty, které jsou obsaženy v trénovacích datech.
  
9. Připojte označenou datovou sadu a jeden z [trénovacích modulů](module-reference.md):  
  
    -   Pokud nastavíte **vytvořit režim trenažéru** na **jeden parametr**, použijte modul [Model vlaku.](./train-model.md)  
    
## <a name="results"></a>Výsledky

Po dokončení tréninku:

+ Chcete-li uložit snímek trénovaného modelu, vyberte kartu **Výstupy** v pravém panelu modulu **modelu Vlak.** Vyberte ikonu **Registrovat datovou sadu,** chcete-li model uložit jako opakovaně použitelný modul.

+ Chcete-li použít model pro vyhodnocování, přidejte modul **Modelu skóre** do kanálu.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 