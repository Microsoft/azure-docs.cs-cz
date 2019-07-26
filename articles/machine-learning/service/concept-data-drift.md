---
title: Monitorování posunu dat (Preview)
titleSuffix: Azure Machine Learning service
description: Naučte se, jak může služba Azure Machine Learning sledovat posun dat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371083"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Co je sledování posunu dat (Preview)?

Posun dat je změnou distribuce dat. V kontextu strojového učení můžou školicí modely strojového učení mít vliv na snížení výkonu předpovědi z důvodu snížení úrovně. Sledování posunu mezi školicími daty a daty používanými k vytváření předpovědi může přispět k detekci potíží s výkonem.

Modely strojového učení jsou stejně vhodné jako data používaná k jejich výuce. Nasazení modelů do produkčního prostředí bez monitorování jeho výkonu může vést k nezjištěným a nepříznivým dopadům. Díky monitorování posunu dat můžete detekovat a přizpůsobovat se ke posunu dat. 

## <a name="when-to-monitor-for-data-drift"></a>Kdy se má monitorovat posun dat?

Mezi metriky, které můžeme monitorovat, patří:

+ Velikost posunu (posunový koeficient)
+ Způsob posunu (posun podílu podle funkce)
+ Metriky ujeté vzdálenosti (Wasserstein, energie atd.)

V rámci tohoto monitorování je možné nastavit výstrahy nebo akce, když se zjistí posun, a odborník na data může prozkoumat původní příčinu problému. 

Pokud si myslíte, že se vstupní data pro nasazený model můžou změnit, měli byste zvážit použití detekce posunu dat.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak se monitoruje posun dat ve službě Azure Machine Learning

Pomocí **Azure Machine Learning služby**je mezi datovými sadami a nasazeními monitorovaná data. Chcete-li monitorovat pro posun dat, je určena datová sada, která je obvykle datovou sadou pro model. Druhá datová sada – obvykle modelují vstupní data shromážděná z nasazení – jsou testována proti základní datové sadě. Obě datové [sady jsou](how-to-explore-prepare-data.md#explore-with-summary-statistics) profilované a vstupní služba pro sledování datového posunu dat. Model strojového učení je vyškolen k detekci rozdílů mezi dvěma datovými sadami. Výkon modelu se převede na koeficient posunu, který měří velikost posunu mezi dvěma datovými sadami. Při použití [Interpretace modelu](machine-learning-interpretability-explainability.md) jsou vypočítány funkce, které přispívají k koeficientu posunu. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcích. 

## <a name="data-drift-metric-output"></a>Výstup metriky pro posun dat

Existují různé způsoby, jak zobrazit metriky pro posun:

* Použijte widget [Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* Použijte funkci na libovolný `datadriftRun` objekt. `get_metrics()`
* Zobrazení metrik v Azure Portal modelu

Následující metriky jsou uloženy v každé iteraci spuštění pro úlohu posunu dat:

|Metrika|Popis|
--|--|
wasserstein_distance|Statistická vzdálenost definovaná pro jednorozměrné číselné rozdělení.|
energy_distance|Statistická vzdálenost definovaná pro jednorozměrné číselné rozdělení.|
datadrift_coefficient|Formálně Matthews korelační koeficient, reálné číslo od-1 do 1. V případě posunu 0 neindikuje žádný posun a 1 označuje maximální posun.|
datadrift_contribution|Důležitost funkcí, které přispívají k posunu.|

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a Naučte se monitorovat posun dat:

+ [Přečtěte si, jak monitorovat posun dat na modelech nasazených prostřednictvím služby Azure Kubernetes Service (AKS).](how-to-monitor-data-drift.md)
+ Vyzkoušení [ukázek Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)