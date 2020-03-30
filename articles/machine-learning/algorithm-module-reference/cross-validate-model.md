---
title: 'Model křížového ověření: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Zjistěte, jak pomocí modulu Model křížového ověření v Azure Machine Learning křížově ověřit odhady parametrů pro klasifikační nebo regresní modely rozdělením dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 7550bb7c6bbf7602245f9a9f1ac006ce693b36a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477642"
---
# <a name="cross-validate-model"></a>Křížové ověření modelu

Tento článek popisuje, jak používat modul Model křížového ověření v návrháři Azure Machine Learning (preview). *Křížové ověřování* je technika, která se často používá ve strojovém učení k posouzení variability datové sady i spolehlivosti jakéhokoli modelu trénovaného prostřednictvím těchto dat.  

Modul Model křížového ověření přebírá jako vstup označenou datovou sadu spolu s netrénovaným klasifikačním nebo regresním modelem. Rozdělí datovou sadu na určitý počet podmnožiny (*záhyby*), vytvoří model na každém záhybu a pak vrátí sadu statistik přesnosti pro každý záhyb. Porovnáním statistiky přesnosti pro všechny záhyby můžete interpretovat kvalitu sady dat. Potom můžete pochopit, zda je model náchylný k odchylkám v datech.  

Model křížového ověření také vrátí předpokládané výsledky a pravděpodobnosti pro datovou sadu, takže můžete posoudit spolehlivost předpovědi.  

### <a name="how-cross-validation-works"></a>Jak funguje křížové ověřování

1. Křížové ověření náhodně rozděluje trénovací data do záhybů. 

   Algoritmus výchozí 10 krát, pokud jste dříve rozdělena datové sady. Chcete-li rozdělit datovou sadu na jiný počet záhybů, můžete použít modul [Oddíl a Ukázkový](partition-and-sample.md) a určit, kolik záhybů se má použít.  

2.  Modul vyčlení data v záhybu 1 použít pro ověření. (To se někdy nazývá *holdout fold*.) Modul používá zbývající záhyby k trénování modelu. 

    Pokud například vytvoříte pětkrát, modul generuje pět modelů během křížového ověření. Modul trénuje každý model pomocí čtyř pětin dat. Testuje každý model na zbývající jedné pětině.  

3.  Během testování modelu pro každý záhyb modul vyhodnocuje více statistik přesnosti. Statistiky, které modul používá, závisí na typu modelu, který vyhodnocujete. Různé statistiky se používají k vyhodnocení klasifikačních modelů versus regresních modelů.  

4.  Po dokončení procesu vytváření a hodnocení pro všechny záhyby, křížová ověření modelu generuje sadu metrik výkonu a skóre výsledky pro všechna data. Zkontrolujte tyto metriky a zjistěte, zda má některý jednotlivý záhyb vysokou nebo nízkou přesnost. 

### <a name="advantages-of-cross-validation"></a>Výhody křížového ověřování

Jiný a běžný způsob vyhodnocení modelu je rozdělit data do trénování a testovací sady pomocí [split data](split-data.md)a potom ověřit model na trénovací data. Ale křížové ověření nabízí některé výhody:  

-   Křížové ověření používá více testovacích dat.

    Křížové ověření měří výkon modelu se zadanými parametry ve větším datovém prostoru. To znamená, že křížové ověření používá celou trénovací datovou sadu pro školení i hodnocení, namísto části. Naopak pokud ověříte model pomocí dat generovaných z náhodného rozdělení, obvykle vyhodnotíte model pouze na 30 procent nebo méně dostupných dat.  

    Však vzhledem k tomu, že křížové ověření vlaky a ověřuje model vícekrát přes větší datové sady, je mnohem více výpočetní náročné. Trvá mnohem déle než ověření na náhodné rozdělení.  

-   Křížové ověřování vyhodnocuje datovou sadu i model.

    Křížové ověření neměří pouze přesnost modelu. Poskytuje také určitou představu o tom, jak reprezentativní je datová sada a jak citlivý může být model na změny v datech.  

## <a name="how-to-use-cross-validate-model"></a>Jak používat model křížového ověření

Křížové ověření může trvat dlouhou dobu, pokud je vaše datová sada velká.  Takže můžete použít cross validate model v počáteční fázi vytváření a testování modelu. V této fázi můžete vyhodnotit dobrotu parametrů modelu (za předpokladu, že výpočetní čas je přípustný). Potom můžete trénování a vyhodnocení modelu pomocí zavedených parametrů s [modulech Model vlaku](train-model.md) a [Vyhodnotit model.](evaluate-model.md)

V tomto scénáři trénujete a otestujete model pomocí modelu křížového ověření.

1. Přidejte do kanálu modul Model křížového ověření. Najdete ji v návrháři Azure Machine Learning, v kategorii **Hodnocení hodnocení modelu &.** 

2. Připojte výstup libovolné klasifikace nebo regresní model. 

    Například pokud používáte **dvě třídy posílené rozhodovací strom** pro klasifikaci, nakonfigurujte model s parametry, které chcete. Potom přetáhněte spojnici z portu **modelu Untrained** třídění na odpovídající port modelu křížovéověření. 

    > [!TIP] 
    > Není třeba trénovat model, protože cross-validate model automaticky trénuje model jako součást hodnocení.  
3.  Na portu **datové sady** modelu křížového ověření připojte libovolnou taveřnou datovou sadu s popiskem.  

4.  V pravém panelu modelu křížového ověření klepněte na **upravit sloupec**. Vyberte jeden sloupec, který obsahuje popisek třídy nebo předvídatelnou hodnotu. 

5. Pokud chcete opakovat výsledky křížového ověření napříč jednotlivými spuštěními na stejných datech, nastavte hodnotu parametru **Random seed.**  

6. Odešlete potrubí.

7. Popis sestav naleznete v části [Výsledky.](#results)

## <a name="results"></a>Výsledky

Po dokončení všech iterací cross validate model vytvoří skóre pro celou datovou sadu. Také vytvoří metriky výkonu, které můžete použít k posouzení kvality modelu.

### <a name="scored-results"></a>Výsledky hodnocení

První výstup modulu poskytuje zdrojová data pro každý řádek spolu s některými předpovídanými hodnotami a souvisejícími pravděpodobnostmi. 

Chcete-li zobrazit výsledky, v kanálu klikněte pravým tlačítkem myši na modul Křížové ověření modelu. Vyberte **možnost Vizuace výsledků s hodnocením**.

| Nový název sloupce      | Popis                              |
| -------------------- | ---------------------------------------- |
| Vyhodnocené popisky        | Tento sloupec je přidán na konec datové sady. Obsahuje předpovídanou hodnotu pro každý řádek. |
| Pravděpodobnosti skóre | Tento sloupec je přidán na konec datové sady. Označuje odhadovanou pravděpodobnost hodnoty v **skórované popisky**. |
| Sklopné číslo          | Označuje nulový index přeložení, ke kterému byl přiřazen každý řádek dat během křížového ověření. |

 ### <a name="evaluation-results"></a>Výsledky vyhodnocení

Druhá sestava je seskupena podle záhybů. Nezapomeňte, že během provádění křížová ověření modelu náhodně rozdělí trénovací data do *n* záhybů (ve výchozím nastavení 10). V každé iteraci přes datovou sadu model křížového ověření používá jednou jako ověřovací datovou sadu. Používá zbývající *n-1* záhyby trénovat model. Každý z *modelů n* je testován proti datům ve všech ostatních záhybech.

V této sestavě jsou přeložení uvedena podle hodnoty indexu ve vzestupném pořadí.  Chcete-li objednat v libovolném jiném sloupci, můžete výsledky uložit jako datovou sadu.

Chcete-li zobrazit výsledky, v kanálu klikněte pravým tlačítkem myši na modul Křížové ověření modelu. Vyberte **možnost Vizuace vyhodnocení výsledky přeložením**.


|Název sloupce| Popis|
|----|----|
|Číslo přeložení| Identifikátor pro každý záhyb. Pokud jste vytvořili pět krát, bude existovat pět podmnožiny dat, číslované 0 až 4.
|Počet příkladů v záhybu|Počet řádků přiřazených ke každému záhybu. Měly by být zhruba stejné. |


Modul také obsahuje následující metriky pro každý záhyb, v závislosti na typu modelu, který vyhodnocujete: 

+ **Klasifikační modely**: Přesnost, stažení, F-skóre, AUC, přesnost  

+ **Regresní modely**: Střední absolutní chyba, střední kvadratická chyba kořene, relativní absolutní chyba, relativní kvadratří chyba a koeficient stanovení


## <a name="technical-notes"></a>Technické poznámky  

+ Je osvědčeným postupem normalizovat datové sady před jejich použitím pro křížové ověření. 

+ Křížová ověření modelu je mnohem více výpočetní náročné a trvá déle, než pokud jste ověřili model pomocí náhodně rozdělené datové sady. Důvodem je, že cross validate model vlaky a ověří model vícekrát.

+ Při použití křížového ověřování k měření přesnosti modelu není nutné rozdělit datovou sadu na trénovací a testovací sady. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

