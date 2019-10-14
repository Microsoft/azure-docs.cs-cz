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
ms.date: 10/11/2019
ms.custom: seoapril2019
ms.openlocfilehash: c16b6d769aa191b0e8ac86768a7eafd35ccbc3b9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301022"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorování a shromažďování dat z koncových bodů webové služby ML

V tomto článku se dozvíte, jak shromažďovat data z a monitorovat modely nasazené do koncových bodů webové služby ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI) povolením Azure Application Insights. Kromě shromažďování vstupních dat a odpovědí koncového bodu můžete sledovat:
* Míry požadavků, doby odezvy a míry selhání.
* Míry závislosti, doby odezvy a míry selhání.
* Výjimek.

[Přečtěte si další informace o Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

* Azure Machine Learning pracovní prostor, místní adresář, který obsahuje vaše skripty a nainstalovanou sadu Azure Machine Learning SDK pro Python. Informace o tom, jak tyto požadavky získat, najdete v tématu [Jak konfigurovat vývojové prostředí](how-to-configure-environment.md).
* Školený model strojového učení, který se má nasadit do služby Azure Kubernetes (AKS) nebo Azure Container instance (ACI). Pokud ho nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

## <a name="web-service-input-and-response-data"></a>Data vstupu a odpovědi webové služby

Vstup a odpověď na službu – odpovídající vstupům k modelu ML a jeho předpovědi – jsou protokolovány do Azure Application Insights trasování v rámci zprávy `"model_data_collection"`. Můžete se dotázat na Azure Application Insights přímo pro přístup k těmto datům nebo nastavit [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do účtu úložiště pro delší dobu uchovávání nebo dalšího zpracování. Data modelu se pak dají ve službě Azure ML použít k nastavení popisků, rekurzování, vyjasnění, analýzy dat nebo jiné použití. 

## <a name="use-the-azure-portal-to-configure"></a>Ke konfiguraci použijte Azure Portal

V Azure Portal můžete povolit a zakázat službu Azure Application Insights. 

1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.

1. Na kartě **nasazení** vyberte službu, ve které chcete povolit Azure Application Insights.

   [@no__t – 1List služeb na kartě nasazení](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Vyberte **Upravit**.

   [@no__t – tlačítko 1Edit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. V části **Upřesnit nastavení**zaškrtněte políčko **Povolit diagnostiku AppInsights** .

   [zaškrtávací políčko @no__t – 1Selected pro povolení diagnostiky](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. V dolní části obrazovky vyberte **aktualizovat** , aby se změny projevily. 

### <a name="disable"></a>Zakázat
1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.
1. Vyberte **nasazení**, vyberte službu a pak vyberte **Upravit**.

   [@no__t – 1Use tlačítko pro úpravy](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. V části **Upřesnit nastavení**zrušte zaškrtnutí políčka **Povolit diagnostiku AppInsights** . 

   [zaškrtávací políčko @no__t – 1Cleared pro povolení diagnostiky](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. V dolní části obrazovky vyberte **aktualizovat** , aby se změny projevily. 
 
## <a name="use-python-sdk-to-configure"></a>Použití sady Python SDK ke konfiguraci 

### <a name="update-a-deployed-service"></a>Aktualizace nasazené služby
1. Identifikujte službu ve vašem pracovním prostoru. Hodnota pro `ws` je název vašeho pracovního prostoru.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizujte svou službu a povolte Azure Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Protokolovat vlastní trasování ve službě
Pokud chcete protokolovat vlastní trasování, postupujte podle standardního procesu nasazení pro AKS nebo ACI v tématu [postup nasazení a umístění](how-to-deploy-and-where.md) dokumentu. Pak použijte následující postup:

1. Aktualizujte soubor bodování přidáním příkazů Print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizujte konfiguraci služby.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Sestavte image a nasaďte ji na [AKS](how-to-deploy-to-aks.md) nebo [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Zakázat sledování v Pythonu

Pokud chcete zakázat službu Azure Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="evaluate-data"></a>Vyhodnotit data
Data vaší služby se ukládají do účtu Azure Application Insights v rámci stejné skupiny prostředků jako Azure Machine Learning.
Zobrazení:
1. V [Azure Portal](https://portal.azure.com)přejdete do pracovního prostoru služby Machine Learning. Klikněte na odkaz Azure Application Insights.

    [@no__t – 1AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Výběrem karty **Přehled** zobrazíte základní sadu metrik pro vaši službu.

   [@no__t – 1Overview](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Chcete-li se podívat na vstupy a výstupy vaší webové služby, vyberte **Analýza** .
1. V části schéma vyberte **trasování** a vyfiltrujte trasování pomocí zprávy `"model_data_collection"`. Ve vlastních dimenzích můžete zobrazit vstupy, předpovědi a další relevantní podrobnosti.

   [data @no__t – 1Model](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Pokud chcete vyhledat vlastní trasování, vyberte **Analytics**.
4. V části schématu vyberte **trasování**. Pak vyberte **Spustit** a spusťte dotaz. Data by se měla zobrazit ve formátu tabulky a měla by se namapovat na vaše vlastní volání v souboru bodování. 

   [@no__t – trasování 1Custom](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak používat Azure Application Insights, najdete v tématu [co je Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Exportovat data pro další zpracování a delší dobu uchování

Můžete použít [průběžný export](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) v Azure Application Insights k posílání zpráv do podporovaného účtu úložiště, kde je možné nastavit delší dobu uchování. Zprávy `"model_data_collection"` jsou uloženy ve formátu JSON a lze je snadno analyzovat pro extrakci dat modelu. Azure Data Factory, kanály Azure ML nebo jiné nástroje pro zpracování dat se dají použít k transformaci dat podle potřeby. Po transformaci dat ji můžete zaregistrovat v pracovním prostoru služby Azure Machine Learning jako datovou sadu.

   [@no__t – export 1Continuous](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [Enable-App-Insights-in-produkční-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další kroky

* Podívejte [se, jak nasadit model do clusteru služby Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) nebo [Jak nasadit model, Azure Container Instances aby](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) se nasadily vaše modely do koncových bodů webové služby, a umožněte službě Azure Application Insights využívat shromažďování a koncových údajů. sledovaný.
* Další informace o využití dat shromážděných z modelů v produkčním prostředí najdete v tématu [MLOps: Správa, nasazení a monitorování modelů pomocí Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) . Tato data vám můžou pomoci při neustálém vylepšování procesu strojového učení. 
