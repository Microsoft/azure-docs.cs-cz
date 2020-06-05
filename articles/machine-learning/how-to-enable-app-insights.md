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
ms.date: 03/12/2020
ms.openlocfilehash: 925b562189d713dd4cb1e72aa8b8fae28fcde0a5
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433229"
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

>[!Important]
> Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, budou protokolovány pouze nejaktuálnější výstupy modelu. 

Metadata a odpověď na službu, která odpovídá metadatům webové služby a předpovědi modelu, se zaznamenávají do trasování Azure Application Insights v rámci zprávy `"model_data_collection"` . Můžete se dotázat na Azure Application Insights přímo pro přístup k těmto datům nebo nastavit [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do účtu úložiště pro delší dobu uchovávání nebo dalšího zpracování. Data modelu se pak dají použít v Azure Machine Learning k nastavení označování, rekurze, vyjasnění, analýze dat nebo jiné použití. 

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

1. Aktualizace souboru bodování přidáním příkazů Print
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
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

1. Výběrem karty **Přehled** zobrazíte základní sadu metrik pro vaši službu.

   [![Přehled](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Pokud chcete vyhledat požadavky na metadata a odpověď vaší webové služby, vyberte tabulku **požadavků** v části **protokoly (analýza)** a vyberte **Spustit** pro zobrazení požadavků.

   [![Data modelu](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Pokud chcete hledat vlastní trasování, vyberte **Analytics** .
4. V části schématu vyberte **trasování**. Pak vyberte **Spustit** a spusťte dotaz. Data by se měla zobrazit ve formátu tabulky a měla by se namapovat na vaše vlastní volání v souboru bodování.

   [![Vlastní trasování](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportovat data pro další zpracování a delší dobu uchování

>[!Important]
> Azure Application Insights podporuje jenom exporty do úložiště objektů BLOB. Další omezení této možnosti exportu jsou uvedená v části [Export telemetrie z App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Můžete použít [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) v Azure Application Insights k posílání zpráv do podporovaného účtu úložiště, kde je možné nastavit delší dobu uchování. `"model_data_collection"`Zprávy jsou uloženy ve formátu JSON a lze je snadno analyzovat pro extrakci dat modelu. 

Azure Data Factory, kanály Azure ML nebo jiné nástroje pro zpracování dat se dají použít k transformaci dat podle potřeby. Po transformaci dat je můžete zaregistrovat v pracovním prostoru Azure Machine Learning jako datovou sadu. Postup najdete v tématu [jak vytvořit a zaregistrovat datové sady](how-to-create-register-datasets.md).

   [![Průběžný export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [Enable-App-Insights-in-produkční-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak nasadit model do clusteru služby Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) nebo [Jak nasadit model, Azure Container Instances aby](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) se nasadily vaše modely do koncových bodů webové služby, a umožněte službě Azure Application Insights využívat shromažďování dat a monitorování koncových bodů.
* Další informace o využití dat shromážděných z modelů v produkčním prostředí najdete v tématu [MLOps: Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Tato data vám můžou pomoci při neustálém vylepšování procesu strojového učení.
