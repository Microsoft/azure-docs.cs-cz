---
title: Sestavení modelů ML pomocí návrháře
titleSuffix: Azure Machine Learning
description: Přečtěte si o pojmech, konceptech a pracovních postupech, které tvoří návrháře pro Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517862"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Co je Návrhář Azure Machine Learning (Preview)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Návrhář pro Azure Machine Learning umožňuje přípravu dat, výuku, testování, nasazení, správu a sledování modelů strojového učení bez psaní kódu.

Není nutné žádné programování, vizuálně propojit [datové sady](#datasets) a [moduly](#module) a vytvořit model.

Návrhář používá [pracovní prostor](concept-workspace.md) Azure Machine Learning k těmto akcím:

+ Vytvářejte, upravujte a spouštějte [kanály](#pipeline) v pracovním prostoru.
+ Přístup k [datovým sadám](#datasets).
+ Pomocí [výpočetních prostředků](#compute) v pracovním prostoru spusťte kanál. 
+ Registrovat [modely](concept-azure-machine-learning-architecture.md#models).
+ [Publikování](#publish) kanálů jako koncových bodů REST
+ [Nasazení](#deployment) modelů jako koncových bodů kanálu (pro odvození dávky) nebo koncových bodů v reálném čase u výpočetních prostředků v pracovním prostoru.

![Přehled návrháře](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Pracovní postupy

Návrhář poskytuje interaktivní a vizuální plátno pro rychlé sestavování, testování a iteraci modelu. 

+ [Datové sady](#datasets) a [moduly](#module) přetahujete na plátno.
+ Propojit moduly dohromady a vytvořit [kanál](#pipeline).
+ Spusťte kanál pomocí výpočetního prostředku pracovního prostoru služby Machine Learning.
+ V návrhu modelu Iterujte úpravou kanálu a znovu ho spusťte.
+ Až budete připraveni, převeďte svůj **školicí kanál** na **odvozený kanál**.
+ Pokud ho chcete znovu odeslat bez kódu Pythonu, [publikujte](#publish) svůj kanál jako koncový bod REST.
+ [Nasaďte](#deployment) kanál odvození jako koncový bod kanálu nebo koncový bod v reálném čase, aby k vašemu modelu měli přístup jiní uživatelé.

## <a name="pipeline"></a>Kanál

Vytvořte [kanál](concept-azure-machine-learning-architecture.md#ml-pipelines) ml od začátku nebo použijte existující vzorový kanál jako šablonu. Při každém spuštění kanálu se artefakty ukládají do svého pracovního prostoru. Spuštění kanálu jsou seskupena do [experimentů](concept-azure-machine-learning-architecture.md#experiments).

Kanál se skládá z datových sad a analytických modulů, které spojíte dohromady za účelem vytvoření modelu. Konkrétně platný kanál má tyto vlastnosti:

* Datové sady mohou být připojeny pouze k modulům.
* Moduly se můžou připojit k datovým sadám nebo jiným modulům.
* Všechny vstupní porty pro moduly musí mít připojení k toku dat.
* Musí být nastavené všechny požadované parametry pro každý modul.


Informace o tom, jak začít s návrhářem, najdete v tématu [kurz: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Datové sady

Datová sada Machine Learning usnadňuje přístup k datům a práci s nimi. V návrháři je zahrnutý několik ukázkových datových sad, které můžete experimentovat s nástrojem. Můžete [zaregistrovat](./how-to-create-register-datasets.md) více datových sad, jak je potřebujete.

## <a name="module"></a>Modul

Modul je algoritmus, který je možné provést na datech. Návrhář má několik modulů od vstupních funkcí dat až po procesy školení, bodování a ověřování.

Modul může obsahovat sadu parametrů, pomocí kterých je možné konfigurovat jeho vnitřní algoritmy. Když vyberete modul na plátně, parametry modulu se zobrazí v podokně vlastnosti napravo od plátna. Úpravou parametrů v tomto podokně můžete model optimalizovat.

![Vlastnosti modulu](media/ui-concept-visual-interface/properties.png)

Další nápovědu k dispozici v knihovně strojového učení, najdete v tématu [Přehled modulu & algoritmu](../algorithm-module-reference/module-reference.md) .

## <a name="compute"></a>Výpočetní prostředky

Pomocí výpočetních prostředků z pracovního prostoru můžete spustit kanál a hostovat nasazené modely jako koncové body v reálném čase nebo koncové body kanálu (pro odvození dávky). Podporované cíle výpočtů:

| Cílový výpočetní objekt | Školení | Nasazení |
| ---- |:----:|:----:|
| Azure Machine Learning COMPUTE | ✓ | |
| Azure Kubernetes Service | | ✓ |

Cíle výpočetní služby jsou připojeny k vašemu [pracovnímu prostoru](concept-workspace.md)Machine Learning. Výpočetní cíle můžete spravovat ve vašem pracovním prostoru v [Azure Machine Learning Studiu](https://ml.azure.com).

## <a name="publish"></a>Publikování

Jakmile budete mít kanál připravený, můžete ho publikovat jako koncový bod REST. [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) se dá odeslat bez kódu Pythonu, který ho vytvořil.

Kromě toho se dá PublishedPipeline použít k opětovnému odeslání kanálu s různými hodnotami PipelineParameter a vstupy.

## <a name="deployment"></a>Nasazení

Jakmile je prediktivní model připravený, nasaďte ho jako koncový bod kanálu nebo koncový bod v reálném čase přímo z návrháře.

Koncový bod kanálu je PublishedPipeline, ve kterém můžete odeslat běh kanálu s různými hodnotami PipelineParameter a vstupy pro odvození dávky.

Koncový bod v reálném čase poskytuje rozhraní mezi aplikací a modelem bodování. Externí aplikace může komunikovat s modelem bodování v reálném čase. Volání koncového bodu v reálném čase vrátí výsledky předpovědi do externí aplikace. Pro volání koncového bodu v reálném čase předáte klíč rozhraní API, který byl vytvořen při nasazení koncového bodu. Koncový bod je založený na REST, oblíbené architektuře, která je vhodná pro projekty webového programování.

Informace o tom, jak model nasadit, najdete v tématu [kurz: nasazení modelu strojového učení pomocí návrháře](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Přesun z vizuálního rozhraní do návrháře

Rozhraní jazyka Visual (Preview) bylo aktualizováno a nyní je Azure Machine Learning Designer (Preview). Návrhář byl znovu navržen tak, aby používal back-end založený na kanálu, který je plně integrován s ostatními funkcemi Azure Machine Learning. 

V důsledku těchto aktualizací se některé koncepty a pojmy pro vizuální rozhraní změnily nebo přejmenovaly. Nejdůležitější koncepční změny najdete v následující tabulce. 

| Koncept v Návrháři | Dříve v vizuálním rozhraní |
| ---- |:----:|
| Koncept kanálu | Začátku |
| Koncový bod v reálném čase | Webová služba |

### <a name="migrating-to-the-designer"></a>Migrace do návrháře

Existující experimenty vizuálního rozhraní a webové služby můžete převést na kanály a koncové body v návrháři v reálném čase. K migraci prostředků vizuálního rozhraní použijte následující postup:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Další kroky

* Seznamte se se základy prediktivní analýzy a strojového učení s využitím [kurzu: předpověď ceny automobilu pomocí návrháře](tutorial-designer-automobile-price-train-score.md)
* Použijte jednu z ukázek a upravte ji tak, aby vyhovovala vašim potřebám:

- [Ukázka 1 – regrese: předpověď ceny automobilu](how-to-designer-sample-regression-automobile-price-basic.md)
- [Ukázka 2 – regrese: porovnání algoritmů pro předpověď cen automobilu](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Ukázka 3 – klasifikace s výběrem funkcí: předpověď příjmů](how-to-designer-sample-classification-predict-income.md)
- [Ukázka 4 – klasifikace: předpověď úvěrového rizika (citlivé na náklady)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Ukázka 5 – klasifikace: předpověď změn](how-to-designer-sample-classification-churn.md)
- [Ukázka 6 – klasifikace: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
- [Ukázka 7 – klasifikace textu: Wikipedii SP 500 DataSet](how-to-designer-sample-text-classification.md)

