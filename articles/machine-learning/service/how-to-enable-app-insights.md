---
title: Povolit application insights pro služby Azure Machine Learning v produkčním prostředí
description: Informace o nastavení Application Insights pro služby Azure Machine Learning, která byly nasazené ve službě Azure Kubernetes
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114562"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitorování vašich modelů Azure Machine Learning v produkčním prostředí pomocí nástroje Application Insights

V tomto článku se dozvíte o nastavení **Application Insights** pro vaše **Azure Machine Learning** služby. Po povolení Application Insights vám dává příležitost k monitorování:
* Požádat o sazby, doby odezvy a míra selhání
* Míra závislosti, doby odezvy a míra selhání
* Výjimky

Další informace o Application Insights [tady](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Požadavky
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Pracovnímu prostoru Azure Machine Learning, místní adresář obsahující skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.
* Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS). Pokud ho nemáte, přečtěte si článek [trénování modelů klasifikace image](tutorial-train-models-with-aml.md) kurzu.
* [Clusteru AKS](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Povolit a zakázat na portálu

Můžete povolit nebo zakázat Application Insights na webu Azure portal.

### <a name="enable"></a>Povolení

1. V [webu Azure portal](https://portal.azure.com), otevřete pracovní prostor.

1. Přejděte do vašeho nasazení a vyberte službu, ve které chcete povolit Application Insights.

   [![Nasazení](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Klikněte na tlačítko **upravit** a přejděte na **Upřesnit nastavení**.

   [![Upravit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. V **rozšířená nastavení** vyberte **diagnostiky povolte Application Insights**.

   [![Upravit](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Vyberte **aktualizace** v dolní části obrazovky, aby se změny projevily. 

### <a name="disable"></a>Zakázat
Pokud chcete zakázat Application Insights na webu Azure portal, proveďte následující kroky:

1. Přihlaste se k webu Azure portal v https://portal.azure.com.
1. Přejděte do svého pracovního prostoru.
1. Zvolte **nasazení**, pak **vyberte službu**a potom **upravit**.

   [![Upravit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. V **Upřesnit nastavení**, zrušte výběr možnosti **diagnostiku AppInsights povolit**. 

   [![Zrušte zaškrtnutí políčka](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Vyberte **aktualizace** v dolní části obrazovky, aby se změny projevily. 

## <a name="enable--disable-from-the-sdk"></a>Povolit a zakázat ze sady SDK

### <a name="update-a-deployed-service"></a>Aktualizace nasazené službě
1. Identifikaci služby ve vašem pracovním prostoru (ws = název pracovního prostoru)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizovat službu a povolte Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Vlastní trasování protokolu ve službě
Pokud chcete vlastní trasování protokolů, postupujte podle [procesu standardní nasazení pro AKS](how-to-deploy-to-aks.md) a získáte:

1. Aktualizujte soubor vyhodnocení přidáním tisku příkazy.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizujte konfiguraci aks.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Sestavení image a nasaďte ji.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Zakázání sledování v Pythonu

Pokud chcete zakázat Application Insights, použijte následující kód:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Vyhodnocení dat
Data služby se uloží ve vašem účtu služby Application Insights ve stejné skupině prostředků, které váš pracovní prostor služby Azure Machine Learning je v.
Chcete-li zobrazit ho:
1. Přejděte do vaší skupiny prostředků v [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko do prostředku Application Insights. 
2. **Přehled** kartě se zobrazí základní sadu metriky pro vaši službu.

   [![Přehled](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Aby viděl klikněte na tlačítko Vlastní trasování **Analytics**.
4. V rámci schématu oddílu, klikněte na **trasy** a potom **spustit** dotazu. Data by se měla zobrazit ve formátu tabulky dolů níže a měli namapovat na vlastní volání v souboru bodování. 

   [![Vlastní trasy](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Klikněte na tlačítko [tady](../../application-insights/app-insights-overview.md) získat další informace o tom, jak používat Application Insights.
    

## <a name="example-notebook"></a>Příklad poznámkového bloku

[00. Začínáme Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) Poznámkový blok ukazuje koncepty v tomto článku.  Získáte tento poznámkový blok:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Další postup
Může také shromažďovat data o vašich modelů v produkčním prostředí. Přečtěte si článek [shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md) 
