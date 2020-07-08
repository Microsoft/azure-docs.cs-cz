---
title: Monitorování a shromažďování dat z Machine Learning koncových bodů webové služby
titleSuffix: Azure Machine Learning
description: Monitorování webových služeb nasazených pomocí Azure Machine Learning pomocí Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 06/09/2020
ms.custom: tracking-python
ms.openlocfilehash: d28cd3b1d8722970505eb313bd8e80589ce9ff87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743502"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorování a shromažďování dat z koncových bodů webové služby ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak shromažďovat data z a monitorovat modely nasazené do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI) povolením Azure Application Insights přes 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning Studio](#studio) vhttps://ml.azure.com

Kromě shromažďování výstupních dat a odpovědí koncového bodu můžete sledovat:

* Sazby požadavků, doba odezvy a frekvence selhání
* Sazby závislosti, doba odezvy a frekvence selhání
* Výjimky

[Přečtěte si další informace o Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet, ještě než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Azure Machine Learning pracovní prostor, místní adresář, který obsahuje vaše skripty a nainstalovanou sadu Azure Machine Learning SDK pro Python. Informace o tom, jak tyto požadavky získat, najdete v tématu [Jak konfigurovat vývojové prostředí](how-to-configure-environment.md) .

* Školený model strojového učení, který se má nasadit do služby Azure Kubernetes (AKS) nebo Azure Container instance (ACI). Pokud ho nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

## <a name="web-service-metadata-and-response-data"></a>Metadata a data odpovědi webové služby

> [!IMPORTANT]
> Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, může dojít k chybám, jako je například nedostatek paměti, nebo nemůžete zaznamenat žádné informace.

Chcete-li protokolovat informace o požadavku na webovou službu, přidejte `print` do souboru Score.py příkazy. Každý `print` příkaz má za následek jednu položku v tabulce trasování v Application Insights v rámci zprávy `STDOUT` . Obsah `print` příkazu bude obsažen v části `customDimensions` a potom `Contents` v tabulce trasování. Pokud vytisknete řetězec JSON, vytvoří hierarchickou strukturu dat ve výstupu trasování v části `Contents` .

Můžete se dotázat na Azure Application Insights přímo pro přístup k těmto datům nebo nastavit [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do účtu úložiště pro delší dobu uchovávání nebo dalšího zpracování. Data modelu se pak dají použít v Azure Machine Learning k nastavení označování, rekurze, vyjasnění, analýze dat nebo jiné použití. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Použití sady Python SDK ke konfiguraci 

### <a name="update-a-deployed-service"></a>Aktualizace nasazené služby

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

Pokud chcete protokolovat vlastní trasování, postupujte podle standardního procesu nasazení pro AKS nebo ACI v tématu [postup nasazení a umístění](how-to-deploy-and-where.md) dokumentu. Pak použijte následující postup:

1. Chcete-li odesílat data do Application Insights při odvozování, aktualizujte soubor bodování přidáním příkazů Print. Chcete-li protokolovat složitější informace, jako jsou například data žádosti a odpověď, strukturu JSON. Následující příklad souboru score.py protokoluje čas inicializace modelu, vstup a výstup během odvození a čas výskytu chyby:

    > [!IMPORTANT]
    > Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, může se zobrazit chyba, například nedostatek paměti, nebo nemusí být protokolovány žádné informace. Pokud jsou data, která chcete protokolovat, větší než 64 KB, měli byste ji místo toho ukládat do úložiště objektů BLOB pomocí informací v části [shromažďování dat pro modely v produkčním](how-to-enable-data-collection.md)prostředí.
    
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

2. Aktualizace konfigurace služby
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Sestavte image a nasaďte ji na [AKS nebo ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Zakázat sledování v Pythonu

Pokud chcete zakázat službu Azure Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Použití Azure Machine Learning studia ke konfiguraci

Pokud jste připraveni nasadit model pomocí těchto kroků, můžete Azure Application Insights taky povolit z Azure Machine Learning studia.

1. Přihlaste se k pracovnímu prostoru na adresehttps://ml.azure.com/
1. Přejít na **modely** a vybrat model, který chcete nasadit
1. Vybrat **+ nasadit**
1. Naplnění formuláře **nasazení modelu**
1. Rozbalte nabídku **Upřesnit** .

    ![Formulář nasazení](./media/how-to-enable-app-insights/deploy-form.png)
1. Vyberte **Povolit diagnostiku Application Insights a shromažďování dat** .

    ![Povolit App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Vyhodnotit data
Data vaší služby se ukládají do účtu Azure Application Insights v rámci stejné skupiny prostředků jako Azure Machine Learning.
Zobrazení:

1. V [Azure Portal](https://ms.portal.azure.com/) přejděte do svého pracovního prostoru Azure Machine Learning a klikněte na odkaz Application Insights.

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Na kartě **Přehled** nebo v části __monitorování__ v seznamu na levé straně vyberte __protokoly__.

    [![Karta Přehled monitorování](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Chcete-li zobrazit informace zaznamenané ze souboru score.py, podívejte se na tabulku __trasování__ . Následující dotaz vyhledá protokoly, ve kterých byla __vstupní__ hodnota zaznamenána:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![trasovat data](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportovat data pro další zpracování a delší dobu uchování

>[!Important]
> Azure Application Insights podporuje jenom exporty do úložiště objektů BLOB. Další omezení této možnosti exportu jsou uvedená v části [Export telemetrie z App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Můžete použít [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) v Azure Application Insights k posílání zpráv do podporovaného účtu úložiště, kde je možné nastavit delší dobu uchování. Data jsou uložena ve formátu JSON a lze je snadno analyzovat pro extrakci dat modelu. 

Azure Data Factory, kanály Azure ML nebo jiné nástroje pro zpracování dat se dají použít k transformaci dat podle potřeby. Po transformaci dat je můžete zaregistrovat v pracovním prostoru Azure Machine Learning jako datovou sadu. Postup najdete v tématu [jak vytvořit a zaregistrovat datové sady](how-to-create-register-datasets.md).

   [![Průběžný export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [Enable-App-Insights-in-produkční-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak nasadit model do clusteru služby Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) nebo [Jak nasadit model, Azure Container Instances aby](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) se nasadily vaše modely do koncových bodů webové služby, a umožněte službě Azure Application Insights využívat shromažďování dat a monitorování koncových bodů.
* Další informace o využití dat shromážděných z modelů v produkčním prostředí najdete v tématu [MLOps: Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Tato data vám můžou pomoci při neustálém vylepšování procesu strojového učení.
