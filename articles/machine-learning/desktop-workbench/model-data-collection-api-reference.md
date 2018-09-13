---
title: Referenční informace k Azure Machine Learning Model dat kolekce API | Dokumentace Microsoftu
description: Rozhraní API pro Machine Learning Model dat kolekce referenční informace k Azure.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: d9fee56d7748cdfd34f982fe79467f7d61c54926
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642645"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Referenční informace k Azure Machine Learning Model dat kolekce API

Shromažďování dat modelu můžete archivovat vstupy modelu a předpovědi z webovou službu machine learning. Zobrazit [příručce s postupy shromažďování dat modelu](how-to-use-model-data-collection.md) pochopit, jak nainstalovat `azureml.datacollector` na svém počítači Windows a Linux.

V této referenční příručce rozhraní API používáme postupného o tom, jak shromažďovat vstupy modelu a předpovědi z webovou službu machine learning.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Povolit shromažďování dat modelu v prostředí Azure ML Workbench

 Hledat conda\_dependencies.yml souborů ve vašem projektu ve složce aml_config a mít vaše systému conda\_dependencies.yml soubor zahrnutí modulu azureml.datacollector části pip, jak je znázorněno níže. Všimněte si, že se jedná pouze dílčí část objektu úplné conda\_dependencies.yml souboru:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>V současné době můžete použít modul kolekcí dat v aplikaci Azure ML Workbench běží v režimu docker. Místní režim nemusí fungovat pro všechna prostředí.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Povolit shromažďování dat modelu v soubor vyhodnocení

V souboru bodování, která se používá při operacionalizaci importujte modul kolekcí dat a třída ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Vytvoření instance kolektoru dat modelu
Vytvoření instance novou instanci třídy ModelDataCollector:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Zobrazit podrobnosti třídy a parametr:

### <a name="class"></a>Třída
| Název | Popis |
|--------------------|--------------------|
| ModelDataCollector | Třídy v oboru názvů azureml.datacollector. Instance této třídy se používá ke shromažďování dat modelu. Jeden soubor vyhodnocení může obsahovat více ModelDataCollectors. Každá instance by měla sloužit ke shromažďování dat na jednom místě diskrétní v souboru bodování tak, aby zůstala konzistentní schéma shromážděná data (to znamená, vstupy a predikcí)|


### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
|-------------|------------|-------------------------|
| model_name | řetězec | Název modelu, která data se shromažďují pro |
| identifikátor | řetězec | umístění v kódu, který identifikuje data, například 'RawInput' nebo 'předpovědi. |
| feature_names | seznam řetězců | seznam názvů funkcí, které se stanou záhlaví souboru csv, pokud je zadán |
| model_management_account_id | řetězec | identifikátor pro tento model se mají ukládat účtu pro správu modelu. To se vyplní automaticky při modely jsou zprovoznili jej prostřednictvím AML |
| webservice_name | řetězec | Název webové služby, ke kterému je tento model aktuálně nasazený. To se vyplní automaticky při modely jsou zprovoznili jej prostřednictvím AML |
| model_id | řetězec | Jedinečný identifikátor pro tento model v kontextu účtu služby Správa modelů. To se vyplní automaticky při modely jsou zprovoznili jej prostřednictvím AML |
| model_version | řetězec | číslo verze tohoto modelu v kontextu účtu služby Správa modelů. To se vyplní automaticky při modely jsou zprovoznili jej prostřednictvím AML |



 

## <a name="collecting-the-model-data"></a>Shromažďování dat modelu.

Můžete shromažďovat data model s využitím instanci ModelDataCollector vytvořili výše.

    dc.collect(input_data, user_correlation_id="")

Viz podrobnosti o metodu a parametru:

### <a name="method"></a>Metoda
| Název | Popis |
|--------------------|--------------------|
| shromažďování | Používá ke shromažďování dat pro model vstup nebo predikcí|


### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
|-------------|------------|-------------------------|
| input_data | více typů | shromažďování dat (aktuálně přijímá seznam typů, numpy.array, pandas. Datový rámec, pyspark.sql.DataFrame). Pro typy datového rámce, pokud existuje hlavička s názvy funkcí, tyto informace jsou uvedeny v cílovém umístění dat (aniž byste museli explicitně předávat názvy funkcí v konstruktoru ModelDataCollector) |
| user_correlation_id | řetězec | volitelné korelace id, které může zadat uživatel ke korelaci této předpovědi |

