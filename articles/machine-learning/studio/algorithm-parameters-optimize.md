---
title: Optimalizovat algoritmy
titleSuffix: ML Studio (classic) - Azure
description: Vysvětluje, jak zvolit optimální sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 04148b482cb07665f43df5bd86a77175cbbaf08b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218272"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Výběr parametrů pro optimalizaci algoritmů v Azure Machine Learning Studio (Classic)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Toto téma popisuje, jak zvolit správnou sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic). Většina algoritmů strojového učení mít parametry se mají nastavit. Při tréninku modelu, budete muset zadat hodnoty pro tyto parametry. Efektivnost trénovaného modelu, závisí na parametry modelu, které zvolíte. Proces nalezení optimální sady parametrů je známý jako *Výběr modelu*.



Existují různé způsoby, jak modelu výběru. V strojovém učení je křížové ověřování jednou z nejpoužívanějších metod výběru modelu a je výchozím mechanismem pro výběr modelu v Azure Machine Learning Studio (Classic). Vzhledem k tomu, že Azure Machine Learning Studio (Classic) podporuje R i Python, můžete vždy implementovat vlastní mechanismy výběru modelu pomocí jazyka R nebo Python.

Probíhá hledání sady nejlepší čtyři kroky:

1. **Definujte prostor parametru**: pro algoritmus nejdřív určete přesné hodnoty parametrů, které chcete zvážit.
2. **Definice nastavení křížového ověření**: Rozhodněte, jak zvolit skládání křížového ověřování pro datovou sadu.
3. **Definování metriky**: Rozhodněte, jaká metrika se má použít k určení nejlepší sady parametrů, jako je přesnost, hlavní střední hodnota chyby, přesnost, odvolání nebo f-skóre.
4. **Výukové, vyhodnocování a porovnávání**: pro každou jedinečnou kombinaci hodnot parametrů se křížové ověření provádí pomocí a na základě metriky chyby, kterou definujete. Po vyhodnocení a porovnání můžete zvolit ty modelu.

Následující obrázek ukazuje, jak to lze dosáhnout v Azure Machine Learning Studio (Classic).

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definování prostor parametr
Můžete definovat parametrem nastaveným na model inicializačnímu kroku. Podokno parametrů všech algoritmů strojového učení má dva režimy Trainer: *jeden parametr* a *Rozsah parametru*. Zvolte režim oblasti parametrů. V režimu parametr rozsahu můžete zadat více hodnot pro každý parametr. V textovém poli můžete zadat hodnoty oddělené čárkami.

![Dvěma třídami posíleného rozhodovacího stromu, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální body mřížky a celkový počet bodů, které mají být vygenerovány pomocí **Tvůrce rozsahu použití**. Hodnoty parametrů jsou ve výchozím nastavení generovány lineární stupnice. Pokud je ale zaškrtnuto **měřítko protokolu** , generují se hodnoty v logaritmickém měřítku (to znamená, že poměr sousedních bodů je konstantní místo jejich rozdílu). Pro celočíselné parametry můžete definovat rozsah použitím spojovníku. Například "1-10" znamená, že všechna celá čísla mezi 1 a 10 (včetně) tvoří sadu parametrů. Ve smíšeném režimu je také podporována. Například sada parametrů "1-10, 20, 50" by obsahovala celá čísla 1-10, 20 a 50.

![Dvěma třídami posíleného rozhodovacího stromu, parametr rozsahu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování přeložení křížového ověřování
[Oddíl a vzorový][partition-and-sample] modul lze použít k náhodnému přiřazování skládání k datům. V následující ukázková konfigurace pro modul jsme definování pěti složení a náhodně přiřaďte číslo fold k instancím vzorku.

![Rozdělení a vzorky](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
Modul [předparametrů modelu ladění][tune-model-hyperparameters] poskytuje podporu pro empirický výběr nejvhodnější sady parametrů pro daný algoritmus a datovou sadu. Kromě dalších informací týkajících se školení modelu obsahuje podokno **vlastností** tohoto modulu metriku pro určení nejlepší sady parametrů. Má dvě různé rozevírací seznamy klasifikačních a regresních algoritmů, v uvedeném pořadí. Pokud tento algoritmus uvažovanou je klasifikační algoritmus, je ignorován metrika regrese a naopak. V tomto konkrétním příkladu je metrika **přesnost**.   

![Uklízení parametrů](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Školení, vyhodnocení a porovnání
Stejný modul [parametrů][tune-model-hyperparameters] modulů pro ladění má za základ navlakování všech modelů odpovídajících sadě parametrů, vyhodnotit různé metriky a pak vytvoří nejlepší školený model na základě metriky, kterou zvolíte. Tento modul má dvě povinné vstupy:

* Nezkušený learner
* Datové sady

Modul obsahuje také volitelné datové sadě služby vstup. Připojte datové sady s informacemi o fold povinná datová sada vstupem. Pokud datovou sadu není přiřazené žádné informace o skládání, pak 10-fold křížové ověření automaticky spustí ve výchozím nastavení. Pokud podmínka není splněna fold přiřazení a ověření datové sady se poskytuje port volitelné datové sady, je zvolen režim trénování a testování a první datová sada použije k natrénování modelu pro každou kombinaci parametrů.

![Posílený rozhodovací strom třídění](./media/algorithm-parameters-optimize/fig6a.png)

Model se pak vyhodnocuje na datové sadě ověření. Na levý výstupní port modulu ukazuje různé požadované metriky, jako funkce hodnot parametrů. Pravý výstupní port poskytuje vyškolený model, který odpovídá nejlepšímu modelu podle zvolené metriky (**přesnost** v tomto případě).  

![Ověření datové sady](./media/algorithm-parameters-optimize/fig6b.png)

Zobrazí se přesný parametry zvolí vizualizace na pravé výstupní port. Tento model je možné v vyhodnocování sady testů nebo zprovozněné webové službě po uložení jako trénovaného modelu.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
