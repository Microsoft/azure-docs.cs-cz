---
title: Modely monitorování pomocí Application Insights
titleSuffix: Azure Machine Learning service
description: Použití Application Insights a monitorujte webové služby nasazené prostřednictvím služby Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.custom: seodec18
ms.openlocfilehash: 385f829002d65335c8039e478c148b6140148ad8
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117244"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Monitorování vašich modelů Azure Machine Learning s využitím Application Insights

V tomto článku se dozvíte, jak nastavit Azure Application Insights pro vaši službu Azure Machine Learning. Application Insights nabízí možnost monitorovat:
* Požádat o sazby, doby odezvy a míra selhání.
* Míra závislosti, doby odezvy a míra selhání.
* Výjimky.

[Další informace o Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Požadavky

* Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

* Pracovnímu prostoru Azure Machine Learning, místní adresář, který obsahuje skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách, najdete v článku [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md).
* Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS) nebo Azure Container Instance (ACI). Pokud ho nemáte, přečtěte si článek [Train model klasifikace obrázků](tutorial-train-models-with-aml.md) kurzu.


## <a name="enable-and-disable-from-the-sdk"></a>Povolení a zákaz ze sady SDK

### <a name="update-a-deployed-service"></a>Aktualizace nasazené službě
1. Identifikaci služby ve vašem pracovním prostoru. Hodnota pro `ws` je název vašeho pracovního prostoru.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizovat službu a povolte Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Vlastní trasování protokolu ve službě
Pokud chcete vlastní trasování protokolů, postupujte podle procesu standardní nasazení pro AKS nebo ACI v [nasazení a kde](how-to-deploy-and-where.md) dokumentu. Pak pomocí následujících kroků:

1. Aktualizujte soubor vyhodnocení přidáním tisku příkazy.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizujte konfiguraci služby.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Sestavte image a nasadit virtuální počítač na [AKS](how-to-deploy-to-aks.md) nebo [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Zakázání sledování v Pythonu

Pokud chcete zakázat Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="enable-and-disable-in-the-portal"></a>Povolit nebo zakázat na portálu

Můžete povolit nebo zakázat Application Insights na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com), otevřete pracovní prostor.

1. Na **nasazení** kartu, vyberte službu, ve které chcete povolit Application Insights.

   [![Seznam služeb na kartě nasazení](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Vyberte **Upravit**.

   [![Tlačítko Upravit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. V **Upřesnit nastavení**, vyberte **diagnostiku AppInsights povolit** zaškrtávací políčko.

   [![Zaškrtnuté zaškrtávací políčko pro povolení diagnostiky](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Vyberte **aktualizace** v dolní části obrazovky, aby se změny projevily. 

### <a name="disable"></a>Zakázat
1. V [webu Azure portal](https://portal.azure.com), otevřete pracovní prostor.
1. Vyberte **nasazení**, vyberte službu a vyberte **upravit**.

   [![Použijte tlačítko pro úpravy](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. V **Upřesnit nastavení**, zrušte zaškrtnutí políčka **diagnostiku AppInsights povolit** zaškrtávací políčko. 

   [![Nezaškrtnuté zaškrtávací políčko pro povolení diagnostiky](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Vyberte **aktualizace** v dolní části obrazovky, aby se změny projevily. 
 

## <a name="evaluate-data"></a>Vyhodnocení dat
Vaše služba data se ukládají v účtu služby Application Insights ve stejné skupině prostředků jako službu Azure Machine Learning.
Chcete-li zobrazit ho:
1. Přejděte do pracovního prostoru služby Machine Learning v [webu Azure portal](https://portal.azure.com) a klikněte na odkaz Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Vyberte **přehled** kartu pro zobrazení základní sadu metriky pro vaši službu.

   [![Přehled](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Do vaší vlastní trasování, že vyberete **Analytics**.
4. V části schématu vyberte **trasy**. Potom vyberte **spustit** ke spuštění dotazu. Data by se měla objevit ve formátu tabulky a měli namapovat na vlastní volání v souboru bodování. 

   [![Vlastní trasy](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak pomocí Application Insights najdete v tématu [co je Application Insights?](../../azure-monitor/app/app-insights-overview.md).
    

## <a name="example-notebook"></a>Příklad poznámkového bloku

[How-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) Poznámkový blok ukazuje koncepty v tomto článku. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
Může také shromažďovat data o vašich modelů v produkčním prostředí. Přečtěte si článek [shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md). 


## <a name="other-references"></a>Další odkazy
* [Azure Monitor pro kontejnery](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
