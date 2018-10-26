---
title: Povolení Application Insights pro službu Azure Machine Learning v produkčním prostředí
description: Informace o nastavení Application Insights pro službu Azure Machine Learning pro nasazení do služby Azure Kubernetes
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: fa425a5ecd8cf8f4c7b3516534b4c4f0f4257850
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085338"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitorování vašich modelů Azure Machine Learning v produkčním prostředí pomocí nástroje Application Insights

V tomto článku se dozvíte, jak nastavit Azure Application Insights pro vaši službu Azure Machine Learning. Application Insights nabízí možnost monitorovat:
* Požádat o sazby, doby odezvy a míra selhání.
* Míra závislosti, doby odezvy a míra selhání.
* Výjimky.

[Další informace o Application Insights](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Pracovnímu prostoru Azure Machine Learning, místní adresář, který obsahuje skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách, najdete v článku [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md).
* Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS). Pokud ho nemáte, přečtěte si článek [Train model klasifikace obrázků](tutorial-train-models-with-aml.md) kurzu.
* [Clusteru AKS](how-to-deploy-to-aks.md).

## <a name="enable-and-disable-in-the-portal"></a>Povolit nebo zakázat na portálu

Můžete povolit nebo zakázat Application Insights na webu Azure Portal.

### <a name="enable"></a>Povolení

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

   [![Tlačítko Upravit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. V **Upřesnit nastavení**, zrušte zaškrtnutí políčka **diagnostiku AppInsights povolit** zaškrtávací políčko. 

   [![Nezaškrtnuté zaškrtávací políčko pro povolení diagnostiky](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Vyberte **aktualizace** v dolní části obrazovky, aby se změny projevily. 

## <a name="enable-and-disable-from-the-sdk"></a>Povolení a zákaz ze sady SDK

### <a name="update-a-deployed-service"></a>Aktualizace nasazené službě
1. Identifikaci služby ve vašem pracovním prostoru. Hodnota pro `ws` je název vašeho pracovního prostoru.

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizovat službu a povolte Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Vlastní trasování protokolu ve službě
Pokud chcete vlastní trasování protokolů, postupujte [procesu standardní nasazení pro AKS](how-to-deploy-to-aks.md). Potom:

1. Aktualizujte soubor vyhodnocení přidáním tisku příkazy.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizujte konfiguraci AKS.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Sestavení image a nasaďte ji](how-to-deploy-to-aks.md).  

### <a name="disable-tracking-in-python"></a>Zakázání sledování v Pythonu

Pokud chcete zakázat Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Vyhodnocení dat
Vaše služba data se ukládají v účtu služby Application Insights ve stejné skupině prostředků jako službu Azure Machine Learning.
Chcete-li zobrazit ho:
1. Přejděte do vaší skupiny prostředků v [webu Azure portal](https://portal.azure.com) a přejděte do prostředku Application Insights. 
2. **Přehled** kartě se zobrazí základní sadu metriky pro vaši službu.

   [![Přehled](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Do vaší vlastní trasování, že vyberete **Analytics**.
4. V části schématu vyberte **trasy**. Potom vyberte **spustit** ke spuštění dotazu. Data by se měla objevit ve formátu tabulky a měli namapovat na vlastní volání v souboru bodování. 

   [![Vlastní trasy](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Další informace o tom, jak pomocí Application Insights najdete v tématu [co je Application Insights?](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Příklad poznámkového bloku

[00. Začínáme Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) Poznámkový blok ukazuje koncepty v tomto článku.  Získáte tento poznámkový blok:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
Může také shromažďovat data o vašich modelů v produkčním prostředí. Přečtěte si článek [shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md). 
