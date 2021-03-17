---
title: 'Model vzájemného ověřování: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Pomocí modulu model křížového ověřování v Návrháři Azure Machine Learning můžete vyhodnocovat odhady parametrů pro klasifikace nebo regresní modely.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421341"
---
# <a name="cross-validate-model"></a>Křížové ověření modelu

Tento článek popisuje, jak použít modul pro různé ověřování modelu v Návrháři Azure Machine Learning. *Křížové ověřování* je technika často používaná ve službě Machine Learning k vyhodnocení proměnlivosti datové sady a spolehlivosti všech modelů vyškolených prostřednictvím těchto dat.  

Modul pro různé ověřování modelu přijímá jako vstupní datovou sadu s popisem, společně s nevlakovou klasifikací nebo regresní model. Rozdělí datovou sadu na určitý počet podmnožin ( *skládání* ), sestaví model na každé přeložení a potom vrátí sadu statistik přesnosti pro každé skládání. Porovnáním statistik přesnosti pro všechna skládání můžete interpretovat kvalitu sady dat. Pak můžete pochopit, jestli je model náchylný k proměnlivosti dat.  

Model vzájemného ověřování také vrací předpovězené výsledky a pravděpodobnosti pro datovou sadu, abyste mohli posoudit spolehlivost předpovědi.  

### <a name="how-cross-validation-works"></a>Jak funguje křížové ověřování

1. Křížové ověřování náhodně dělí školicí data na skládání. 

   Pokud jste datovou sadu předtím nevytvořili, algoritmus se standardně přeloží na 10. Chcete-li datovou sadu rozdělit na jiný počet přeložení, můžete použít [oddíl a vzorový](partition-and-sample.md) modul a určit, kolik skládání se má použít.  

2.  Modul nastavuje data přeložená 1 pro ověření. (To se někdy označuje jako *skládání blokování*.) Modul využívá zbývající skládání ke výukě modelu. 

    Například pokud vytvoříte pět přeložení, modul generuje během křížového ověřování pět modelů. Modul navlakuje každý model pomocí čtyř pětiny dat. Testuje každý model na zbylé jedné pětině.  

3.  Během testování modelu pro každé přeložení modul vyhodnocuje více statistik přesnosti. Které statistiky modul používá, závisí na typu modelu, který budete vyhodnocovat. Pro vyhodnocení modelů klasifikace vs. regresních modelů se používají různé statistiky.  

4.  Když je proces sestavení a vyhodnocení dokončen pro všechny skládání, model vzájemného ověřování vygeneruje sadu metrik výkonu a výsledky skóre pro všechna data. Projděte si tyto metriky, abyste viděli, zda má jedno přeložení vysokou nebo nízkou přesnost. 

### <a name="advantages-of-cross-validation"></a>Výhody křížového ověřování

Různými a běžným způsobem, jak vyhodnocovat model, je rozdělit data do sady školení a testování pomocí [rozdělených dat](split-data.md)a pak ověřit model školicích dat. Ale křížové ověřování nabízí některé výhody:  

-   Křížové ověření používá více testovacích dat.

    Křížové ověřování měří výkon modelu se zadanými parametry ve větším datovém prostoru. To znamená, že křížové ověřování používá celou výukovou datovou sadu pro školení a vyhodnocení místo části. Naproti tomu, pokud ověřujete model pomocí dat generovaných náhodným rozdělením, obvykle vyhodnocujete model pouze 30 procent nebo méně dostupných dat.  

    Vzhledem k tomu, že vlaky s křížovým ověřováním a ověřují model víckrát nad větší datovou sadou, je mnohem efektivnější výpočetní výkon. Bude trvat mnohem déle než ověřování při náhodném rozdělení.  

-   Křížové ověřování vyhodnocuje jak datovou sadu, tak i model.

    Křížové ověřování nedokáže jednoduše změřit přesnost modelu. Poskytuje vám také informace o tom, jak je základní datovou sadou a jak citlivá model může být variace v datech.  

## <a name="how-to-use-cross-validate-model"></a>Jak používat model vzájemného ověřování

Spuštění křížového ověřování může trvat dlouhou dobu, pokud je datová sada velká.  Model vzájemného ověřování proto můžete použít v počáteční fázi vytváření a testování modelu. V této fázi můžete vyhodnotit dobré parametry modelu (za předpokladu, že doba výpočtu je přípustná). Potom můžete model vyhodnotit a vyhodnocovat pomocí navázaných parametrů s [modelem vlaků](train-model.md) a [vyhodnocovat moduly modelů](evaluate-model.md) .

V tomto scénáři provedete a otestujete model pomocí modelu vzájemného ověřování.

1. Přidejte do svého kanálu modul model vzájemného ověřování. Můžete ji najít v Návrháři Azure Machine Learning v kategorii **hodnocení modelu & vyhodnocení** . 

2. Připojte výstup jakéhokoli modelu klasifikace nebo regrese. 

    Například pokud používáte dva třídy pro vyhodnocení **rozhodovacího stromu** pro klasifikaci, nakonfigurujte model pomocí parametrů, které chcete. Pak přetáhněte konektor z **nevýukového portu modelu** klasifikátoru na shodný port modelu křížového ověření. 

    > [!TIP] 
    > Model nemusíte procházet, protože model křížového ověřování ho automaticky nastavil jako součást vyhodnocení.  
3.  V portu **DataSet** modelu křížového ověřování připojte všechny datové sady s popiskem.  

4.  V pravém panelu modelu vzájemného ověřování klikněte na **Upravit sloupec**. Vyberte jeden sloupec, který obsahuje popisek třídy, nebo předvídatelné hodnoty. 

5. Nastavte hodnotu **náhodného parametru počátečního** navýšení, pokud chcete výsledky křížového ověřování opakovat v rámci souběžných běhů na stejná data.  

6. Odešlete kanál.

7. Popis sestav najdete v části s [výsledky](#results) .

## <a name="results"></a>Výsledky

Po dokončení všech iterací model vzájemného ověřování vytvoří skóre pro celou datovou sadu. Také vytváří metriky výkonu, které můžete použít k vyhodnocení kvality modelu.

### <a name="scored-results"></a>Výsledky skóre

První výstup modulu poskytuje zdrojová data pro každý řádek spolu s některými předpovězenými hodnotami a souvisejícími pravděpodobnostmi. 

Pokud chcete zobrazit výsledky, klikněte v kanálu pravým tlačítkem na modul modelu křížového ověřování. Vyberte **Vizualizace výsledků skóre**.

| Nový název sloupce      | Popis                              |
| -------------------- | ---------------------------------------- |
| Vyhodnocené popisky        | Tento sloupec se přidá na konec datové sady. Obsahuje předpokládanou hodnotu pro každý řádek. |
| Pravděpodobnost skóre | Tento sloupec se přidá na konec datové sady. Označuje odhadovanou pravděpodobnost hodnoty v **popiscích skóre**. |
| Číslo skládání          | Určuje index přeložení založený na nule, ke kterému byly při křížovém ověření přiřazeny jednotlivé řádky dat. |

 ### <a name="evaluation-results"></a>Výsledky vyhodnocení

Druhá sestava je seskupena podle skládání. Mějte na paměti, že při provádění model vzájemného ověřování náhodně rozdělí data školení na *n* skládání (ve výchozím nastavení 10). V každé iteraci přes datovou sadu používá model vzájemného ověřování jednu skládání jako datovou sadu. Pomocí zbývajících *n-1* skládání vytvoří výuku modelu. Každý z modelů *n* je testován proti datům ve všech ostatních skládáních.

V této sestavě jsou skládání uvedena podle hodnoty indexu ve vzestupném pořadí.  Chcete-li seřadit v jakémkoli jiném sloupci, můžete výsledky Uložit jako datovou sadu.

Pokud chcete zobrazit výsledky, klikněte v kanálu pravým tlačítkem na modul modelu křížového ověřování. Vyberte **vizualizovat výsledky vyhodnocení podle skládání**.


|Název sloupce| Popis|
|----|----|
|Číslo skládání| Identifikátor pro každé skládání. Pokud jste vytvořili pět přeložení, bude se jednat o pět podmnožin dat, očíslované od 0 do 4.
|Počet příkladů v skládání|Počet řádků přiřazených každému skládání. Měly by být zhruba stejné. |


Modul obsahuje také následující metriky pro každé skládání v závislosti na typu modelu, který vyhodnocujete: 

+ **Modely klasifikace** : přesnost, odvolání, F-SKORE, AUC, přesnost  

+ **Regresní modely** : střední chyba, hlavní střední hodnota, chyba, relativní absolutní chyba, relativní natažená chyba a koeficient určení


## <a name="technical-notes"></a>Technické poznámky  

+ Je osvědčeným postupem normalizovat datové sady předtím, než je použijete pro křížové ověřování. 

+ Model vzájemného ověřování je mnohem více výpočetně náročný a trvá déle, než kdybyste ověřili model pomocí náhodně rozdělené datové sady. Důvodem je, že model vzájemného ověřování navlaky a ověřuje model víckrát.

+ Není nutné rozdělit datovou sadu do školicích a testovacích sad při použití křížového ověřování pro měření přesnosti modelu. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 

