---
title: Experimenty při sledování a nasazování modelů
titleSuffix: Azure Data Science Virtual Machine
description: Přečtěte si, jak sledovat a protokolovat experimenty z Data Science Virtual Machine pomocí Azure Machine Learning a/nebo MLFlow.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 17418b0255182934045acc9174b34cff2aefff99
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307443"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Sledovat experimenty a nasazovat modely v Azure Machine Learning

Vylepšete proces vytváření modelů sledováním experimentů a monitorováním metrik spuštění. V tomto článku se dozvíte, jak přidat kód protokolování do školicího skriptu pomocí rozhraní [MLflow](https://mlflow.org/) API a jak sledovat experiment v Azure Machine Learning.

Následující diagram znázorňuje, že se sledováním MLflow sledujete metriky spuštění experimentu a artefakty modelu úložiště v pracovním prostoru Azure Machine Learning.

![sledovat experimenty](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Předpoklady

* Musíte [zřídit pracovní prostor Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Vytvoření nového poznámkového bloku

Sada Azure Machine Learning a sada MLFlow SDK jsou v Data Science VM předem nainstalovány a lze k nim přistupovat v prostředí * *azureml_py36_ \** _ conda. V Jupyterlab klikněte na spouštěč a vyberte následující jádro:

![Výběr jádra](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Nastavení pracovního prostoru

Přejít na [Azure Portal](https://portal.azure.com) a vyberte pracovní prostor, který jste zřídili jako součást požadavků. Uvidíte, že je potřeba __stáhnout config.js__ (viz níže) – Stáhněte si konfiguraci a ujistěte se, že je uložená ve vašem pracovním adresáři na DSVM.

![Získat konfigurační soubor](./media/how-to-track-experiments/experiment-tracking-2.png)

Konfigurace obsahuje informace, jako je název pracovního prostoru, předplatné atd. a znamená, že nemusíte tyto parametry pevně nakódovat.

## <a name="track-dsvm-runs"></a>Sledování spuštění DSVM

Přidejte následující kód do svého poznámkového bloku (nebo skriptu) a nastavte objekt pracovního prostoru AzureML.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
Identifikátor URI sledování je platný až do hodin nebo méně. Pokud po nějaké době nečinnosti svůj skript restartujete, použijte rozhraní get_mlflow_tracking_uri API k získání nového identifikátoru URI.

### <a name="load-the-data"></a>Načtení dat

Tento příklad používá datovou sadu diabetes, známou malou datovou sadu, která je dodávána s scikit-učí. Tato buňka načte datovou sadu a rozdělí ji do náhodných školicích a testovacích sad.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Přidat sledování

Přidejte sledování experimentů pomocí Azure Machine Learning SDK a nahrajte trvalý model do záznamu experimentálního spuštění. Následující kód přidá protokoly a nahraje soubor modelu do spuštění experimentu. Model je také zaregistrován v registru Azure Machine Learningho modelu.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Zobrazit běhy v Azure Machine Learning

Můžete zobrazit experiment spustit v [Azure Machine Learning Studio](https://ml.azure.com). V nabídce na levé straně klikněte na __experimenty__ a vyberte experiment_with_mlflow (nebo pokud jste se rozhodli pojmenovat experiment jinak ve výše uvedeném fragmentu kódu, klikněte na název, který jste použili):

![vybrat experiment](./media/how-to-track-experiments/mlflow-experiments.png)

Měla by se zobrazit zpráva zaznamenaná střední hodnota (MSE):

![PROGRAMEM](./media/how-to-track-experiments/mlflow-experiments-2.png)

Pokud kliknete na spuštění, zobrazí se další podrobnosti a také rozevírací model ve __výstupech a protokolech__ .

## <a name="deploy-model-in-azure-machine-learning"></a>Nasadit model v Azure Machine Learning

V této části se naučíme, jak nasadit modely vyškolené na DSVM a Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Krok 1: vytvoření odvození výpočetních prostředků

V nabídce vlevo v části [AzureML Studio](https://ml.azure.com) klikněte na __výpočty__ a pak na kartu __odvození clustery__ . Potom klikněte na __+ Nový__ , jak je popsáno níže:

![Vytvořit odvozenou výpočetní prostředky](./media/how-to-track-experiments/mlflow-experiments-6.png)

V podokně __nové odvození clusterů__ zadejte podrobnosti pro:

_ COMPUTE název
* Služba Kubernetes – vyberte vytvořit novou.
* Vyberte oblast.
* Vyberte velikost virtuálního počítače (pro účely tohoto kurzu je výchozí hodnota Standard_D3_v2 dostatečná).
* Účel clusteru – vybrat __vývoj a testování__
* Počet uzlů by se měl rovnat __1__ .
* Konfigurace sítě – Basic

Potom klikněte na __vytvořit__.

![Podrobnosti o výpočetním prostředí](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Krok 2: nasazení neodvozené služby bez kódu

Když jsme zaregistrovali model v našem kódu pomocí `register_model` , určili jsme rozhraní jako skriptu sklearn. Azure Machine Learning nepodporuje žádná nasazení kódu pro následující architektury:

* scikit-learn
* Formát Tensorflow SaveModel
* Formát modelu ONNX

Nasazení bez kódu znamená, že můžete nasadit přímo z artefaktu modelu, aniž byste museli zadat nějaký konkrétní skript bodování.

Pokud chcete nasadit model diabetes, přejděte do nabídky vlevo v [Azure Machine Learning Studio](https://ml.azure.com) a vyberte __modely__. Potom klikněte na registrovanou diabetes_model:

![Vybrat model](./media/how-to-track-experiments/mlflow-experiments-3.png)

Potom v podokně Podrobnosti modelu klikněte na tlačítko __nasadit__ :

![Nasazení](./media/how-to-track-experiments/mlflow-experiments-4.png)

Tento model nasadíme do clusteru odvození (Azure Kubernetes Service), který jsme vytvořili v kroku 1. Vyplňte níže uvedené podrobnosti zadáním názvu služby a názvu AKS výpočetního clusteru (vytvořeného v kroku 1). Doporučujeme také zvýšit __kapacitu kapacity procesoru__ na 1 (od 0,1) a __rezervu paměti__ na 1 (od 0,5) – Toto zvýšení můžete udělat kliknutím na __Upřesnit__ a vyplněním podrobností. Pak klikněte na __nasadit__.

![Podrobnosti nasazení](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Krok 3: využití

Po úspěšném nasazení modelu byste měli vidět následující (pro získání této stránky klikněte na koncové body v nabídce na levé straně > potom klikněte na název nasazené služby):

![Spotřebovat model](./media/how-to-track-experiments/mlflow-experiments-8.png)

Měli byste vidět, že stav nasazení přejde z __přechodu__ do __stavu v pořádku__. Tato část podrobností navíc poskytuje koncovým bodem REST a adresám URL Swagger, které může vývojář aplikace použít k integraci modelu ML do svých aplikací.

Koncový bod můžete otestovat pomocí [post](https://www.postman.com/)nebo můžete použít sadu test SDK:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Krok 4: vyčištění

Odstraňte výpočetní prostředky, které jste vytvořili v kroku 1, abyste se neúčtují průběžné poplatky za výpočetní výkon. V nabídce na levé straně Azure Machine Learning Studio klikněte na COMPUTE > odvození clusterů > vyberte výpočetní > odstranit.

## <a name="next-steps"></a>Další kroky

* Další informace o [nasazování modelů v AzureML](../how-to-deploy-and-where.md)