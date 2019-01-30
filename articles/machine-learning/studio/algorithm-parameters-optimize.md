---
title: Optimalizace algoritmů
titleSuffix: Azure Machine Learning Studio
description: Vysvětluje, jak vybrat optimální parametrem nastaveným pro algoritmus v Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0f324dec9349b44252a85b2f9334a1b6d847faae
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247132"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Vybrat parametry pro optimalizaci algoritmů ve službě Azure Machine Learning Studio

Toto téma popisuje, jak zvolit správný hyperparameter nastavení pro algoritmus ve službě Azure Machine Learning. Většina algoritmů strojového učení mít parametry se mají nastavit. Při tréninku modelu, budete muset zadat hodnoty pro tyto parametry. Efektivnost trénovaného modelu, závisí na parametry modelu, které zvolíte. Proces hledání optimální sadu parametrů se označuje jako *modelu výběru*.



Existují různé způsoby, jak modelu výběru. Ve službě machine learning, křížového ověření je jedním z nejpoužívanějších metody pro výběr modelu a je mechanismus výběru výchozí model v Azure Machine Learning. Protože Azure Machine Learning podporuje R a Python, můžete vždy implementovat vlastní mechanismus výběru modelu pomocí R nebo Python.

Probíhá hledání sady nejlepší čtyři kroky:

1. **Definování prostor parametr**: Pro algoritmus nejdřív se rozhodněte, přesné parametr hodnoty, které se mají zvážit.
2. **Definujte nastavení křížového ověření**: Rozhodněte, jak zvolit přeložení křížového ověření datové sady.
3. **Definování metriky**: Rozhodněte, jaké metriky pro určení nejlepší sadu parametrů, jako jsou správnost, střední kořenové spolehlivosti chyba, přesnosti, odvolání nebo f skóre.
4. **Školení, vyhodnocení a porovnat**: Pro každou jedinečnou kombinaci hodnot parametrů je křížového ověřování prováděné a na základě chybové metriky, které definujete. Po vyhodnocení a porovnání můžete zvolit ty modelu.

Ukazuje, jak jde tohoto dosáhnout v Azure Machine Learning je znázorněný na následujícím obrázku.

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definování prostor parametr
Můžete definovat parametrem nastaveným na model inicializačnímu kroku. Podokno parametr algoritmů strojového učení všechny má dva režimy trainer: *Jeden parametr* a *parametr rozsahu*. Zvolte režim oblasti parametrů. V režimu parametr rozsahu můžete zadat více hodnot pro každý parametr. V textovém poli můžete zadat hodnoty oddělené čárkami.

![Dvěma třídami posíleného rozhodovacího stromu, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální bodů mřížky a celkový počet bodů pomocí **Tvůrce rozsahu použití**. Hodnoty parametrů jsou ve výchozím nastavení generovány lineární stupnice. Avšak v tom případě **logaritmické měřítko** je zaškrtnuto, hodnoty jsou generovány v logaritmické měřítko (to znamená, poměr sousední body je konstantní místo jejich rozdíl). Pro celočíselné parametry můžete definovat rozsah použitím spojovníku. Například "1-10" znamená, že všechny celých čísel od 1 do 10 (obojí inkluzivní) tvoří sadu parametrů. Ve smíšeném režimu je také podporována. Například nastavte parametr "1-10, 20, 50" bude zahrnovat celá čísla 1 až 10, 20 a 50.

![Dvěma třídami posíleného rozhodovacího stromu, parametr rozsahu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování přeložení křížového ověřování
[Rozdělení a vzorky] [ partition-and-sample] modulu je možné náhodně přiřazení složení k datům. V následující ukázková konfigurace pro modul jsme definování pěti složení a náhodně přiřaďte číslo fold k instancím vzorku.

![Rozdělení a vzorky](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
[Tune Model Hyperparameters] [ tune-model-hyperparameters] modulu poskytuje podporu pro empirických výběr nejlepší sadu parametrů pro danou algoritmus a datové sady. Kromě dalších informací ohledně trénování modelu, **vlastnosti** podokně tento modul obsahuje metriky pro určení nejlepší sadu parametrů. Má dvě různé rozevírací seznamy klasifikačních a regresních algoritmů, v uvedeném pořadí. Pokud tento algoritmus uvažovanou je klasifikační algoritmus, je ignorován metrika regrese a naopak. V tomto konkrétním příkladu je metrika **přesnost**.   

![Uklízení parametrů](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Školení, vyhodnocení a porovnání
Stejné [Tune Model Hyperparameters] [ tune-model-hyperparameters] modulu železniční všechny modely, které odpovídají parametru nastavení, vyhodnotí jako různé metriky a pak vytvoří přizpůsobené trénovaného modelu na základě metriku můžete Zvolte. Tento modul má dvě povinné vstupy:

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
