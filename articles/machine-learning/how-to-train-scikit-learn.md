---
title: Výukové scikity – Naučte se modely strojového učení
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spouštět scikit skripty pro školení na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 28401b5900640ed7228d7c7caad0cebbabf00a65
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532716"
---
# <a name="train-scikit-learn-models-at-scale-with-azure-machine-learning"></a>Naučte se scikit modely s možností škálování pomocí Azure Machine Learning

V tomto článku se dozvíte, jak spouštět scikit skripty s učením pomocí Azure Machine Learning.

V ukázkových skriptech v tomto článku se používají ke klasifikaci imagí květů Iris k sestavení modelu machine learningu založeného na [datové sadě Iris](https://archive.ics.uci.edu/ml/datasets/iris)scikit-učení.

Bez ohledu na to, jestli provedete výukový model Machine Learning scikit z provozu nebo do cloudu převedete existující model, můžete použít Azure Machine Learning k horizontálnímu navýšení kapacity Open-Source školicích úloh pomocí elastických výpočetních prostředků pro Cloud. Pomocí Azure Machine Learning můžete sestavovat, nasazovat, používat a monitorovat modely produkčního prostředí.

## <a name="prerequisites"></a>Předpoklady

Spusťte tento kód v jednom z těchto prostředí:
 - Azure Machine Learning výpočetní instance – nepotřebujete žádné soubory ke stažení nebo instalaci

    - Dokončete [kurz: instalační prostředí a pracovní prostor](tutorial-1st-experiment-sdk-setup.md)  pro vytvoření vyhrazeného serveru poznámkového bloku předem načteného se sadou SDK a s ukázkovým úložištěm.
    - Ve složce školení ukázek na serveru poznámkového bloku najděte dokončený a rozbalený Poznámkový blok tak, že přejdete na tento adresář: How-to---- **> ml-frameworks > scikit-učí > výuka skriptu sklearn**

 - Váš vlastní server Jupyter Notebook

    - [Nainstalujte sadu Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) (>= 1.13.0).
    - [Vytvořte konfigurační soubor pracovního prostoru](how-to-configure-environment.md#workspace).

## <a name="set-up-the-experiment"></a>Nastavení experimentu

Tato část nastavuje experiment pro školení načtením požadovaných balíčků Pythonu, inicializací pracovního prostoru, definováním školicího prostředí a přípravou školicího skriptu.

### <a name="initialize-a-workspace"></a>Inicializovat pracovní prostor

[Azure Machine Learning pracovní prostor](concept-workspace.md) je prostředek nejvyšší úrovně pro službu. Poskytuje centralizované místo pro práci se všemi artefakty, které vytvoříte. V sadě Python SDK máte přístup k artefaktům pracovního prostoru vytvořením [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) objektu.

Vytvořte objekt pracovního prostoru ze `config.json` souboru vytvořeného v [části požadavky](#prerequisites).

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="prepare-scripts"></a>Příprava skriptů

V tomto kurzu se školicí skript **train_iris. py** [pro vás už](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)poskytuje. V praxi byste měli být schopni vzít libovolný vlastní školicí skript a spustit ho s Azure ML bez nutnosti upravovat kód.

Poznámky:
- Zadaný školicí skript ukazuje, jak protokolovat některé metriky do běhu Azure ML pomocí `Run` objektu v rámci skriptu.
- Zadaný školicí skript používá ukázková data z  `iris = datasets.load_iris()` funkce.  Pokud chcete používat vlastní data a přistupovat k nim, přečtěte si téma [Postup výuky s](how-to-train-with-datasets.md) datovými sadami k zpřístupnění dat během školení.

### <a name="define-your-environment"></a>Definice prostředí

Pokud chcete definovat [prostředí](concept-environments.md) Azure ml, které zapouzdřuje závislosti školicího skriptu, můžete buď definovat vlastní prostředí, použití a prostředí Azure ml.

#### <a name="create-a-custom-environment"></a>Vytvoření vlastního prostředí

Pokud chcete vytvořit vlastní prostředí, definujte závislosti conda v souboru YAML; v tomto příkladu je soubor pojmenován `conda_dependencies.yml` .

```yaml
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

Vytvořte prostředí Azure ML z této specifikace prostředí conda. Prostředí se zabalí do kontejneru Docker za běhu.
```python
from azureml.core import Environment

sklearn_env = Environment.from_conda_specification(name='sklearn-env', file_path='conda_dependencies.yml')
```

Další informace o vytváření a používání prostředí najdete v tématu [vytváření a používání softwarových prostředí v Azure Machine Learning](how-to-use-environments.md).

#### <a name="use-a-curated-environment"></a>Použití spravovaného prostředí
V případě potřeby poskytuje Azure ML předem vytvořená a poduspořádaná prostředí, pokud nechcete vytvořit vlastní image. Další informace najdete [tady](resource-curated-environments.md).
Pokud chcete použít konkrétní prostředí, můžete místo toho spustit následující příkaz:

```python
sklearn_env = Environment.get(workspace=ws, name='AzureML-Tutorial')
```

## <a name="configure-and-submit-your-training-run"></a>Konfigurace a odeslání školicích běhů

### <a name="create-a-scriptrunconfig"></a>Vytvoření ScriptRunConfig
Vytvořte objekt ScriptRunConfig a zadejte podrobnosti o konfiguraci školicí úlohy, včetně vašeho školicího skriptu, prostředí, které se má použít, a výpočetní cíl, který se má spustit.
Jakékoli argumenty školicího skriptu budou předány prostřednictvím příkazového řádku, pokud je zadáno v `arguments` parametru.

Následující kód provede konfiguraci objektu ScriptRunConfig pro odeslání úlohy ke spuštění na místním počítači.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      environment=sklearn_env)
```

Pokud chcete tuto úlohu místo toho spustit ve vzdáleném clusteru, můžete zadat požadovaný výpočetní cíl pro `compute_target` parametr ScriptRunConfig.

```python
from azureml.core import ScriptRunConfig

compute_target = ws.compute_targets['<my-cluster-name>']
src = ScriptRunConfig(source_directory='.',
                      script='train_iris.py',
                      arguments=['--kernel', 'linear', '--penalty', 1.0],
                      compute_target=compute_target,
                      environment=sklearn_env)
```

### <a name="submit-your-run"></a>Odeslat běh
```python
from azureml.core import Experiment

run = Experiment(ws,'train-iris').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Azure Machine Learning spouští školicí skripty zkopírováním celého zdrojového adresáře. Pokud máte citlivá data, která nechcete nahrávat, použijte [soubor. Ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) nebo ho nezahrnujte do zdrojového adresáře. Místo toho ke svým datům dostanete přístup pomocí [datové sady](how-to-train-with-datasets.md)Azure ml.

### <a name="what-happens-during-run-execution"></a>Co se stane při spuštění
Po spuštění se spustí v následujících fázích:

- **Příprava**: image Docker se vytvoří podle definovaného prostředí. Obrázek se nahraje do registru kontejneru v pracovním prostoru a v mezipaměti pro pozdější spuštění. Protokoly se také streamují do historie spuštění a dají se zobrazit ke sledování průběhu. Pokud je místo toho zadáno spravované prostředí, bude použit obrázek uložený v mezipaměti, který bude toto prostředí obsahovat.

- **Škálování**: cluster se pokusí o horizontální navýšení kapacity, pokud Batch AI cluster vyžaduje více uzlů pro spuštění běhu, než je aktuálně k dispozici.

- **Spuštěno**: všechny skripty ve složce skriptu se nahrají do cílového výpočetního prostředí, úložiště dat se připojí nebo zkopírují a `script` spustí se. Výstupy z stdout a složky **./logs** se streamují do historie spuštění a dají se použít k monitorování běhu.

- **Následné zpracování**: složka **./Outputs** se v běhu kopíruje do historie spuštění.

## <a name="save-and-register-the-model"></a>Uložení a registrace modelu

Po proškolení modelu ho můžete uložit a zaregistrovat do svého pracovního prostoru. Registrace modelu umožňuje ukládat a modelovat vaše modely do svého pracovního prostoru, aby bylo možné zjednodušit [správu modelů a nasazení](concept-model-management-and-deployment.md).

Do skriptu pro školení přidejte následující kód train_iris. py pro uložení modelu. 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

Zaregistrujte model do svého pracovního prostoru pomocí následujícího kódu. Zadáním parametrů `model_framework` , `model_framework_version` a `resource_configuration` nebudete mít k dispozici nasazení modelu bez kódu. Nasazení modelu bez kódu vám umožňuje přímo nasadit model jako webovou službu z registrovaného modelu a [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py&preserve-view=true) objekt definuje výpočetní prostředek pro webovou službu.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>Nasazení

Model, který jste právě zaregistrovali, se dá nasadit přesně stejným způsobem jako jakýkoli jiný registrovaný model v Azure ML. Postup nasazení obsahuje část týkající se registrace modelů, ale můžete přeskočit přímo na [Vytvoření výpočetního cíle](how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení, protože již máte registrovaný model.

### <a name="preview-no-code-model-deployment"></a>Tisk Nasazení modelu bez kódu

Místo tradičního postupu nasazení můžete také použít funkci nasazení bez kódu (Preview) pro scikit-učení. Nasazení modelu bez kódu se nepodporuje pro všechny integrované typy modelů scikit-učení. Registrací modelu, jak je uvedeno výše, `model_framework` pomocí `model_framework_version` parametrů, a `resource_configuration` můžete jednoduše použít [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) statickou funkci pro nasazení modelu.

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

Poznámka: tyto závislosti jsou součástí předem připraveného kontejneru scikit-učení.

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

Úplný [postup](how-to-deploy-and-where.md) pokrývá nasazení v Azure Machine Learning s větší hloubkou.


## <a name="next-steps"></a>Další kroky

V tomto článku jste si naučili a zaregistrovali model scikit-učení a seznámili jste se s možnostmi nasazení. Další informace o Azure Machine Learning najdete v těchto článcích.

* [Sledovat metriky spuštění během školení](how-to-track-experiments.md)
* [Ladění hyperparametrů](how-to-tune-hyperparameters.md)
