---
title: 'Model skóre: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se používat modul určení skóre modelu v Azure Machine Learning k vygenerování předpovědi pomocí vyškoleného modelu klasifikace nebo regrese.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497649"
---
# <a name="score-model-module"></a>Modul Určení skóre modelu

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Pomocí tohoto modulu můžete vygenerovat předpovědi s využitím trained nebo regresního modelu.

## <a name="how-to-use"></a>Způsob použití

1. Přidejte modul určení **skóre modelu** do kanálu.

2. Připojte školený model a datovou sadu obsahující nová vstupní data. 

    Data by měla být ve formátu kompatibilním s typem vyškolený model, který používáte. Schéma vstupní datové sady by mělo také obecně odpovídat schématu dat použitých pro výuku modelu.

3. Spuštění kanálu

## <a name="results"></a>Výsledky

Po vygenerování sady skóre pomocí [modelu skóre](./score-model.md):

+ Pro vygenerování sady metrik používané pro vyhodnocení přesnosti modelu (výkon).  můžete propojit [vyhodnocenou](./evaluate-model.md)datovou sadu k vyhodnocení modelu, 
+ Klikněte pravým tlačítkem na modul a vyberte **vizualizovat** a zobrazí se ukázka výsledků.
+ Uloží výsledky do datové sady.

Skóre nebo předpokládaná hodnota může být v mnoha různých formátech v závislosti na modelu a vstupních datech:

- U modelů klasifikace [model skóre](./score-model.md) zapisuje předpokládanou hodnotu pro třídu a také pravděpodobnost předpovězené hodnoty.
- Pro regresní modely generuje [model skóre](./score-model.md) pouze předpovězenou číselnou hodnotu.
- V případě modelů klasifikace obrázků může být skóre třída objektu v obrázku nebo logická hodnota, která označuje, zda byla nalezena konkrétní funkce.

## <a name="publish-scores-as-a-web-service"></a>Publikování skóre jako webové služby

Běžné použití bodování je vrácení výstupu v rámci prediktivní webové služby. Další informace najdete v tomto kurzu, jak vytvořit webovou službu založenou na kanálu v Azure Machine Learning:

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 