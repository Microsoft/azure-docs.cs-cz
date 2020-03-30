---
title: Sledování a shromažďování dat z koncových bodů webové služby Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorování webových služeb nasazených pomocí Azure Machine Learning pomocí Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136189"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Sledování a shromažďování dat z koncových bodů webové služby ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak shromažďovat data z a monitorovat modely nasazené do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI) povolením Azure Application Insights přes Azure Application Insights přes Azure Application Insights 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning studio](#studio) ve společnostihttps://ml.azure.com

Kromě shromažďování výstupních dat a odezvy koncového bodu můžete sledovat:

* Četnost požadavků, doba odezvy a míra selhání
* Míra závislostí, doba odezvy a míra selhání
* Výjimky

[Další informace o Přehledech aplikací Azure](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes

* Pracovní prostor Azure Machine Learning, místní adresář, který obsahuje vaše skripty, a nainstalovaná sada Azure Machine Learning SDK pro Python. Informace o tom, jak tyto požadavky získat, najdete v [tématu Konfigurace vývojového prostředí](how-to-configure-environment.md)

* Trénovaný model strojového učení, který se má nasadit do služby Azure Kubernetes Service (AKS) nebo Azure Container Instance (ACI). Pokud ho nemáte, podívejte se na kurz [modelu klasifikace obrázků Vlak](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadata webové služby a data odpovědí

>[!Important]
> Azure Application Insights protokoluje pouze datové části až 64 kb. Pokud je dosaženo tohoto limitu, jsou zaznamenány pouze nejnovější výstupy modelu. 

Metadata a odpověď na službu – odpovídající metadatům webové služby a předpovědím modelu – jsou zaznamenány do trasování Azure Application Insights pod zprávou `"model_data_collection"`. Azure Application Insights můžete dotazovat přímo pro přístup k těmto datům nebo nastavit [nepřetržitý export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do účtu úložiště pro delší uchovávání informací nebo další zpracování. Data modelu pak můžete použít v Azure Machine Learning nastavit označování, retraining, vysvětlitelnost, analýzu dat nebo jiné použití. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Konfigurace pomocí sady Python SDK 

### <a name="update-a-deployed-service"></a>Aktualizace nasazené služby

1. Identifikujte službu ve vašem pracovním prostoru. Hodnota pro `ws` je název pracovního prostoru

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizujte svou službu a povolte Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Protokolovat vlastní trasování ve vaší službě

Pokud chcete protokolovat vlastní trasování, postupujte podle standardního procesu nasazení pro AKS nebo ACI v [jak nasadit a kde](how-to-deploy-and-where.md) dokumentu. Potom použijte následující kroky:

1. Aktualizace souboru vyhodnocování přidáním tiskových příkazů
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizace konfigurace služby
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Vytvořte bitovou kopii a nasaďte ji na [AKS nebo ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Zakázání sledování v Pythonu

Pokud chcete Zakázat Azure Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Konfigurace pomocí studia Azure Machine Learning

Azure Application Insights můžete taky povolit ze studia Azure Machine Learning, až budete připraveni nasadit model pomocí těchto kroků.

1. Přihlaste se do pracovního prostoru na adresehttps://ml.azure.com/
1. Přejděte na **Modely** a vyberte model, který chcete nasadit.
1. Vybrat **+Nasadit**
1. Naplnění formuláře **nasadit model**
1. Rozbalit nabídku **Upřesnit**

    ![Nasadit formulář](./media/how-to-enable-app-insights/deploy-form.png)
1. Vyberte **Povolit diagnostiku a shromažďování dat přehledů aplikací.**

    ![Povolení přehledů aplikací](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Vyhodnocení dat
Data vaší služby se ukládají ve vašem účtu Azure Application Insights ve stejné skupině prostředků jako Azure Machine Learning.
Chcete-li jej zobrazit:

1. Přejděte do pracovního prostoru Azure Machine Learning na [portálu Azure a](https://ms.portal.azure.com/) klikněte na odkaz Přehledy aplikací.

    [![Aplikace InsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Výběrem karty **Přehled** zobrazíte základní sadu metrik pro vaši službu.

   [![Přehled](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Chcete-li se podívat na metadata a odpověď na požadavek webové **služby,** vyberte tabulku požadavků v části **Protokoly (Analytics)** a výběrem **možnosti Spustit** zobrazíte požadavky.

   [![Data modelu](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Chcete-li se podívat do vlastních tras, vyberte **Analytics**
4. V části schématu vyberte **Trasování**. Pak vyberte **Spustit** a spusťte dotaz. Data by se měla zobrazovat ve formátu tabulky a měla by být mapována na vlastní hovory v souboru hodnocení.

   [![Vlastní trasování](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [Co je application insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Export dat pro další zpracování a delší uchovávání

>[!Important]
> Azure Application Insights podporuje jenom export do úložiště objektů blob. Další omezení této funkce exportu jsou uvedeny v [exportu telemetrie z App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

[Pomocí průběžného exportu](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) Azure Application Insights můžete odesílat zprávy do podporovaného účtu úložiště, kde lze nastavit delší uchovávání informací. Zprávy `"model_data_collection"` jsou uloženy ve formátu JSON a lze je snadno analyzovat extrahovat data modelu. 

Azure Data Factory, Azure ML pipelines nebo jiné nástroje pro zpracování dat se můžou použít k transformaci dat podle potřeby. Když jste transformovali data, pak je můžete zaregistrovat v pracovním prostoru Azure Machine Learning jako datové sady. Chcete-li tak učinit, naleznete [v tématu Jak vytvořit a zaregistrovat datové sady](how-to-create-register-datasets.md).

   [![Nepřetržitý export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Ukázkový poznámkový blok

Poznámkový blok [enable-app-insights-in-service-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak nasadit model do clusteru služby Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) nebo [jak nasadit model do instancí kontejnerů Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) k nasazení vašich modelů do koncových bodů webové služby a povolit Azure Application Insights k využití shromažďování dat a monitorování koncových bodů
* Viz [MLOps: Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) další informace o využití dat shromážděných z modelů v produkčním prostředí. Tato data mohou pomoci neustále zlepšovat proces strojového učení
