---
title: Monitorování a shromažďování dat z Machine Learning koncových bodů webové služby
titleSuffix: Azure Machine Learning
description: Monitorování webových služeb nasazených pomocí Azure Machine Learning pomocí Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 68c7f3082adbf10ee6f5b5e6b6fd0bf79232a7d0
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722420"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorování a shromažďování dat z koncových bodů webové služby ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak shromažďovat data z a monitorovat modely nasazené do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI) povolením Azure Application Insights. Kromě shromažďování vstupních dat a odpovědí koncového bodu můžete sledovat:

* Sazby požadavků, doba odezvy a frekvence selhání
* Sazby závislosti, doba odezvy a frekvence selhání
* Výjimky

[Přečtěte si další informace o Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Pracovnímu prostoru Azure Machine Learning, místní adresář, který obsahuje skripty a sady SDK Azure Machine Learning pro Python nainstalován. Informace o tom, jak tyto požadavky získat, najdete v tématu [Jak konfigurovat vývojové prostředí](how-to-configure-environment.md) .
* Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS) nebo Azure Container Instance (ACI). Pokud ho nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

## <a name="web-service-metadata-and-response-data"></a>Metadata a data odpovědi webové služby

>[!Important]
> Azure Application Insights jenom zapisuje jenom datové části až 64 KB. Pokud je dosaženo tohoto limitu, budou protokolovány pouze nejaktuálnější výstupy modelu. 

Metadata a odpověď na službu – odpovídající metadatům webové služby a předpovědi modelu – se zaznamenávají do trasování Azure Application Insights v `"model_data_collection"`zpráv. Můžete se dotázat na Azure Application Insights přímo pro přístup k těmto datům nebo nastavit [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do účtu úložiště pro delší dobu uchovávání nebo dalšího zpracování. Data modelu se pak dají použít v Azure Machine Learning k nastavení označování, rekurze, vyjasnění, analýze dat nebo jiné použití. 

## <a name="use-the-azure-portal-to-configure"></a>Ke konfiguraci použijte Azure Portal

V Azure Portal můžete povolit a zakázat službu Azure Application Insights. 

1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.

1. Na kartě **nasazení** vyberte službu, ve které chcete povolit Azure Application Insights

   [![seznam služeb na kartě nasazení](./media/how-to-enable-app-insights/Deployments.PNG)](././media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Vybrat **Upravit**

   [![– tlačítko pro úpravy](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. V okně **Upřesnit nastavení**zaškrtněte políčko **Povolit diagnostiku AppInsights** .

   [![zaškrtnuté políčko pro povolení diagnostiky](./media/how-to-enable-app-insights/AdvancedSettings.png)](././media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. V dolní části obrazovky vyberte **aktualizovat** , aby se změny projevily.

### <a name="disable"></a>Zakázat

1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.
1. Vyberte **nasazení**, vyberte službu a pak vyberte **Upravit** .

   [![použít tlačítko Upravit](././media/how-to-enable-app-insights/Edit.PNG)](./././media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. V části **Upřesnit nastavení**zrušte zaškrtnutí políčka **Povolit diagnostiku AppInsights** .

   [zaškrtnutí políčka ![pro povolení diagnostiky nezaškrtnuté.](./media/how-to-enable-app-insights/uncheck.png)](././media/how-to-enable-app-insights/uncheck.png#lightbox)

1. V dolní části obrazovky vyberte **aktualizovat** , aby se změny projevily.
 
## <a name="use-python-sdk-to-configure"></a>Použití sady Python SDK ke konfiguraci 

### <a name="update-a-deployed-service"></a>Aktualizace nasazené službě

1. Identifikaci služby ve vašem pracovním prostoru. Hodnota pro `ws` je název vašeho pracovního prostoru.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizace služby a povolení Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Vlastní trasování protokolu ve službě

Pokud chcete protokolovat vlastní trasování, postupujte podle standardního procesu nasazení pro AKS nebo ACI v tématu [postup nasazení a umístění](how-to-deploy-and-where.md) dokumentu. Pak pomocí následujících kroků:

1. Aktualizace souboru bodování přidáním příkazů Print
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizace konfigurace služby
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Sestavte image a nasaďte ji na [AKS nebo ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Zakázání sledování v Pythonu

Pokud chcete zakázat službu Azure Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Vyhodnocení dat
Data vaší služby se ukládají do účtu Azure Application Insights v rámci stejné skupiny prostředků jako Azure Machine Learning.
Chcete-li zobrazit ho:

1. Přejděte do pracovního prostoru Azure Machine Learning v aplikaci [Azure Machine Learning Studio](https://ml.azure.com) a klikněte na odkaz Application Insights.

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Výběrem karty **Přehled** zobrazíte základní sadu metrik pro vaši službu.

   [Přehled ![](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Pokud chcete vyhledat požadavky na metadata a odpověď vaší webové služby, vyberte tabulku **požadavků** v části **protokoly (analýza)** a vyberte **Spustit** pro zobrazení požadavků.

   [![data modelu](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Pokud chcete hledat vlastní trasování, vyberte **Analytics** .
4. V části schématu vyberte **trasování**. Pak vyberte **Spustit** a spusťte dotaz. Data by se měla zobrazit ve formátu tabulky a měla by se namapovat na vaše vlastní volání v souboru bodování.

   [![vlastní trasování](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportovat data pro další zpracování a delší dobu uchování

>[!Important]
> Azure Application Insights podporuje jenom exporty do úložiště objektů BLOB. Další omezení této možnosti exportu jsou uvedená v části [Export telemetrie z App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

Můžete použít [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) v Azure Application Insights k posílání zpráv do podporovaného účtu úložiště, kde je možné nastavit delší dobu uchování. Zprávy `"model_data_collection"` jsou uloženy ve formátu JSON a lze je snadno analyzovat pro extrakci dat modelu. 

Azure Data Factory, kanály Azure ML nebo jiné nástroje pro zpracování dat se dají použít k transformaci dat podle potřeby. Po transformaci dat je můžete zaregistrovat v pracovním prostoru Azure Machine Learning jako datovou sadu. Postup najdete v tématu [jak vytvořit a zaregistrovat datové sady](how-to-create-register-datasets.md).

   [![průběžný export](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [Enable-App-Insights-in-produkční-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak nasadit model do clusteru služby Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) nebo [Jak nasadit model, Azure Container Instances aby](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) se nasadily vaše modely do koncových bodů webové služby, a umožněte službě Azure Application Insights využívat shromažďování dat a monitorování koncových bodů.
* Další informace o využití dat shromážděných z modelů v produkčním prostředí najdete v tématu [MLOps: Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Tato data vám můžou pomoci při neustálém vylepšování procesu strojového učení.
