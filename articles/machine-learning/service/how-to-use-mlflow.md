---
title: MLflow pomocí služby Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak protokolování metrik a artefakty pomocí knihovny MLflow do služby Azure Machine Learning
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 2f3ec9792a7af0398da770286b805be4d1f25589
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657966"
---
# <a name="use-mlflow-with-azure-machine-learning-service-preview"></a>MLflow pomocí služby Azure Machine Learning (Preview)

Tento článek ukazuje, jak používat MLflow na identifikátor URI pro sledování a protokolování rozhraní API, souhrnně označované také jako MLflow sledování, službou Azure Machine Learning, sledování a protokolů, metriky experiment a artefakty v vaše [Azure Machine Learning pracovní prostor služby](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Pokud už používáte MLflow sledování pro své experimenty, tento pracovní prostor poskytuje centralizovaný, zabezpečené a škálovatelné umístění pro uložení školení metriky a modely.

[MLflow](https://www.mlflow.org) open source knihovnu pro správu životního cyklu vaší se strojovým učením. [Sledování MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) je součástí MLflow, který se přihlásí a sleduje trénování spustit metriky a model artefakty, zda jsou vaše experimenty místně, spusťte na virtuálním počítači nebo na vzdáleném výpočetní cluster.
![mlflow s diagramem azure machine learning](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Porovnání klientů MLflow a Azure Machine Learning

 Níže uvedená tabulka shrnuje různé klienty využívající službu Azure Machine Learning a jejími funkcemi příslušné funkce.

 Sledování MLflow nabízí metriky protokolování a artefaktů funkce úložiště, které jsou jinak k dispozici pouze prostřednictvím [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | Sledování MLflow | Azure Machine Learning <br> Python SDK |  Azure Machine Learning <br> Rozhraní příkazového řádku | portál Azure|
|-|-|-|-|-|
| Správa pracovního prostoru |   | ✓ |  ✓ | ✓  |
| Použití úložiště dat  |   | ✓ |  ✓ |    |
| Protokolujte metriky      | ✓ | ✓ |    |    |
| Odešlete artefakty | ✓ | ✓ |    |    |
| Zobrazit metriky     | ✓ | ✓ | ✓  | ✓ |
| Správa výpočetních služeb   |   | ✓ | ✓  | ✓ |
| Modely nasazení    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Nainstalujte Azure Machine Learning Python SDK v místním počítači a vytvořte pracovní prostor služby Azure Machine Learning](setup-create-workspace.md#sdk). Sada SDK poskytuje připojení pro MLflow pro přístup k pracovním prostoru.

## <a name="track-local-runs"></a>Sledovat místní spuštění

Nainstalujte `azureml-contrib-run` balíček určený MLflow sledování pomocí služby Azure Machine Learning na své experimenty místně v editoru kódu nebo Poznámkový blok Jupyter spustit.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Obor názvů azureml.contrib často mění, protože pracujeme na vylepšení služby. V důsledku toho cokoli, co je v tomto oboru názvů by měl být považovány za verzi preview a nejsou plně podporovány společností Microsoft.

Import `mlflow` a [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) tříd pro přístup k MLflow uživatele sledování identifikátor URI a konfigurujte váš pracovní prostor.

V následujícím kódu `get_mlflow_tracking_uri()` metoda přiřadí jedinečný sledování adresa URI do pracovního prostoru, `ws`, a `set_tracking_uri()` odkazuje MLflow sledování identifikátoru URI k této adrese.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>Identifikátor URI pro sledování je platný až na jednu hodinu nebo méně. Restartovat skript po určité době nečinnosti, pomocí rozhraní API get_mlflow_tracking_uri získat nový identifikátor URI.

Nastavte název experimentu MLflow s `set_experiment()` a začněte spouštět s trénování `start_run()`. Pak pomocí `log_metric()` aktivovat MLflow protokolování rozhraní API a zahájí protokolování trénování spustit metriky.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Sledování vzdálených běhů

Vzdálené spuštění umožňují trénování modelů na výkonnější výpočetní prostředí, jako jsou virtuální počítače s podporou grafického procesoru nebo Machine Learning Compute clusterů. Zobrazit [nastavení cílových výpočetních prostředí k tréninku modelu](how-to-set-up-training-targets.md) Další informace o různých výpočetních možností.

Výpočetní prostředky a školení, spusťte prostředí s nakonfigurovat [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) třídy. Zahrnout `mlflow` a `azure-contrib-run` nástroje pip balíčky v prostředí [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) oddílu. Potom vytvořte [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) s vzdálené výpočetních prostředků jako cílové výpočetní prostředí.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

Ve vašem skriptu školení importovat `mlflow` MLflow protokolování rozhraní API a spustit protokolování spuštění metriky.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

K tomuto výpočetní prostředky a školení spustit konfiguraci, použijte `Experiment.submit("train.py")` metody k odeslání spuštění. Tím automaticky nastaví MLflow sledování identifikátor URI a přesměruje protokolování z MLflow do pracovního prostoru.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Zobrazení metrik a artefaktů ve vašem pracovním prostoru

Metriky a artefakty z MLflow protokolování se zachovají v pracovním prostoru na [webu Azure portal](https://portal.azure.com). Chcete-li je zobrazit kdykoli, přejděte do pracovního prostoru a vyhledání experimentu podle názvu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nemáte v úmyslu použít zaznamenané metriky a artefaktů ve vašem pracovním prostoru, je momentálně není k dispozici možnost odstranit je jednotlivě. Místo toho odstraňte skupinu prostředků, který obsahuje účet úložiště a pracovní prostor, takže nebudou účtovat žádné poplatky:

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.

   ![Odstranit na webu Azure Portal](media/how-to-use-mlflow/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

## <a name="example-notebooks"></a>Příklad poznámkové bloky

[MLflow s poznámkovými bloky Azure ML](https://aka.ms/azureml-mlflow-examples) ukazuje koncepty v tomto článku.

## <a name="next-steps"></a>Další kroky

* [Nasazení modelu](how-to-deploy-and-where.md).
