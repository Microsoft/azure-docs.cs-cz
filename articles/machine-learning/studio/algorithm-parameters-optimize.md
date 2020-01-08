---
title: Optimalizovat algoritmy
titleSuffix: ML Studio (classic) - Azure
description: Vysvětluje, jak zvolit optimální sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: ac3aa0dc619ec05dcd79a4f8740026b1eabc19aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427700"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Výběr parametrů pro optimalizaci algoritmů v Azure Machine Learning Studio (Classic)

Toto téma popisuje, jak zvolit správnou sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic). Většina algoritmů strojového učení mít parametry se mají nastavit. Při tréninku modelu, budete muset zadat hodnoty pro tyto parametry. Efektivnost trénovaného modelu, závisí na parametry modelu, které zvolíte. Proces hledání optimální sadu parametrů se označuje jako *modelu výběru*.



Existují různé způsoby, jak modelu výběru. V strojovém učení je křížové ověřování jednou z nejpoužívanějších metod výběru modelu a je výchozím mechanismem pro výběr modelu v Azure Machine Learning Studio (Classic). Vzhledem k tomu, že Azure Machine Learning Studio (Classic) podporuje R i Python, můžete vždy implementovat vlastní mechanismy výběru modelu pomocí jazyka R nebo Python.

Probíhá hledání sady nejlepší čtyři kroky:

1. **Definování prostor parametr**: pro algoritmus, nejdřív se rozhodněte, parametr přesné hodnoty, které chcete vezměte v úvahu.
2. **Definujte nastavení křížového ověření**: Rozhodněte, jak zvolit přeložení křížového ověření datové sady.
3. **Definování metriky**: Rozhodněte, jaké metriky pro určení nejlepší sadu parametrů, jako jsou správnost, střední kořenové spolehlivosti chyba, přesnosti, odvolání nebo f skóre.
4. **Školení, vyhodnocení a porovnat**: pro každou jedinečnou kombinaci hodnot parametrů křížového ověřování prováděné a na základě metriky chyba definujete. Po vyhodnocení a porovnání můžete zvolit ty modelu.

Následující obrázek ukazuje, jak to lze dosáhnout v Azure Machine Learning Studio (Classic).

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definování prostor parametr
Můžete definovat parametrem nastaveným na model inicializačnímu kroku. Podokno parametr všechny algoritmy strojového učení má dva režimy trainer: *jediný parametr* a *parametr rozsahu*. Zvolte režim oblasti parametrů. V režimu parametr rozsahu můžete zadat více hodnot pro každý parametr. V textovém poli můžete zadat hodnoty oddělené čárkami.

![Dvěma třídami posíleného rozhodovacího stromu, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální bodů mřížky a celkový počet bodů pomocí **Tvůrce rozsahu použití**. Hodnoty parametrů jsou ve výchozím nastavení generovány lineární stupnice. Avšak v tom případě **logaritmické měřítko** je zaškrtnuto, hodnoty jsou generovány v logaritmické měřítko (to znamená, poměr sousední body je konstantní místo jejich rozdíl). Pro celočíselné parametry můžete definovat rozsah použitím spojovníku. Například "1-10" znamená, že všechny celých čísel od 1 do 10 (obojí inkluzivní) tvoří sadu parametrů. Ve smíšeném režimu je také podporována. Například nastavte parametr "1-10, 20, 50" bude zahrnovat celá čísla 1 až 10, 20 a 50.

![Dvěma třídami posíleného rozhodovacího stromu, parametr rozsahu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování přeložení křížového ověřování
[Oddíl a vzorový][partition-and-sample] modul lze použít k náhodnému přiřazování skládání k datům. V následující ukázková konfigurace pro modul jsme definování pěti složení a náhodně přiřaďte číslo fold k instancím vzorku.

![Rozdělení a vzorky](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
Modul [předparametrů modelu ladění][tune-model-hyperparameters] poskytuje podporu pro empirický výběr nejvhodnější sady parametrů pro daný algoritmus a datovou sadu. Kromě dalších informací ohledně trénování modelu, **vlastnosti** podokně tento modul obsahuje metriky pro určení nejlepší sadu parametrů. Má dvě různé rozevírací seznamy klasifikačních a regresních algoritmů, v uvedeném pořadí. Pokud tento algoritmus uvažovanou je klasifikační algoritmus, je ignorován metrika regrese a naopak. V tomto konkrétním příkladu je metrika **přesnost**.   

![Uklízení parametrů](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Školení, vyhodnocení a porovnání
Stejný modul [parametrů][tune-model-hyperparameters] modulů pro ladění má za základ navlakování všech modelů odpovídajících sadě parametrů, vyhodnotit různé metriky a pak vytvoří nejlepší školený model na základě metriky, kterou zvolíte. Tento modul má dvě povinné vstupy:

* Nezkušený learner
* Datové sady

Modul obsahuje také volitelné datové sadě služby vstup. Připojte datové sady s informacemi o fold povinná datová sada vstupem. Pokud datovou sadu není přiřazené žádné informace o skládání, pak 10-fold křížové ověření automaticky spustí ve výchozím nastavení. Pokud podmínka není splněna fold přiřazení a ověření datové sady se poskytuje port volitelné datové sady, je zvolen režim trénování a testování a první datová sada použije k natrénování modelu pro každou kombinaci parametrů.

![Posílený rozhodovací strom třídění](./media/algorithm-parameters-optimize/fig6a.png)

Model se pak vyhodnocuje na datové sadě ověření. Na levý výstupní port modulu ukazuje různé požadované metriky, jako funkce hodnot parametrů. Na pravém výstupní port poskytuje trénovaného modelu, který odpovídá na ty model podle vybrané metriky (**přesnost** v tomto případě).  

![Ověření datové sady](./media/algorithm-parameters-optimize/fig6b.png)

Zobrazí se přesný parametry zvolí vizualizace na pravé výstupní port. Tento model je možné v vyhodnocování sady testů nebo zprovozněné webové službě po uložení jako trénovaného modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
