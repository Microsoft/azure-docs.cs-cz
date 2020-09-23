---
title: Trénování modelu PyTorch
titleSuffix: Azure Machine Learning
description: Naučte se, jak proškolit model pytorch od začátku nebo ho finetune.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 0ba603dad7d48be725f308f3a3296676c5f4f108
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883241"
---
# <a name="train-pytorch-model"></a>Trénování modelu PyTorch

V tomto článku se dozvíte, jak pomocí modulu **Pytorch** designeru Azure Machine Learning v Návrháři přePytorch modely, jako je DenseNet. Školení probíhá po definování modelu a nastavení jeho parametrů a vyžaduje data s popisky. 

## <a name="how-to-use-train-pytorch-model"></a>Jak používat model Pytorch pro analýzu 

1. Přidejte do konceptu kanálu v Návrháři modul [DenseNet](densenet.md) nebo [ResNet](resnet.md) .

2. Přidejte do kanálu modul **Pytorch model výuky** . Tento modul můžete najít v kategorii **školení modelu** . Rozbalte položku **vlak**a přetáhněte do svého kanálu modul **Pytorch modelu pro analýzu** .

   > [!NOTE]
   > Modul **výukového modelu Pytorch** je pro velkou datovou sadu lepší běžet na výpočetním typu **GPU** , jinak se váš kanál nezdaří. Můžete vybrat COMPUTE pro určitý modul v pravém podokně modulu nastavením **použít jiný cíl služby COMPUTE**.

3.  Na levém vstupu připojte nevlakový model. Připojte datovou sadu a datovou sadu pro školení k prostřednímu a pravému vstupu **modelu Pytorch pro analýzu**.

    U nevýukového modelu musí to být pytorch model, jako je DenseNet; v opačném případě bude vyvolána výjimka InvalidModelDirectoryError.

    Pro datovou sadu musí být datová sada školení označená jako adresář imagí. Informace o tom, jak získat adresář obrázků s popiskem, najdete v tématu **převod na adresář imagí** . Pokud není označený, bude vyvolána výjimka NotLabeledDatasetError.

    Datová sada školení a datová sada ověření mají stejné kategorie označení, jinak bude vyvolána výjimka InvalidDatasetError.

4.  V případě **Epochs**určete, kolik Epochs se má naučit. Celá sada dat bude v každém epocha ve výchozím nastavení iterace 5.

5.  V případě **velikosti dávky**určete, kolik instancí se má v dávce vytvořit, ve výchozím nastavení se 16.

6.  Do pole **rychlost učení**zadejte hodnotu *studijní frekvence*. Hodnoty studijních kurzů řídí velikost kroku, který se používá v Optimalizátoru jako SGD pokaždé, když se model testuje a opraví.

    Tím, že se rychlost zmenší, otestujete model častěji s rizikem, že se můžete zablokovat v místních stabilní úrovně. Tím, že krok provedete větší, můžete provést sblížení rychleji a na riziko překročení skutečných minimálních minim. ve výchozím nastavení 0,001.

7.  V případě **náhodného osazení**volitelně zadejte celočíselnou hodnotu, která se má použít jako počáteční hodnota. Použití počáteční hodnoty se doporučuje, pokud chcete zajistit reprodukovatelnost experimentu v rámci spuštění.

8.  V případě **trpělivosti**určete, kolik epochs se má v předčasném zastavení školení zastavit, pokud se ztráta ověřování nesníží po sobě. ve výchozím nastavení 3.

9.  Odešlete kanál. Pokud má datová sada větší velikost, bude chvíli trvat.

## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete použít model pro bodování, připojit [model Pytorch](train-pytorch-model.md) pro [vyhodnocení modelu obrázku](score-image-model.md)a předpovídat hodnoty pro nové vstupní příklady.

## <a name="technical-notes"></a>Technické poznámky
###  <a name="expected-inputs"></a>Očekávané vstupy  

| Název               | Typ                    | Description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Nevlakový model    | UntrainedModelDirectory | Nevlakový model, vyžadovat pytorch         |
| Datová sada školení   | ImageDirectory          | Datová sada školení                         |
| Ověřovací datová sada | ImageDirectory          | Ověřovací datová sada pro vyhodnocení všech epocha |

###  <a name="module-parameters"></a>Parametry modulu  

| Name          | Rozsah            | Typ    | Výchozí | Description                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epochs        | > 0               | Celé číslo | 5       | Výběr sloupce obsahujícího sloupec popisek nebo výsledek |
| Velikost dávky    | > 0               | Celé číslo | 16      | Kolik instancí se má naučit v dávce   |
| Rychlost učení | >= Double. Kurzív | Float   | 0,001   | Počáteční rychlost učení pro Optimalizátor stochastického gradientu klesání. |
| Náhodné osazení   | Všechny              | Celé číslo | 1       | Počáteční hodnota pro generátor náhodných čísel používaný modelem |
| Trpělivost      | > 0               | Celé číslo | 3       | Kolik epochs k předčasnému zastavení školení   |

###  <a name="outputs"></a>Výstupy  

| Název          | Typ           | Description   |
| ------------- | -------------- | ------------- |
| Školený model | ModelDirectory | Školený model |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 



