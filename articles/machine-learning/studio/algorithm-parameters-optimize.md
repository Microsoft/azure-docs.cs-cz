---
title: Optimalizujte algoritmy v Azure Machine Learning | Microsoft Docs
description: Vysvětluje, jak zvolit parametr optimální nastavení algoritmu v Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 64d725712dc8cf87deb150944a390c9cef87b56f
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833892"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Vyberte parametry pro Optimalizujte algoritmy v Azure Machine Learning
Toto téma popisuje, jak vybrat správné hyperparameter nastavit algoritmu v Azure Machine Learning. Většina algoritmy strojového učení mít parametry nastavit. Pokud jste trénování modelu, budete muset zadat hodnoty pro tyto parametry. Účinnost trénovaného modelu, závisí na modelu parametry, které zvolíte. Hledání optimální sadu parametrů, proces se označuje jako *modelu výběr*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Existují různé způsoby pro modelování výběr. Ve strojovém učení se křížového ověření je jednou z nejčastěji používané metody pro výběr modelu a je výchozí mechanismus výběru modelu v Azure Machine Learning. Protože Azure Machine Learning podporuje R a Python, můžete implementovat vlastní mechanismy Výběr modelu vždy pomocí R nebo Python.

Existují čtyři kroky procesu hledání nejlepší sadu parametrů:

1. **Zadejte parametr místo**: pro algoritmus, nejprve rozhodněte, přesný parametr hodnoty, které chcete vezměte v úvahu.
2. **Definujte nastavení, křížové ověření**: Rozhodněte, jak zvolit složení křížové ověření pro datovou sadu.
3. **Zadejte metriku**: Rozhodněte, jaké Metrika pro určení nejlepší sadu parametrů, třeba přesnost, střední kořenové spolehlivosti chybu, přesnost, odvolání nebo f – score.
4. **Cvičení, hodnocení a porovnání**: pro každou jedinečnou kombinaci hodnot parametrů křížového ověřování prováděné a podle metrika chyba definujete. Po vyhodnocení a porovnání můžete vybrat nejlepší provádění modelu.

Následující obrázek ukazuje ukazuje, jak toho lze dosáhnout v Azure Machine Learning.

![Najít nejlepší sadu parametrů](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Zadejte parametr místa
Můžete definovat parametr nastavit na krok inicializace modelu. Parametr podokně všechny algoritmy strojového učení obsahuje dva režimy trainer: *jeden parametr* a *parametr rozsahu*. Zvolte režim parametr rozsahu. V režimu parametr rozsahu můžete zadat více hodnot pro jednotlivé parametry. Do textového pole můžete zadat hodnot oddělených čárkami.

![Two-class boosted rozhodovací strom, jeden parametr](./media/algorithm-parameters-optimize/fig2.png)

 Alternativně můžete definovat maximální a minimální body mřížky a celkový počet bodů pomocí **Tvůrce rozsahu použití**. Ve výchozím nastavení se hodnoty parametru generují u lineární stupnice. Avšak v tom případě **škálování protokolu** je zaškrtnuto, hodnoty jsou generovány v protokolu škálování (to znamená, poměr sousedících bodů je konstantní místo jejich rozdíl). Pro parametry celé číslo můžete definovat rozsah použitím spojovníku. Například "1-10" znamená, že všechny celá čísla od 1 do 10 (obě včetně) tvoří sada parametrů. Ve smíšeném režimu je také podporována. Například, nastavte parametr "1 – 10, 20, 50" by mělo zahrnovat celá čísla 1 až 10, 20 až 50.

![Two-class boosted rozhodovací strom, parametr rozsahu](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definování křížové ověření složení
[Rozdělení a vzorky] [ partition-and-sample] modul lze použít k náhodně přiřazení složení k datům. V následující ukázka konfigurace modulu jsme definujte pět složení a náhodně přiřadit číslo násobek ukázka instancí.

![Rozdělení a vzorky](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definování metriky
[Tune Model Hyperparameters] [ tune-model-hyperparameters] modulu poskytuje podporu pro empirically výběr nejlepší sadu parametrů pro danou algoritmus a datové sady. Kromě dalších informací ohledně cvičení modelu, **vlastnosti** podokně tohoto modulu zahrnuje Metrika pro určení nejlepší sadu parametrů. Má dva různé rozevírací seznamy pro klasifikaci a regrese algoritmy, v uvedeném pořadí. Pokud algoritmus v úvahu je klasifikační algoritmus, regrese metrika je ignorován a naopak. V tomto konkrétním příkladu je metrika **přesnost**.   

![Parametry oblouku](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Cvičení, hodnocení a porovnání
Stejné [Tune Model Hyperparameters] [ tune-model-hyperparameters] modulu vlaky všechny modely, které odpovídají parametr nastavení, vyhodnotí různé metriky a potom vytvoří přizpůsobené trénovaného modelu na základě metriky můžete Vyberte. Tento modul má dvě povinné zadání:

* Nezkušený student
* Datové sady

Modul má také volitelné datové sadě služby vstup. Připojte datovou sadu s informacemi o násobek povinná datová sada vstupem. Je-li datovou sadu nemá přiřazeny žádné informace o násobek, 10-fold křížové ověření je provedeno automaticky ve ve výchozím nastavení. Pokud není provést přiřazení složení a ověření datové sady je k dispozici na portu volitelné datovou sadu, je zvolen režim train-test a první datovou sadu se používá pro trénování modelu pro každou kombinaci parametrů.

![Vylepšené rozhodovací strom třídění](./media/algorithm-parameters-optimize/fig6a.png)

Model se pak vyhodnotí na datovou sadu ověření. Levý výstupní port modulu ukazuje různé metriky jako funkce hodnot parametrů. Na pravém výstupní port dává trained model, který odpovídá přizpůsobené provádění modelu podle zvolené metrika (**přesnost** v tomto případě).  

![Ověření datové sady](./media/algorithm-parameters-optimize/fig6b.png)

Můžete zobrazit přesný parametry vybrali vizualizací správné výstupní port. Tento model lze použít v vyhodnocování testovací sada nebo v operationalized webové služby po uložení jako modulu trained model.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
