---
title: Shromažďování dat o vašich produkčních modelech
titleSuffix: Azure Machine Learning
description: Zjistěte, jak shromažďovat data vstupního modelu Azure Machine Learning v úložišti objektů Blob Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c481a2e12d83e865025cd90e59e0eba572ad9a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771389"
---
# <a name="collect-data-for-models-in-production"></a>Shromažďování dat pro modely ve výrobě

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Sada Azure Machine Learning Monitoring SDK bude brzy vyřazena. Sada SDK je stále vhodná pro vývojáře, kteří aktuálně používají sadu SDK ke sledování posunu dat v modelech. Ale pro nové zákazníky doporučujeme používat zjednodušené [monitorování dat s Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

Tento článek ukazuje, jak shromažďovat data vstupního modelu z Azure Machine Learning. Také ukazuje, jak nasadit vstupní data do clusteru služby Azure Kubernetes Service (AKS) a uložit výstupní data v úložišti objektů Blob Azure.

Jakmile je shromažďování povoleno, shromažďujte data, která shromažďujete, vám pomohou:

* [Sledujte posuny dat,](how-to-monitor-data-drift.md) když produkční data zadávají váš model.

* Lépe se rozhodujte, kdy model přeškolit nebo optimalizovat.

* Přeškolte model na shromážděná data.

## <a name="what-is-collected-and-where-it-goes"></a>Co se shromažďuje a kam se to děje

Mohou být shromažďovány následující údaje:

* Vstupní data modelu z webových služeb nasazených v clusteru AKS. Hlasový zvuk, obrázky a video *se neshromažďují.*
  
* Předpovědi modelu pomocí vstupních dat výroby.

>[!NOTE]
> Preagregace a předběžné výpočty na tato data nejsou aktuálně součástí služby kolekce.

Výstup se uloží do úložiště objektů Blob. Vzhledem k tomu, že data se přidávají do úložiště objektů Blob, můžete vybrat svůj oblíbený nástroj pro spuštění analýzy.

Cesta k výstupním datům v objektu blob následuje podle této syntaxe:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Ve verzích sady Azure Machine Learning SDK pro Python starší než verze `designation` 0.1.0a16 je argument pojmenován `identifier`. Pokud jste vyvinuli kód s dřívější verzí, je třeba jej odpovídajícím způsobem aktualizovat.

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://aka.ms/AMLFree) než začnete.

- Pracovní prostor AzureMachine Learning, místní adresář obsahující vaše skripty a azure machine learning sdk pro Python musí být nainstalovaný. Informace o jejich instalaci naleznete v [tématu Konfigurace vývojového prostředí](how-to-configure-environment.md).

- Potřebujete trénovaný model strojového učení, který chcete nasadit do AKS. Pokud nemáte model, podívejte se na kurz [model klasifikace bitových obrázků vlak.](tutorial-train-models-with-aml.md)

- Potřebujete cluster AKS. Informace o tom, jak vytvořit a nasadit do něj, naleznete v [tématu Jak nasadit a kde](how-to-deploy-and-where.md).

- [Nastavte si prostředí](how-to-configure-environment.md) a nainstalujte [sadu Azure Machine Learning Monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Povolení shromažďování dat

Shromažďování dat můžete povolit bez ohledu na model, který nasazujete prostřednictvím Azure Machine Learning nebo jiných nástrojů.

Chcete-li povolit shromažďování dat, je třeba:

1. Otevřete soubor bodování.

1. V horní části souboru přidejte [následující kód:](https://aka.ms/aml-monitoring-sdk)

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarujte proměnné shromažďování `init` dat ve své funkci:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* je volitelný parametr. Nemusíte ji používat, pokud ji váš model nevyžaduje. Použití *CorrelationId* vám pomůže snadněji mapovat s jinými daty, jako je *LoanNumber* nebo *CustomerId*.
    
    Parametr *Identifier* se později používá pro vytváření struktury složek v objektu blob. Můžete ji použít k odlišení nezpracovaných dat od zpracovaných dat.

1. Do `run(input_df)` funkce přidejte následující řádky kódu:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Shromažďování dat *není* automaticky nastavena na **hodnotu true** při nasazení služby v AKS. Aktualizujte konfigurační soubor, jako v následujícím příkladu:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Můžete také povolit Application Insights pro monitorování služeb změnou této konfigurace:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Pokud chcete vytvořit novou bitovou kopii a nasadit model strojového učení, přečtěte si informace [o tom, jak nasadit a kde](how-to-deploy-and-where.md).

Pokud již máte službu se závislostmi nainstalovanými v souboru prostředí a souboru vyhodnocování, povolte shromažďování dat následujícím postupem:

1. Přejděte na [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **možnost Nasazení Vyberte** > **službu** > **Upravit**.

   ![Úprava služby](././media/how-to-enable-data-collection/EditService.PNG)

1. V **části Upřesnit nastavení**vyberte Možnost **Povolit shromažďování dat modelu**.

    [![Vybrat sběr dat](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Můžete také vybrat **Povolit diagnostiku AppInsights** ke sledování stavu vaší služby.

1. Chcete-li změny použít, vyberte **aktualizovat.**

## <a name="disable-data-collection"></a>Zakázání shromažďování dat

Shromažďování dat můžete kdykoli ukončit. Pomocí kódu Pythonu nebo Azure Machine Learning zakázat shromažďování dat.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Možnost 1 – Zakázání shromažďování dat v Azure Machine Learning

1. Přihlaste se k [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **možnost Nasazení Vyberte** > **službu** > **Upravit**.

   [![Vybrat volbu Upravit](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. V **rozšířeném nastavení**zrušte **zaškrtnutí políčka Povolit sběr dat modelu**.

    [![Vymazání zaškrtávacího políčka Shromažďování dat](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Chcete-li změnu použít, vyberte **aktualizovat.**

K těmto nastavením můžete přistupovat také ve svém pracovním prostoru v [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Možnost 2 – Zakázání sběru dat pomocí Pythonu

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Ověření a analýza dat

Můžete si vybrat nástroj podle vašich preferencí k analýze dat shromážděných v úložišti objektů Blob.

### <a name="quickly-access-your-blob-data"></a>Rychlý přístup k datům objektu blob

1. Přihlaste se k [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **možnost Úložiště**.

    [![Výběr možnosti Úložiště](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Postupujte podle cesty k výstupním datům objektu blob s touto syntaxí:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analýza dat modelu pomocí Power BI

1. Stáhněte a otevřete [Power BI Desktop](https://www.powerbi.com).

1. Vyberte **Získat data** a vyberte [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Nastavení objektu blob Power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Přidejte název účtu úložiště a zadejte klíč úložiště. Tyto informace můžete najít **Settings** > výběrem**kláves Windows Access** v objektu blob.

1. Vyberte **kontejner dat modelu** a vyberte **Upravit**.

    [![Power BI Navigátor](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. V editoru dotazů klikněte pod sloupcem **Název** a přidejte účet úložiště.

1. Zadejte cestu modelu do filtru. Pokud chcete prohledávat pouze soubory z určitého roku nebo měsíce, stačí rozbalit cestu filtru. Chcete-li například zobrazit pouze březnová data, použijte tuto cestu filtru:

   /modeldata/\<subscriptionid\<>/resourcegroupname\<\<>/workspacename>/webservicename\<>/modelname>/modelversion\<>/\<designation>/year\<>/3

1. Filtrujte data, která jsou pro vás relevantní, na základě hodnot **Name.** Pokud jste uložili předpovědi a vstupy, je třeba vytvořit dotaz pro každý.

1. Chcete-li soubory zkombinovat, vyberte dvojité šipky dolů vedle záhlaví sloupce **Obsah.**

    [![Obsah Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Vyberte **OK**. Data se předem načtou.

    [![Kombinovat soubory Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Vyberte **Zavřít a Použít**.

1. Pokud jste přidali vstupy a předpovědi, tabulky jsou automaticky seřazeny podle hodnot **RequestId.**

1. Začněte vytvářet vlastní sestavy na datech modelu.

### <a name="analyze-model-data-using-azure-databricks"></a>Analýza dat modelu pomocí Azure Databricks

1. Vytvořte [pracovní prostor Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Přejděte do pracovního prostoru Databricks.

1. V pracovním prostoru Databricks vyberte **Nahrát data**.

    [![Výběr možnosti Databricks Upload Data](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Vyberte **Vytvořit novou tabulku** a vyberte jiné zdroje >  **dat****Azure Blob Storage** > **Create Table table in Notebook**.

    [![Vytvoření tabulky Databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualizujte umístění dat. Zde naleznete příklad:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Nastavení datových cihel](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Podle pokynů v šabloně zobrazte a analyzujte data.
