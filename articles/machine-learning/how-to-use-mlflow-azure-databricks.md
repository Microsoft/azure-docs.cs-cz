---
title: Sledování MLflow pro pokusy Azure Databricks ML
titleSuffix: Azure Machine Learning
description: Nastavte MLflow s Azure Machine Learning pro protokolování metrik a artefaktů z Azure Databricksch pokusů.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: c1bef16d89a22e7df43e1f473697b4577d080d4c
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107888165"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Sledování experimentů Azure Databricks ML pomocí MLflow a Azure Machine Learning (Preview)

V tomto článku se dozvíte, jak povolit sledování identifikátorů URI a protokolovacího rozhraní API pro MLflow, které je souhrnně známé jako [MLflow Tracking](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), aby se připojovaly vaše experimenty Azure DATABRICKS (ADB), MLflow a Azure Machine Learning.

[MLflow](https://www.mlflow.org) je open source knihovna pro správu životního cyklu experimentů ve strojovém učení. Sledování MLFlow je součást MLflow, která protokoluje a sleduje metriky běhu a artefakty modelu. Přečtěte si další informace o [Azure Databricks a MLflow](/azure/databricks/applications/mlflow/). 

Další informace o integraci funkcí MLflow a Azure Machine Learning najdete v tématu [sledování experimentů s MLflow a Azure Machine Learning](how-to-use-mlflow.md) .

>[!NOTE]
> Jako open source knihovna se MLflow změny často. Funkce, které jsou dostupné prostřednictvím Azure Machine Learning a integrace MLflow, by se měly považovat za verzi Preview, a není plně podporovaná Microsoftem.

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro odborníky přes data a vývojáře, kteří chtějí monitorovat proces školení modelu. Pokud jste správcem a chcete monitorovat využití prostředků a události z Azure Machine Learning, jako jsou kvóty, dokončené školicí běhy nebo dokončená nasazení modelu, přečtěte si téma [monitorování Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Požadavky

* Nainstalujte balíček `azureml-mlflow`. 
    * Tento balíček automaticky přinese `azureml-core` [sadu SDK Azure Machine Learning Pythonu](/python/api/overview/azure/ml/install), která poskytuje možnosti připojení pro MLflow k vašemu pracovnímu prostoru.
* [Azure Databricks pracovní prostor a cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md).
    * Podívejte se, která [přístupová oprávnění potřebujete k provádění operací MLflow s vaším pracovním prostorem](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Sledování spuštění Azure Databricks

MLflow sledování pomocí Azure Machine Learning umožňuje ukládat zaznamenané metriky a artefakty z Azure Databricks spouští do obou: 

* Azure Databricks pracovní prostor.
* Pracovní prostor služby Azure Machine Learning

Po vytvoření pracovního prostoru Azure Databricks a clusteru 

1. Nainstalujte knihovnu *AzureML-mlflow* z PyPI a ujistěte se, že má váš cluster přístup k potřebným funkcím a třídám.

1. Nastavte Poznámkový blok experimentu.

1. Připojte pracovní prostor Azure Databricks a pracovní prostor Azure Machine Learning.

Další podrobnosti k těmto krokům najdete v následujících částech, abyste mohli úspěšně spustit MLflow experimenty s Azure Databricks. 

## <a name="install-libraries"></a>Instalovat knihovny

Pokud chcete do clusteru nainstalovat knihovny, přejděte na kartu **knihovny** a vyberte **nainstalovat nový** .

 ![mlflow s využitím Azure datacihly](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

Do pole **balíček** zadejte AzureML-mlflow a pak vyberte nainstalovat. Opakujte tento krok, jak je potřeba k instalaci dalších dalších balíčků do clusteru pro váš experiment.

 ![Knihovna mlflow pro instalaci Azure DB](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Nastavení poznámkového bloku 

Po nastavení clusteru ADB, 
1. V levém navigačním podokně vyberte **pracovní prostory** . 
1. Rozbalte rozevírací nabídku pracovní prostory a vyberte **importovat** .
1. Přetáhnutím nebo procházením vyhledejte, Poznámkový blok experimentu pro import vašeho pracovního prostoru ADB.
1. Vyberte **Importovat**. Poznámkový blok experimentu se automaticky otevře.
1. Pod nadpisem poznámkového bloku v levém horním rohu vyberte cluster, který chcete připojit k poznámkovém bloku experimentu. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Připojení pracovních prostorů Azure Databricks a Azure Machine Learning

Propojení pracovního prostoru ADB k pracovnímu prostoru Azure Machine Learning umožňuje sledovat data experimentů v pracovním prostoru Azure Machine Learning.

Pokud chcete připojit pracovní prostor ADB k novému nebo existujícímu pracovnímu prostoru Azure Machine Learning, 
1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com).
1. Přejděte na stránku **Přehled** v pracovním prostoru ADB.
1. V pravém dolním rohu vyberte tlačítko **propojit Azure Machine Learning pracovní prostor** . 

 ![Propojení pracovních prostorů Azure DB a Azure Machine Learning](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>Sledování MLflow ve vašich pracovních prostorech

Po vytvoření instance pracovního prostoru se sledování MLflow automaticky nastaví tak, aby se sledovalo ve všech následujících místech:

* Propojený pracovní prostor Azure Machine Learning.
* Původní pracovní prostor ADB 

Všechny vaše experimenty jsou ve spravované službě sledování Azure Machine Learning.

Následující kód by měl být v poznámkovém bloku experimentu, abyste získali propojený pracovní prostor Azure Machine Learning. 

Tento kód 

*  Načte podrobnosti o předplatném Azure za účelem vytvoření instance pracovního prostoru Azure Machine Learning. 

* Předpokládá, že máte existující skupinu prostředků a Azure Machine Learning pracovní prostor, jinak je můžete [vytvořit](how-to-manage-workspace.md). 

* Nastaví název experimentu. `user_name`Tady je konzistentní s `user_name` přidruženým k pracovnímu prostoru Azure Databricks.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Nastavení sledování MLflow jenom na sledování v pracovním prostoru Azure Machine Learning

Pokud upřednostňujete správu sledovaných experimentů v centralizovaném umístění, můžete nastavit sledování MLflow **jenom** na sledovat v pracovním prostoru Azure Machine Learning. 

Do skriptu vložte následující kód:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

V školicím skriptu proveďte import `mlflow` tak, aby používal rozhraní API pro protokolování MLflow, a spusťte protokolování metriky spuštění. Následující příklad zaznamená metriku epocha ztrát. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registrace modelů pomocí MLflow

Po vyškolení modelu můžete své modely zaznamenat a zaregistrovat na serveru pro sledování back-endu pomocí `mlflow.<model_flavor>.log_model()` metody. `<model_flavor>`, odkazuje na rozhraní, které je přidruženo k modelu. [Seznamte se s typy modelů, které jsou podporovány](https://mlflow.org/docs/latest/models.html#model-api). 

Serverem pro sledování back-endu je ve výchozím nastavení Azure Databricks pracovní prostor. Pokud jste nezvolili [Nastavení sledování MLflow jenom na sledování v pracovním prostoru Azure Machine Learning](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), je server pro sledování back-endu Azure Machine Learning pracovním prostorem.   

* **Pokud registrovaný model s názvem neexistuje**, zaregistruje metoda nový model, vytvoří verzi 1 a vrátí objekt ModelVersion MLflow. 

* **Pokud registrovaný model s tímto názvem již existuje**, metoda vytvoří novou verzi modelu a vrátí objekt verze. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Vytváření koncových bodů pro modely MLflow

Až budete připraveni vytvořit koncový bod pro vaše modely ML. Můžete nasadit jako, 

* Webová služba Azure Machine Learning Request-Response pro interaktivní bodování. Toto nasazení umožňuje využít a použít správu modelů Azure Machine Learning a možnosti detekce posunu dat pro produkční modely. 

* Objekty modelu MLFlow, které se dají použít v kanálech streamování nebo Batch jako funkce Pythonu nebo PANDAS UDF v Azure Databricks pracovním prostoru.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Nasazení modelů do koncových bodů Azure Machine Learning 
Můžete využít rozhraní API [mlflow. AzureML. deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) k nasazení modelu do pracovního prostoru Azure Machine Learning. Pokud jste model zaregistrovali pouze do Azure Databricks pracovního prostoru, jak je popsáno v části [registrace modelů s MLflow](#register-models-with-mlflow) , zadejte `model_name` parametr pro registraci modelu do pracovního prostoru Azure Machine Learning. 

Azure Databricks spuštění lze nasadit do následujících koncových bodů, 
* [Instance kontejneru Azure](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Nasazení modelů do koncových bodů ADB pro dávkové vyhodnocování 

Pro dávkové vyhodnocování můžete zvolit Azure Databricks clustery. Model MLFlow se načte a použije jako Spark PANDAS UDF k vyhodnocení nových dat. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud neplánujete použít zaznamenané metriky a artefakty v pracovním prostoru, možnost jejich odstranění je momentálně nedostupná. Místo toho odstraňte skupinu prostředků, která obsahuje účet úložiště a pracovní prostor, takže se vám neúčtují žádné poplatky:

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.

   ![Odstranit v Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

## <a name="example-notebooks"></a>Příklady poznámkových bloků

[MLflow s Azure Machine Learning poznámkovým blokům](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) předvádí a rozbalí v konceptech uvedených v tomto článku.

## <a name="next-steps"></a>Další kroky
* [Nasaďte modely MLflow jako webovou službu Azure](how-to-deploy-mlflow-models.md). 
* [Spravujte své modely](concept-model-management-and-deployment.md).
* [Sledujte experimenty s MLflow a Azure Machine Learning](how-to-use-mlflow.md). 
* Přečtěte si další informace o [Azure Databricks a MLflow](/azure/databricks/applications/mlflow/).