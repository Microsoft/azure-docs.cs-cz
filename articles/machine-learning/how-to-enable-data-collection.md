---
title: Shromažďování dat v produkčních modelech
titleSuffix: Azure Machine Learning
description: Naučte se shromažďovat data vstupních modelů Azure Machine Learning v úložišti objektů BLOB v Azure.
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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771389"
---
# <a name="collect-data-for-models-in-production"></a>Shromažďování dat modelů v produkčním prostředí

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Sada Azure Machine Learning monitoring SDK bude brzy vyřazena. Sada SDK je stále vhodná pro vývojáře, kteří aktuálně používají sadu SDK ke sledování posunu dat v modelech. Pro nové zákazníky ale doporučujeme používat zjednodušené [monitorování dat s Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

V tomto článku se dozvíte, jak shromažďovat data vstupního modelu z Azure Machine Learning. Také ukazuje, jak nasadit vstupní data do clusteru Azure Kubernetes Service (AKS) a uložit výstupní data v úložišti objektů BLOB v Azure.

Když je kolekce povolená, data, která shromáždíte, vám pomůžou:

* [Sledujte](how-to-monitor-data-drift.md) , jak se data v modelu dodávají do provozu.

* Udělejte lepší rozhodnutí o tom, kdy se má model předávat nebo optimalizovat.

* Přeškolujte svůj model s shromážděnými daty.

## <a name="what-is-collected-and-where-it-goes"></a>Co se shromáždí a kde se bude nachází

Můžete shromažďovat následující data:

* Modeluje vstupní data z webových služeb nasazených v clusteru AKS. Hlasový zvuk, obrázky a *video se neshromažďují.*
  
* Model predikce na základě vstupních dat v produkčním prostředí.

>[!NOTE]
> Předagregační a předpočty těchto dat nejsou aktuálně součástí služby shromažďování.

Výstup je uložený v úložišti objektů BLOB. Vzhledem k tomu, že se data přidávají do úložiště objektů blob, můžete pro spuštění analýzy zvolit oblíbený nástroj.

Cesta k výstupní data v objektu blob odpovídá této syntaxi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Ve verzích Azure Machine Learning SDK pro Python starší než verze 0.1.0 A16 má argument `designation` název `identifier`. Pokud jste kód vyvinuli v dřívější verzi, budete ho muset aktualizovat odpovídajícím způsobem.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) před tím, než začnete.

- Je potřeba mít nainstalovaný pracovní prostor AzureMachine Learning, místní adresář obsahující vaše skripty a sadu SDK Azure Machine Learning SDK pro Python. Informace o tom, jak je nainstalovat, najdete v tématu [Jak konfigurovat vývojové prostředí](how-to-configure-environment.md).

- Budete potřebovat školený model strojového učení, který se má nasadit do AKS. Pokud model nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

- Potřebujete cluster AKS. Informace o tom, jak vytvořit a nasadit do něj, najdete v tématu [Jak nasadit a kde](how-to-deploy-and-where.md).

- [Nastavte prostředí](how-to-configure-environment.md) a nainstalujte [sadu Azure Machine Learning monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Povolení shromažďování dat

Shromažďování dat můžete povolit bez ohledu na model, který nasazujete prostřednictvím Azure Machine Learning nebo jiných nástrojů.

Chcete-li povolit shromažďování dat, je třeba:

1. Otevřete soubor vyhodnocení.

1. Přidat [následující kód](https://aka.ms/aml-monitoring-sdk) v horní části souboru:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Deklarujte proměnné kolekce dat ve vaší `init` funkce:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *ID korelace* je nepovinný parametr. Nemusíte ho používat, pokud ho váš model nevyžaduje. Použití *ID korelace* vám usnadní snazší mapování s ostatními daty, například *LoanNumber* nebo *KódZákazníka*.
    
    Parametr *Identifier* se později používá pro sestavování struktury složek v objektu BLOB. Můžete ji použít k odlišení nezpracovaných dat ze zpracovaných dat.

1. Přidejte následující řádky kódu, který `run(input_df)` funkce:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. Při nasazení služby v AKS *není* shromažďování dat automaticky nastaveno na **hodnotu true** . Aktualizujte konfigurační soubor, jak je uvedeno v následujícím příkladu:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    Změnou této konfigurace můžete také povolit Application Insights pro monitorování služby:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Pokud chcete vytvořit novou image a nasadit model Machine Learning, přečtěte si [článek Jak nasadit a kde](how-to-deploy-and-where.md).

Pokud již máte v souboru prostředí a v souboru bodování nainstalovanou službu se závislostmi, povolte shromažďování dat pomocí následujících kroků:

1. Přejít na [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **nasazení** > **Vyberte služba** > **Upravit**.

   ![Úprava služby](././media/how-to-enable-data-collection/EditService.PNG)

1. V **upřesňujících nastaveních**vyberte **Povolit shromažďování dat modelu**.

    [![výběr shromažďování dat](./media/how-to-enable-data-collection/CheckDataCollection.png)](././media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   Můžete také vybrat **Povolit diagnostiku AppInsights** , abyste mohli sledovat stav služby.

1. Chcete-li změny použít, vyberte **aktualizovat** .

## <a name="disable-data-collection"></a>Zakázat shromažďování dat

Shromažďování dat můžete kdykoli ukončit. Pro zakázání shromažďování dat použijte kód Pythonu nebo Azure Machine Learning.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Možnost 1 – zakázat shromažďování dat v Azure Machine Learning

1. Přihlaste se k [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **nasazení** > **Vyberte služba** > **Upravit**.

   [![vyberte možnost Upravit.](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. V **rozšířeném nastavení**zrušte zaškrtnutí políčka **Povolit shromažďování dat modelu**.

    [![zrušení zaškrtnutí políčka pro shromažďování dat](./media/how-to-enable-data-collection/UncheckDataCollection.png)](././media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Vyberte **aktualizace** na použití změny.

K těmto nastavením můžete také přistupovat v pracovním prostoru v [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Možnost 2 – použití Pythonu k zakázání shromažďování dat

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Ověřování a analýza dat

Pro analýzu dat shromažďovaných v úložišti objektů blob můžete zvolit nástroj z vaší předvolby.

### <a name="quickly-access-your-blob-data"></a>Rychlý přístup k datům objektů BLOB

1. Přihlaste se k [Azure Machine Learning](https://ml.azure.com).

1. Otevřete pracovní prostor.

1. Vyberte **Úložiště**.

    [![výběru možnosti úložiště](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Použijte cestu k výstupním datům objektu BLOB s touto syntaxí:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analýza dat modelu pomocí Power BI

1. Stáhněte a otevřete [Power BI Desktop](https://www.powerbi.com).

1. Vyberte **získat data** a vyberte [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [nastavení objektu BLOB ![Power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Přidejte název svého účtu úložiště a zadejte klíče účtu úložiště. Tyto informace můžete najít tak, že v objektu BLOB vyberete **nastavení** > **přístupové klíče** .

1. Vyberte kontejner **dat modelu** a vyberte **Upravit**.

    [Navigátor ![Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. V editoru dotazů klikněte do sloupce **název** a přidejte svůj účet úložiště.

1. Do filtru zadejte cestu k modelu. Pokud chcete hledat pouze soubory z konkrétního roku nebo měsíce, stačí rozšířit cestu filtru. Chcete-li například hledat pouze data v březnu, použijte tuto cestu k filtru:

   /modeldata/\<SubscriptionId >/\<ResourceGroupName >/\<pracovního prostoru >/\<WebService >/\<model >/\<modelversion >/\<označení >/\<rok >/3

1. Vyfiltrujte data, která jsou pro vás důležitá, podle hodnot **názvu** . Pokud jste uložili předpovědi a vstupy, musíte pro každý z nich vytvořit dotaz.

1. Pro kombinování souborů vyberte dvojitou šipku dolů vedle záhlaví sloupce **obsahu** .

    [Obsah Power BI ![](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Vyberte **OK**. Předčítat data.

    [![Power BI kombinovat soubory](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Vyberte **Zavřít a použít**.

1. Pokud jste přidali vstupy a předpovědi, tabulky se automaticky seřadí podle hodnot **RequestId** .

1. Začněte vytvářet vlastní sestavy na datech modelu.

### <a name="analyze-model-data-using-azure-databricks"></a>Analýza dat modelu pomocí Azure Databricks

1. Vytvořte [pracovní prostor Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Přejděte do pracovního prostoru Databricks.

1. V pracovním prostoru datacihly vyberte **Odeslat data**.

    [![výběru možnosti nahrát data datacihly](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Vyberte **vytvořit novou tabulku** a vyberte **jiné zdroje dat** > **Azure Blob Storage** > **vytvořit tabulku v poznámkovém bloku**.

    [Vytvoření tabulky ![datacihly](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualizujte umístění vašich dat. Zde naleznete příklad:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [nastavení ![datacihly](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Pokud chcete zobrazit a analyzovat data, postupujte podle kroků v této šabloně.
