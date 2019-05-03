---
title: 'Určení skóre modelu: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak používat modul určení skóre modelu ve službě Azure Machine Learning k vygenerování predikce na základě natrénovaného klasifikačního nebo regresního modelu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029262"
---
# <a name="score-model-module"></a>Modul Určení skóre modelu

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží ke generování predikce na základě trénovaný klasifikační nebo regresní model.

## <a name="how-to-use"></a>Způsob použití

1. Přidat **Score Model** modulu do experimentu.

2. Připojte trénovaného modelu a datovou sadu obsahující nové vstupní data. 

    Data by měla být ve formátu, který je kompatibilní s typem trénovaného modelu, který používáte. Schéma vstupní datová sada by měla odpovídat také obecně schéma dat využívají k tréninku modelu.

3. Spusťte experiment.

## <a name="results"></a>Výsledky

Po vygenerování skóre, které se pomocí sady [Score Model](./score-model.md):

+ Generuje sadu metriky použít za vaše rozhodnutí vyzkoušet jeho přesnost (výkon).  scored datovou sadu, která se můžete připojit [Evaluate Model](./evaluate-model.md), 
+ Klikněte pravým tlačítkem na modul a vyberte **vizualizovat** zobrazíte ukázku výsledků.
+ Uložte výsledky do datové sady.

Skóre nebo předpovězené hodnoty, může být v různých formátech, v závislosti na modelu a vstupní data:

- Pro modelů klasifikace [Score Model](./score-model.md) výstupy předpovězené hodnoty pro třídy, jakož i pravděpodobnost předpovězené hodnoty.
- Pro regresní modely [Score Model](./score-model.md) generuje pouze předpokládaná číselná hodnota.
- U modelů klasifikace imagí může být skóre třídu objektu v obrázku nebo logická hodnota označující, zda byl nalezen konkrétní funkce.

## <a name="publish-scores-as-a-web-service"></a>Publikovat jako webovou službu skóre

Běžně se používají vyhodnocení je výstup jako součást prediktivní webové služby. Další informace najdete v tématu v tomto kurzu o tom, jak vytvořit webové služby založené na experiment v Azure Machine Learning:


## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 