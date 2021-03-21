---
title: 'Model skóre: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul určení skóre modelu v Azure Machine Learning k vygenerování předpovědi pomocí vyškoleného modelu klasifikace nebo regrese.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 67d17af4f615907ca50b27ce8fa26973e5869608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314254"
---
# <a name="score-model"></a>Určení skóre modelu

Tento článek popisuje modul v Návrháři Azure Machine Learning.

Pomocí tohoto modulu můžete vygenerovat předpovědi s využitím trained nebo regresního modelu.

## <a name="how-to-use"></a>Způsob použití

1. Přidejte modul určení **skóre modelu** do kanálu.

2. Připojte školený model a datovou sadu obsahující nová vstupní data. 

    Data by měla být ve formátu kompatibilním s typem vyškolený model, který používáte. Schéma vstupní datové sady by mělo také obecně odpovídat schématu dat použitých pro výuku modelu.

3. Odešlete kanál.

## <a name="results"></a>Výsledky

Po vygenerování sady skóre pomocí [modelu skóre](./score-model.md):

+ Chcete-li vygenerovat sadu metrik používaných pro vyhodnocení přesnosti modelu (výkon), můžete k [vyhodnocení modelu](./evaluate-model.md)použít datovou sadu, 
+ Klikněte pravým tlačítkem na modul a vyberte **vizualizovat** a zobrazí se ukázka výsledků.
<!-- + To Save the results to a dataset. -->

Skóre nebo předpokládaná hodnota může být v mnoha různých formátech v závislosti na modelu a vstupních datech:

- U modelů klasifikace [model skóre](./score-model.md) zapisuje předpokládanou hodnotu pro třídu a také pravděpodobnost předpovězené hodnoty.
- Pro regresní modely generuje [model skóre](./score-model.md) pouze předpovězenou číselnou hodnotu.


## <a name="publish-scores-as-a-web-service"></a>Publikování skóre jako webové služby

Běžné použití bodování je vrácení výstupu v rámci prediktivní webové služby. Další informace najdete v [tomto kurzu](../tutorial-designer-automobile-price-deploy.md) o nasazení koncového bodu v reálném čase na základě kanálu v Návrháři Azure Machine Learning.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning.