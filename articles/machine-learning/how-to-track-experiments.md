---
title: Experimenty protokolu ML & metriky
titleSuffix: Azure Machine Learning
description: Sledujte experimenty Azure ML a sledujte metriky spuštění, abyste vylepšili proces vytváření modelů. Přidejte do školicího skriptu protokolování pomocí rutiny run. log, spusťte. start_logging nebo ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 44fe71f575a32ccc1a687bc87793cb6a8b6508a9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650619"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Povolit protokolování v běhu školicích kurzů Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Sada SDK Azure Machine Learning Pythonu umožňuje protokolovat informace v reálném čase pomocí výchozího balíčku protokolování Pythonu a funkcí specifických pro sadu SDK. Můžete se přihlásit místně a odeslat protokoly do svého pracovního prostoru na portálu.

Protokoly vám můžou pomáhat při diagnostice chyb a upozornění nebo sledovat metriky výkonu, jako jsou parametry a výkon modelu. V tomto článku se dozvíte, jak povolit protokolování v následujících scénářích:

> [!div class="checklist"]
> * Interaktivní školicí cvičení
> * Odesílání školicích úloh pomocí ScriptRunConfig
> * Nativní nastavení Pythonu `logging`
> * Protokolování z dalších zdrojů


> [!TIP]
> V tomto článku se dozvíte, jak monitorovat proces školení modelu. Pokud vás zajímá monitorování využití prostředků a událostí z Azure Machine Learningu, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelů, přečtěte si téma [monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Typy dat

Můžete protokolovat více datových typů, včetně skalárních hodnot, seznamů, tabulek, obrázků, adresářů a dalších. Další informace a příklady kódu Pythonu pro různé datové typy najdete na stránce s [odkazem na třídu spuštění](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true).

## <a name="interactive-logging-session"></a>Relace interaktivního protokolování

Relace interaktivního protokolování se obvykle používají v prostředích poznámkových bloků. Metoda [experiment. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) spustí interaktivní relaci protokolování. Všechny metriky zaznamenávané během relace jsou přidány do záznamu spuštění v experimentu. Metoda [Run. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) ukončí relace a označí běh jako dokončený.

## <a name="scriptrunconfig-logs"></a>Protokoly ScriptRunConfig

V této části se dozvíte, jak přidat kód protokolování do ScriptConfigch spuštění. Třídu [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) můžete použít k zapouzdření skriptů a prostředí pro opakující se běhy. Tuto možnost můžete použít také k zobrazení widgetu vizuálních poznámkových bloků Jupyter pro monitorování.

V tomto příkladu se provádí rozmazání parametrů přes alfa hodnoty a zachycuje výsledky pomocí metody [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truelog-name--value--description----) .

1. Vytvořte školicí skript, který obsahuje logiku protokolování, `train.py` .

   [! Code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Odešlete ```train.py``` skript, který se spustí v prostředí spravovaném uživatelem. Je odeslána celá složka skriptu pro školení.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = src)] [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? název = run)]

    `show_output`Parametr zapne podrobné protokolování, které vám umožní zobrazit podrobnosti z procesu školení a také informace o všech vzdálených prostředcích nebo výpočetních cílech. Pomocí následujícího kódu zapněte podrobné protokolování při odeslání experimentu.

```python
run = exp.submit(src, show_output=True)
```

Můžete také použít stejný parametr ve `wait_for_completion` funkci ve výsledném běhu.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Nativní protokolování v Pythonu

Některé protokoly v sadě SDK mohou obsahovat chybu, která dává pokyn k nastavení úrovně protokolování na ladění. Chcete-li nastavit úroveň protokolování, přidejte do skriptu následující kód.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Další zdroje protokolování

Azure Machine Learning může během školení protokolovat také informace z jiných zdrojů, jako jsou například automatizované běhy strojového učení nebo kontejnery Docker spouštějící úlohy. Tyto protokoly nejsou dokumentovány, ale pokud narazíte na problémy a kontaktujete podporu Microsoftu, můžou při řešení potíží používat tyto protokoly.

Informace o protokolování metrik v Návrháři Azure Machine Learning (Preview) najdete v tématu [postup protokolu metrik v Návrháři (Preview)](how-to-track-designer-experiments.md) .

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Následující poznámkové bloky ukazují koncepty v tomto článku:
* [Postupy: použití-AzureML/školení/výuka v místním prostředí](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [Postupy: použití-AzureML/Track-and-monitor-experimenty/protokolování – rozhraní API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak používat Azure Machine Learning, najdete v těchto článcích:

* Naučte se [Protokolovat metriky v návrháři Azure Machine Learning (Preview)](how-to-track-designer-experiments.md).

* Podívejte se na příklad, jak registrovat nejlepší model a nasadit ho v kurzu, [Naučte si model klasifikace imagí pomocí Azure Machine Learning](tutorial-train-models-with-aml.md).
