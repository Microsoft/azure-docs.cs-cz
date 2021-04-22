---
title: Monitorování a shromažďování dat z Machine Learning koncových bodů webové služby
titleSuffix: Azure Machine Learning
description: Naučte se shromažďovat data z modelů nasazených do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: blackmist
ms.date: 09/15/2020
ms.topic: how-to
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: f3853ecc5a3741b485f779581da387b133065ed5
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107889353"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorování a shromažďování dat z koncových bodů webové služby ML


V tomto článku se dozvíte, jak shromažďovat data z modelů nasazených do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI). Pomocí služby [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) shromážděte následující data z koncového bodu:
* Výstupní data
* Odpovědi
* Sazby požadavků, doba odezvy a frekvence selhání
* Sazby závislosti, doba odezvy a frekvence selhání
* Výjimky

Poznámkový blok [Enable-App-Insights-in-produkční-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku.
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
 
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – Vyzkoušejte si [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).

* Azure Machine Learning pracovní prostor, místní adresář, který obsahuje vaše skripty a nainstalovanou sadu Azure Machine Learning SDK pro Python. Další informace najdete v tématu [jak nakonfigurovat vývojové prostředí](how-to-configure-environment.md).

* Školený model strojového učení. Další informace najdete v kurzu [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

<a name="python"></a>

## <a name="configure-logging-with-the-python-sdk"></a>Konfigurace protokolování pomocí Python SDK

V této části se dozvíte, jak povolit protokolování Application Insight pomocí sady Python SDK. 

### <a name="update-a-deployed-service"></a>Aktualizace nasazené služby

K aktualizaci existující webové služby použijte následující postup:

1. Identifikujte službu ve vašem pracovním prostoru. Hodnota pro `ws` je název vašeho pracovního prostoru.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizace služby a povolení Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Protokolovat vlastní trasování ve službě

> [!IMPORTANT]
> Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, může se zobrazit chyba, například nedostatek paměti, nebo nemusí být protokolovány žádné informace. Pokud jsou data, která chcete protokolovat, větší než 64 KB, měli byste ji místo toho ukládat do úložiště objektů BLOB pomocí informací v části [shromažďování dat pro modely v produkčním](how-to-enable-data-collection.md)prostředí.
>
> U složitějších situací, jako je sledování modelů v rámci nasazení AKS, doporučujeme použít knihovnu třetí strany, jako je [OpenCensus](https://opencensus.io).

Pokud chcete protokolovat vlastní trasování, postupujte podle standardního procesu nasazení pro AKS nebo ACI v tématu [Jak nasadit a kde](how-to-deploy-and-where.md) dokument. Pak použijte následující postup:

1. Aktualizujte soubor bodování přidáním příkazů Print k odeslání dat do Application Insights při odvozování. Pro složitější informace, jako jsou například data žádosti a odpověď, použijte strukturu JSON. 

    Následující příklady `score.py` souborů protokolu, pokud byl model inicializován, vstup a výstup během odvození a čas výskytu chyby.

    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. Aktualizujte konfiguraci služby a ujistěte se, že jste povolili Application Insights.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Sestavte image a nasaďte ji na AKS nebo ACI. Další informace naleznete v tématu [Jak nasadit a kde](how-to-deploy-and-where.md).


### <a name="disable-tracking-in-python"></a>Zakázat sledování v Pythonu

Pokud chcete zakázat službu Azure Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="configure-logging-with-azure-machine-learning-studio"></a>Konfigurace protokolování pomocí Azure Machine Learning studia

Službu Azure Application Insights můžete taky povolit z Azure Machine Learning studia. Až budete připraveni nasadit svůj model jako webovou službu, pomocí následujícího postupu povolte Application Insights:

1. Přihlaste se k studiu na adrese https://ml.azure.com .
1. Přejít na **modely** a vybrat model, který chcete nasadit.
1. Vyberte  **+ nasadit**.
1. Naplňte formulář **nasazení modelu** .
1. Rozbalte nabídku **Upřesnit** .

    ![Formulář nasazení](./media/how-to-enable-app-insights/deploy-form.png)
1. Vyberte **Povolit diagnostiku Application Insights a shromažďování dat**.

    ![Povolit App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Zobrazit metriky a protokoly

### <a name="query-logs-for-deployed-models"></a>Protokoly dotazů pro nasazené modely

Protokoly koncových bodů v reálném čase jsou zákaznická data. Funkci můžete použít `get_logs()` k načtení protokolů z dříve nasazené webové služby. Protokoly mohou obsahovat podrobné informace o všech chybách, ke kterým došlo během nasazení.

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()

# load existing web service
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```

Pokud máte více tenantů, možná budete muset přidat následující ověřovací kód před `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

### <a name="view-logs-in-the-studio"></a>Zobrazit protokoly v studiu

Azure Application Insights ukládá vaše protokoly služby ve stejné skupině prostředků jako pracovní prostor Azure Machine Learning. Pomocí následujících kroků zobrazíte data s využitím studia:

1. V [studiu](https://ml.azure.com/)přejdete do svého pracovního prostoru Azure Machine Learning.
1. Vyberte **koncové body**.
1. Vyberte nasazenou službu.
1. Vyberte odkaz **Application Insights URL** .

    [![Najít adresu URL Application Insights](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. V Application Insights na kartě **Přehled** nebo v části __monitorování__ vyberte __protokoly__.

    [![Karta Přehled monitorování](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Chcete-li zobrazit informace zaznamenané ze souboru score.py, podívejte se na tabulku __trasování__ . Následující dotaz vyhledá protokoly, ve kterých byla __vstupní__ hodnota zaznamenána:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![trasovat data](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="web-service-metadata-and-response-data"></a>Metadata a data odpovědi webové služby

> [!IMPORTANT]
> Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, může dojít k chybám, jako je například nedostatek paměti, nebo nemůžete zaznamenat žádné informace.

Pokud chcete protokolovat informace o žádosti webové služby, přidejte `print` do souboru Score.py příkazy. Každý `print` příkaz má za následek jednu položku v tabulce trasování Application Insights v rámci zprávy `STDOUT` . Application Insights ukládá `print` výstupy příkazů do  `customDimensions` a v `Contents` tabulce trasování. Tisk řetězců JSON vytvoří hierarchickou strukturu dat ve výstupu trasování v části `Contents` .

## <a name="export-data-for-retention-and-processing"></a>Exportovat data pro uchovávání a zpracování

>[!Important]
> Azure Application Insights podporuje jenom exporty do úložiště objektů BLOB. Další informace o omezeních této implementace najdete v tématu [Export telemetrie z App Insights](../azure-monitor/app/export-telemetry.md#continuous-export-advanced-storage-configuration).

Pokud chcete exportovat data do účtu úložiště BLOB, kde můžete definovat nastavení uchovávání, použijte Application Insights [průběžný export](../azure-monitor/app/export-telemetry.md) . Application Insights exportuje data ve formátu JSON. 

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Průběžný export":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak povolit protokolování a zobrazit protokoly pro koncové body webové služby. Další kroky si můžete vyzkoušet v těchto článcích:


* [Postup nasazení modelu do clusteru AKS](./how-to-deploy-azure-kubernetes-service.md)

* [Postup nasazení modelu pro Azure Container Instances](./how-to-deploy-azure-container-instance.md)

* [MLOps: můžete spravovat, nasazovat a monitorovat modely pomocí Azure Machine Learning](./concept-model-management-and-deployment.md) a získat další informace o využití dat shromážděných z modelů v produkčním prostředí. Tato data vám můžou pomoci při neustálém vylepšování procesu strojového učení.