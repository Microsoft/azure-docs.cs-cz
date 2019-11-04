---
title: 'Model vzájemného ověřování: odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Naučte se používat model křížového ověřování ve službě Azure Machine Learning ke vzájemné kontrole odhadů parametrů pro klasifikace nebo regresní modely pomocí dělení dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512844"
---
# <a name="cross-validate-model"></a>Model vzájemného ověřování

Tento článek popisuje, jak používat modul pro **různé ověřování modelu** v Návrháři Azure Machine Learning (Preview). *Křížové ověřování* je důležitou technikou, která se často používá ve službě Machine Learning k vyhodnocení proměnlivosti datové sady a spolehlivosti všech modelů vyškolených pomocí těchto dat.  

Modul pro **různé ověřování modelu** přijímá jako vstupní datovou sadu s popisem, společně s nevlakovou klasifikací nebo regresní model. Rozdělí datovou sadu na určitý počet podmnožin (*skládání*), sestaví model na každé přeložení a potom vrátí sadu statistik přesnosti pro každé skládání. Porovnáním statistik přesnosti pro všechna skládání můžete interpretovat kvalitu sady dat a pochopit, zda je model náchylný k variacím dat.  

Křížové ověřování také vrací předpovězené výsledky a pravděpodobnosti pro datovou sadu, abyste mohli posoudit spolehlivost předpovědi.  

### <a name="how-cross-validation-works"></a>Jak funguje vzájemné ověřování

1. Vzájemné ověřování náhodně dělí školicí data do několika oddílů, označovaných také jako *skládání*. 

    + Pokud jste datovou sadu předtím nevytvořili, algoritmus se standardně přeloží na 10. 
    + Chcete-li datovou sadu rozdělit na jiný počet přeložení, můžete použít [oddíl a vzorový](partition-and-sample.md) modul a určit, kolik skládání se má použít.  

2.  Modul odloží data přeložená 1, aby se použila k ověření (Tato situace se někdy označuje jako *přeložení*), a používá zbývající skládání k vytvoření výukového modelu. 

    Například pokud vytvoříte pět přeložení, modul by během křížového ověřování vygeneroval pět modelů, každý model byl vyučen pomocí 4/5 dat a testován na zbývajících 1/5.  

3.  Během testování modelu pro každé skládání se vyhodnocuje více statistik přesnosti. Které statistiky jsou používány, závisí na typu modelu, který vyhodnocujete. Pro vyhodnocení modelů klasifikace vs. regresních modelů se používají různé statistiky.  

4.  Když je proces sestavení a vyhodnocení dokončen pro všechny skládání, **model vzájemného ověřování** vygeneruje sadu metrik výkonu a výsledky skóre pro všechna data. Tyto metriky byste měli zkontrolovat a zjistit, zda některé z nich mají zvláště vysokou nebo nízkou přesnost. 

### <a name="advantages-of-cross-validation"></a>Výhody vzájemného ověřování

Jiný a běžný způsob, jak vyhodnotit model, je rozdělit data do školicích a testovacích sad pomocí [rozdělených dat](split-data.md)a pak ověřit model školicích dat. Křížové ověřování však nabízí některé výhody:  

-   Křížové ověření používá více testovacích dat.

     Křížové ověřování měří výkon modelu se zadanými parametry ve větším datovém prostoru. To znamená, že křížové ověřování používá celou výukovou datovou sadu pro školení a vyhodnocení, a ne na určitou část. Naproti tomu, pokud ověřujete model pomocí dat generovaných náhodným rozdělením, obvykle vyhodnocujete model pouze 30% nebo méně dostupných dat.  

     Vzhledem k tomu, že vlaky pro křížové ověřování a ověřují model víckrát nad větší datovou sadou, je mnohem více výpočetně náročný a trvá mnohem déle než ověřování při náhodném rozdělení.  

-   Křížové ověřování vyhodnocuje datovou sadu i model.

     Křížové ověřování neměří pouhou přesnost modelu, ale také poskytuje určitou představu o tom, jak je datová sada a jak citlivá je model, který může být v datech proměnlivý.  

## <a name="how-to-use-cross-validate-model"></a>Jak používat model vzájemného ověřování

Spuštění křížového ověřování může trvat dlouhou dobu, pokud je datová sada velká.  Proto můžete použít **model vzájemného ověřování** v počáteční fázi sestavování a testování modelu, pro vyhodnocení správnosti parametrů modelu (za předpokladu, že doba výpočtu je přípustná), a potom můžete vystavit a vyhodnotit svůj model pomocí navázaly se parametry pomocí [modelu vlaku](train-model.md) a [vyhodnotit moduly modelu](evaluate-model.md) .

V tomto scénáři provedete a otestujete model pomocí **modelu vzájemného ověřování**.

1. Přidejte do svého kanálu modul **model vzájemného ověřování** . Můžete ji najít v Návrháři Azure Machine Learning v kategorii **hodnocení modelu & vyhodnocení** . 

2. Připojte výstup jakéhokoli modelu **klasifikace** nebo **regrese** . 

    Pokud například používáte pro klasifikaci **Bayes počítač se dvěma třídami** , nakonfigurujte model pomocí parametrů, které chcete, a pak přetáhněte konektor z **nevýukového portu modelu** klasifikátoru na shodný port **křížového ověření. Model**. 

    > [!TIP] 
    > Model není nutné vyškolený, protože **model křížového ověřování** automaticky nakládá model jako součást vyhodnocení.  
3.  V portu **DataSet** **modelu křížového ověřování**připojte všechny datové sady s popiskem.  

4.  V podokně **vlastnosti** **modelu vzájemného ověřování**klikněte na možnost **Spustit selektor sloupců** a vyberte jeden sloupec, který obsahuje popisek třídy, nebo předvídatelné hodnoty. 

5. Nastavte hodnotu **náhodného parametru počátečního** navýšení, pokud chcete, aby se výsledky křížového ověřování opakovaly po sobě jdoucích spuštění na stejných datech.  

6.  Spuštění kanálu

7. Popis sestav najdete v části s [výsledky](#results) .

    Chcete-li získat kopii modelu pro pozdější použití, klikněte pravým tlačítkem myši na výstup modulu, který obsahuje algoritmus (například na **dva počítače Bayes bodu**), a klikněte na **Uložit jako trained model**.

## <a name="results"></a>Výsledky

Po dokončení všech iterací **model vzájemného ověřování** vytvoří skóre pro celou datovou sadu a také metriky výkonu, které můžete použít k vyhodnocení kvality modelu.

### <a name="scored-results"></a>Výsledky skóre

První výstup modulu poskytuje zdrojová data pro každý řádek spolu s některými předpovězenými hodnotami a souvisejícími pravděpodobnostmi. 

Chcete-li zobrazit tyto výsledky, klikněte pravým tlačítkem myši na modul **modelu křížové validace** , vyberte **skóre výsledků**a klikněte na **vizualizovat**.

| Nový název sloupce      | Popis                              |
| -------------------- | ---------------------------------------- |
| Popisky s skóre        | Tento sloupec se přidá na konec datové sady a obsahuje předpokládanou hodnotu pro každý řádek. |
| Pravděpodobnost skóre | Tento sloupec je přidán na konec datové sady a označuje odhadovanou pravděpodobnost hodnoty ve **vyhodnocených popiscích**. |
| Číslo skládání          | Určuje index přeložení na základě 0, ke kterému byl během křížového ověřování přiřazen každý řádek dat. |

 ### <a name="evaluation-results"></a>Výsledky vyhodnocení

Druhá sestava je seskupena podle skládání. Pamatujte, že při provádění **model vzájemného ověřování** náhodně rozdělí data školení na *n* skládání (ve výchozím nastavení 10). V každé iteraci přes datovou sadu používá **model křížového** ověřování jednu přeložení jako datovou sadu ověřování a používá zbývající *n-1* skládání pro výuku modelu. Každý z modelů *n* je testován proti datům ve všech ostatních skládáních.

V této sestavě jsou skládání uvedena podle hodnoty indexu ve vzestupném pořadí.  Chcete-li seřadit v jakémkoli jiném sloupci, můžete výsledky Uložit jako datovou sadu.

Chcete-li zobrazit tyto výsledky, klikněte pravým tlačítkem myši na modul **modelu křížové validace** , vyberte možnost **výsledky vyhodnocení podle skládání**a klikněte na **vizualizovat**.


|Název sloupce| Popis|
|----|----|
|Číslo skládání| Identifikátor pro každé skládání. Pokud jste vytvořili 5 přeložení, mělo by být 5 podmnožin dat, očíslované od 0 do 4.
|Počet příkladů v skládání|Počet řádků přiřazených každému skládání. Měly by být zhruba stejné. |


Kromě toho jsou pro každé skládání zahrnuty následující metriky v závislosti na typu modelu, který vyhodnocujete. 

+ **Modely klasifikace**: přesnost, odvolání, F-SKORE, AUC, přesnost  

+ **Regresní modely**: střední chyba, hlavní střední hodnota, chyba, relativní absolutní chyba, relativní natažená chyba a koeficient určení


## <a name="technical-notes"></a>Technické poznámky  

+ Je osvědčeným postupem normalizovat datové sady předtím, než je použijete pro křížové ověřování. 

+ Vzhledem **k** tomu, že model prochází a ověřuje model víckrát, je mnohem více výpočetně náročný a trvá déle, než kdybyste ověřili model pomocí náhodně rozdělené datové sady. 

+ Není nutné rozdělit datovou sadu do školicích a testovacích sad při použití vzájemného ověřování k měření přesnosti modelu. 


## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning služby. 

