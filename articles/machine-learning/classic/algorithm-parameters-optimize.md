---
title: 'ML Studio (Classic): optimalizace algoritmů – Azure'
description: Vysvětluje, jak zvolit optimální sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 5daa15dd01be3726ea1ff8e093d1fe94d1755122
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342219"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-machine-learning-studio-classic"></a>Výběr parametrů pro optimalizaci algoritmů v Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

Toto téma popisuje, jak zvolit správnou sadu parametrů pro algoritmus v Azure Machine Learning Studio (Classic). Většina algoritmů strojového učení má nastavené parametry. Při výukovém modelu je nutné zadat hodnoty pro tyto parametry. Účinnost proučeného modelu závisí na zvolených parametrech modelu. Proces nalezení optimální sady parametrů je známý jako *Výběr modelu*.



Existují různé způsoby, jak vybrat model. V strojovém učení je křížové ověřování jednou z nejpoužívanějších metod výběru modelu a je výchozím mechanismem pro výběr modelu v Azure Machine Learning Studio (Classic). Vzhledem k tomu, že Azure Machine Learning Studio (Classic) podporuje R i Python, můžete vždy implementovat vlastní mechanismy výběru modelu pomocí jazyka R nebo Python.

Při hledání nejlepší sady parametrů je potřeba postupovat podle čtyř kroků:

1. **Definujte prostor parametru**: pro algoritmus nejdřív určete přesné hodnoty parametrů, které chcete zvážit.
2. **Definice nastavení křížového ověření**: Rozhodněte, jak zvolit skládání křížového ověřování pro datovou sadu.
3. **Definování metriky**: Rozhodněte, jaká metrika se má použít k určení nejlepší sady parametrů, jako je přesnost, hlavní střední hodnota chyby, přesnost, odvolání nebo f-skóre.
4. **Výukové, vyhodnocování a porovnávání**: pro každou jedinečnou kombinaci hodnot parametrů se křížové ověření provádí pomocí a na základě metriky chyby, kterou definujete. Po vyhodnocení a porovnání můžete vybrat nejlepší model.

Následující obrázek ukazuje, jak to lze dosáhnout v Azure Machine Learning Studio (Classic).

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definovat prostor parametru
Můžete definovat parametr nastavený v kroku inicializace modelu. Podokno parametrů všech algoritmů strojového učení má dva režimy Trainer: *jeden parametr* a *Rozsah parametru*. Vyberte režim rozsahu parametrů. V režimu rozsahu parametrů můžete pro každý parametr zadat více hodnot. Do textového pole můžete zadat hodnoty oddělené čárkami.

![Zvýšení rozhodovacího stromu se dvěma třídami, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální body mřížky a celkový počet bodů, které mají být vygenerovány pomocí **Tvůrce rozsahu použití**. Ve výchozím nastavení se hodnoty parametrů generují lineární stupnice. Pokud je ale zaškrtnuto **měřítko protokolu** , generují se hodnoty v logaritmickém měřítku (to znamená, že poměr sousedních bodů je konstantní místo jejich rozdílu). Pro celočíselné parametry můžete definovat rozsah pomocí pomlčky. Například "1-10" znamená, že všechna celá čísla mezi 1 a 10 (včetně) tvoří sadu parametrů. Podporuje se i smíšený režim. Například sada parametrů "1-10, 20, 50" by obsahovala celá čísla 1-10, 20 a 50.

![Zvýšení rozhodovacího stromu se dvěma třídami, rozsah parametrů](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování skládání křížového ověřování
[Oddíl a vzorový][partition-and-sample] modul lze použít k náhodnému přiřazování skládání k datům. V následující ukázkové konfiguraci pro modul definujeme pět skládání a náhodně přiřadíte k ukázkovým instancím číslo přeložení.

![Oddíl a ukázka](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
Modul [předparametrů modelu ladění][tune-model-hyperparameters] poskytuje podporu pro empirický výběr nejvhodnější sady parametrů pro daný algoritmus a datovou sadu. Kromě dalších informací týkajících se školení modelu obsahuje podokno **vlastností** tohoto modulu metriku pro určení nejlepší sady parametrů. Má dvě různá rozevírací seznamy pro klasifikaci a regresní algoritmy. Pokud je dotyčný algoritmus klasifikační algoritmus, je metrika regrese ignorována a naopak. V tomto konkrétním příkladu je metrika **přesnost**.   

![Parametry Sweep](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Výuka, vyhodnocení a porovnání
Stejný modul [parametrů][tune-model-hyperparameters] modulů pro ladění má za základ navlakování všech modelů odpovídajících sadě parametrů, vyhodnotit různé metriky a pak vytvoří nejlepší školený model na základě metriky, kterou zvolíte. Tento modul má dva povinné vstupy:

* Nevlakované učení
* Datová sada

Modul má také volitelnou vstupní datovou sadu. Připojte datovou sadu s informacemi přeloženými do povinného vstupu datové sady. Pokud datová sada nemá přiřazeny žádné informace o přeložení, bude ve výchozím nastavení automaticky spuštěn křížové ověřování. Pokud přiřazení skládání není provedeno a datová sada ověření je k dispozici na volitelném portu DataSet, je zvolen režim vlaku-test a první datová sada je použita ke výukě modelu pro každou kombinaci parametrů.

![Rozdaný klasifikátor rozhodovacího stromu](./media/algorithm-parameters-optimize/fig6a.png)

Model se pak vyhodnotí na datové sadě ověřování. Levý výstupní port modulu zobrazuje různé metriky jako funkce hodnot parametrů. Pravý výstupní port poskytuje vyškolený model, který odpovídá nejlepšímu modelu podle zvolené metriky (**přesnost** v tomto případě).  

![Ověřovací datová sada](./media/algorithm-parameters-optimize/fig6b.png)

Můžete zobrazit přesné parametry zvolené k vizualizaci správného výstupního portu. Tento model lze použít při vyhodnocování testovací sady nebo v provozní webové službě po uložení jako trained model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
