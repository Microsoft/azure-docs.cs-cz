---
title: Posun dat monitorování (Preview)
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak monitorovat službu Azure Machine Learning pro data odchylek.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 56761c32484d4f5b27800e56143c62d3731da852
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332933"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Co je data odchylek monitorování (Preview)?

Data jsou změny v distribuci dat. V rámci služby machine learning natrénované modely strojového učení zaznamenat snížení předpověď výkonu z důvodu odchylek. Monitorování odchylek mezi trénovací data a data ukládaná za účelem provádění předpovědí může pomoct detekovat problémy s výkonem.

Modely strojového učení jsou jenom tak dobré jako data využívají k tréninku je. Nasazení modelů do produkčního prostředí bez sledování jeho výkon a může vést k nezjištěné a škodlivé následky. Pomocí dat odchylek monitorování, můžete zjišťovat a přizpůsobit odchylek data. 

## <a name="when-to-monitor-for-data-drift"></a>Kdy se má monitorovat posun data?

Metriky, které nám můžete monitorovat patří:

+ Velikost odchylek odchylek koeficient)
+ Příčinu odchylek (odchylek příspěvek funkcí)
+ Vzdálenost metriky (Wasserstein, energie, atd.)

Pomocí tohoto monitorování na místě, upozornění nebo které lze upravit při zjištění odchylek a mezi odborníky přes data, můžete zjistit příčinu problému. 

Pokud si myslíte, že vstupní data pro vaše nasazené modelu může změnit, měli byste zvážit, pomocí zjišťování odchylek data.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak se monitoruje odchylek data ve službě Azure Machine Learning

Pomocí **služby Azure Machine Learning**, odchylek dat je monitorovat prostřednictvím datové sady nebo nasazení. Ke sledování dat odchylek, datovou sadu standardních hodnot – obvykle trénovací datové sady pro model - určena. Druhý datová sada – obvykle modelu vstupních dat shromážděných z nasazení – je testován vůči datovou sadu standardních hodnot. Oba datové sady jsou [profilované](how-to-create-dataset-snapshots.md) a zadání dat odchylek monitorování služby. Ke zjišťování rozdílů mezi dvěma datovými sadami se trénuje model strojového učení. Výkon modelu je převedena na odchylek koeficient, která měří velikost odchylek mezi dvěma datovými sadami. Pomocí [model interpretability](machine-learning-interpretability-explainability.md) se zpracovávají funkce, které přispívají k koeficient odchylek. Z profilu datové sady jsou sledovány statistické informace o jednotlivých funkcí. 

## <a name="data-drift-metric-output"></a>Metriky výstup dat odchylek

Pokud chcete zobrazit metriky odchylek několika způsoby:

* Pomocí widgetu Jupyter.
* Použití `get_metrics()` funkce na všech `datadriftRun` objektu.
* Zobrazení metrik na webu Azure Portal na modelu

V každé iteraci spuštění úlohy odchylek data se uloží následující metriky:

|Metrika|Popis|
--|--|
wasserstein_distance|Statistické vzdálenost definované pro jednorozměrné číselné distribuci.|
energy_distance|Statistické vzdálenost definované pro jednorozměrné číselné distribuci.|
datadrift_coefficient|Formálně Matthews korelačního koeficientu, reálné číslo od -1 do 1. V rámci odchylek 0 znamená bez odchylek a 1 označuje maximální odchylek.|
datadrift_contribution|Funkce důležitost funkce, které přispívají k odchylek.|

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o monitorování pro data odchylek:

+ [Další informace o monitorování dat odchylek na modely nasazené prostřednictvím Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Vyzkoušejte si [ukázky Poznámkový blok Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)