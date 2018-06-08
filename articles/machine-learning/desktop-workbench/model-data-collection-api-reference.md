---
title: Referenční dokumentace Azure Machine Learning modelu dat kolekce API | Microsoft Docs
description: Azure Machine Learning modelu dat kolekce API odkaz.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ff80130ebb9c4095d4a20202cdfabd9aaf1b1992
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832002"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Azure Machine Learning modelu dat kolekce API – referenční informace

Shromažďování dat modelu umožňuje archivovat vstupy modelu a predikcím ze strojového učení webové služby. Najdete v článku [modelu dat kolekce postupy průvodce](how-to-use-model-data-collection.md) pochopit, jak nainstalovat `azureml.datacollector` na počítači pro Windows a Linux.

V této příručce referenční dokumentace rozhraní API používáme postupného o tom, jak shromažďovat vstupy modelu a předpovědi ze strojového učení webové služby.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Povolení shromažďování dat modelu Azure ML pracovní prostředí

 Vyhledejte conda\_dependencies.yml souborů ve vašem projektu ve složce aml_config a mít vaše conda\_dependencies.yml soubor obsahovat modul azureml.datacollector části pip, jak je uvedeno níže. Všimněte si, že se jedná pouze podmnožinu úplné conda\_dependencies.yml souboru:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>V současné době můžete použít modul kolekce dat v Azure ML Workbench spuštěním v režimu docker. Místní režimu nemusí fungovat pro všechna prostředí.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Povolení shromažďování dat modelu v vyhodnocování souboru

V vyhodnocování souboru, který se používá pro operationalization importujte modul sady kolekcí dat a třída ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Vytváření instancí kolekce dat modelu
Vytvořit novou instanci třídy ModelDataCollector:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Zobrazit podrobnosti třídy a parametr:

### <a name="class"></a>Třída
| Název | Popis |
|--------------------|--------------------|
| ModelDataCollector | Třída v oboru názvů azureml.datacollector. Instance této třídy se používá ke shromažďování dat modelu. Jeden vyhodnocování soubor může obsahovat více ModelDataCollectors. Každá instance se mají použít pro shromažďování dat do jedné diskrétní umístění v vyhodnocování souboru tak, aby zůstala konzistentní schéma shromážděná data (tj. vstupy a předpovědi)|


### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
|-------------|------------|-------------------------|
| model_name | řetězec | Název modelu, která data se shromažďují pro |
| Identifikátor | řetězec | umístění v kódu, který identifikuje tato data, tj. 'RawInput' nebo 'předpovědi. |
| feature_names | seznam řetězců | seznam názvů funkcí, které se stanou csv záhlaví při zadaný |
| model_management_account_id | řetězec | identifikátor pro účet správy modelu se uloží tento model. To je naplněny automaticky při modely jsou operationalized prostřednictvím AML |
| webservice_name | řetězec | Název webové služby, ke kterému je tento model aktuálně nasazená. To je naplněny automaticky při modely jsou operationalized prostřednictvím AML |
| model_id | řetězec | Jedinečný identifikátor pro tento model v kontextu účtu správy modelu. To je naplněny automaticky při modely jsou operationalized prostřednictvím AML |
| model_version | řetězec | číslo verze tohoto modelu v kontextu účtu správy modelu. To je naplněny automaticky při modely jsou operationalized prostřednictvím AML |



 

## <a name="collecting-the-model-data"></a>Shromažďování dat modelu.

Můžete shromáždit data modelu pomocí instance ModelDataCollector vytvořili výše.

    dc.collect(input_data, user_correlation_id="")

Podrobnosti najdete v tématu metoda a parametr:

### <a name="method"></a>Metoda
| Název | Popis |
|--------------------|--------------------|
| shromažďování | Používá ke shromažďování dat pro model vstup nebo předpovědi|


### <a name="parameters"></a>Parametry

| Název | Typ | Popis |
|-------------|------------|-------------------------|
| input_data | více typů | data, která mají být shromažďovány (aktuálně přijímá seznam typů, numpy.array, pandas. DataFrame, pyspark.sql.DataFrame). Pro typy dataframe, pokud existuje hlavička s názvy funkce tato informace je obsažena v cílovém umístění dat (bez nutnosti explicitně předávání názvů funkcí v konstruktoru ModelDataCollector) |
| user_correlation_id | řetězec | volitelné korelace id, které lze zadat uživatel ke korelaci tento předpovědi |

