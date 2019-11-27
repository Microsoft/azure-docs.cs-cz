---
title: Shromažďování dat v produkčních modelech
titleSuffix: Azure Machine Learning
description: Zjistěte, jak shromažďovat data o vstupním modelu Azure Machine Learning v Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406456"
---
# <a name="collect-data-for-models-in-production"></a>Shromažďování dat modelů v produkčním prostředí
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Tato sada SDK brzy vychází z provozu. Tato sada SDK je stále vhodná pro vývojáře, kteří sledují posun dat v modelech, ale většina vývojářů by měla používat zjednodušené [monitorování dat s Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

V tomto článku se dozvíte, jak shromažďovat data vstupního modelu z Azure Machine Learning jste nasadili do clusteru Azure Kubernetes (AKS) do úložiště objektů BLOB v Azure. 

Po povolení tohoto data o vám pomůže:
* [Monitorování posunu dat](how-to-monitor-data-drift.md) jako provozních dat do modelu

* Lepší rozhodování na tom, kdy přeučování nebo optimalizace modelu

* Přeučování s daty shromážděnými modelu

## <a name="what-is-collected-and-where-does-it-go"></a>Co je shromažďováno a kde položka přejít?

Můžete shromažďovat následující data:
* Model **vstupních** dat z webových služeb nasazených v clusteru Azure KUBERNETES (AKS) (hlas, obrázky a **video se neshromažďují)** 
  
* Předpovědi modelu s použitím vstupních produkčních dat

> [!Note]
> Předběžnou agregací nebo předběžné výpočty na tato data nejsou součástí služby v tuto chvíli.   

Získá výstup uložen v objektu Blob Azure. Protože data přidá do objektu Blob Azure, můžete následně vašeho oblíbeného nástroje pro spuštění analýzy. 

Cesta k výstupní data v objektu blob odpovídá této syntaxi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> Ve verzích sady SDK před `0.1.0a16` `designation` argument byl pojmenován `identifier`. Pokud byl váš kód vyvinutý pomocí starší verze, budete ho muset aktualizovat odpovídajícím způsobem.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

- Azure Machine Learning pracovní prostor, místní adresář obsahující vaše skripty a nainstalovanou sadu Azure Machine Learning SDK pro Python. Informace o tom, jak tyto požadavky získat pomocí [postupu konfigurace dokumentu vývojového prostředí](how-to-configure-environment.md)

- Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS). Pokud ho nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

- Cluster Azure Kubernetes Service. Informace o tom, jak vytvořit a nasadit na jeden, najdete v tématu [nasazení a umístění](how-to-deploy-and-where.md) dokumentu.

- [Nastavení prostředí](how-to-configure-environment.md) a instalace [sady monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Povolení shromažďování dat
Shromažďování dat lze povolit bez ohledu na model, který je nasazen prostřednictvím Azure Machine Learning nebo jiných nástrojů. 

Ho Pokud chcete povolit, je potřeba:

1. Otevřít soubor bodování

1. Do horní části souboru přidejte [následující kód](https://aka.ms/aml-monitoring-sdk) :

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarace proměnných shromažďování dat ve funkci `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *ID korelace* je nepovinný parametr, nemusíte ho nastavovat, pokud ho model nevyžaduje. S ID korelace v místě vám pomůže snadněji mapování s jinými daty. (Příklady: LoanNumber, ID zákazníka, atd.)
    
    *Identifikátor* se později používá k vytvoření struktury složek v objektu blob, dá se použít k rozdělení nezpracovaných dat versus zpracovaných.

3.  Do funkce `run(input_df)` přidejte následující řádky kódu:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Při nasazení služby v AKS **není** shromažďování dat automaticky nastaveno na **hodnotu true** , takže musíte aktualizovat konfigurační soubor, například: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights pro službu monitorování lze také zapnout tak, že změníte tuto konfiguraci:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Postup vytvoření nové image a nasazení služby najdete v tématu [nasazení a umístění](how-to-deploy-and-where.md) dokumentu.


Pokud již máte v souboru **prostředí** a v **souboru bodování**nainstalovanou službu se závislostmi, povolte shromažďování dat pomocí:

1. Přejít na [Azure Machine Learning Studio](https://ml.azure.com)

1. Otevřete pracovní prostor.

1. Přejít na **nasazení** -> **Vybrat službu** -> **Upravit**

   ![Upravit službu](media/how-to-enable-data-collection/EditService.PNG)

1. V **upřesňujících nastaveních**vyberte **Povolit shromažďování dat modelu**

    [![shromažďování dat kontroly](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   V tomto okně můžete také vybrat možnost Povolit diagnostiku Appinsights a sledovat stav služby.

1. Vyberte **aktualizovat** , aby se změna projevila.


## <a name="disable-data-collection"></a>Zakázat shromažďování dat
Můžete zastavit shromažďování dat kdykoli. K zakázání shromažďování dat použijte kód Pythonu nebo Azure Machine Learning Studio.

+ Možnost 1 – zakázat v Azure Machine Learning Studiu: 
  1. Přihlášení k [Azure Machine Learning Studiu](https://ml.azure.com)

  1. Otevřete pracovní prostor.

  1. Přejít na **nasazení** -> **Vybrat službu** -> **Upravit**

     [![– možnost úprav](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. V **rozšířeném nastavení**zrušte výběr možnosti **Povolit shromažďování dat modelu** .

     [![zrušit kontrolu shromažďování dat](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Vyberte **aktualizovat** , aby se změna projevila.

  K těmto nastavením můžete získat přístup také v [Azure Machine Learning Studiu](https://ml.azure.com)v pracovním prostoru.

+ Možnost 2 – použití Pythonu k zakázání shromažďování dat:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Ověření dat a analyzovat
Můžete použít libovolný nástroj dle dáváte přednost, pokud chcete analyzovat data shromáždí do objektů Blob v Azure.

Rychle se dostat k dat z objektu blob služby:

1. Přihlášení k [Azure Machine Learning Studiu](https://ml.azure.com)

1. Otevřete pracovní prostor.
1. Klikněte na **úložiště** .

    [![úložiště](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Postupujte podle cesty k výstupní data v objektu blob s následující syntaxí:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analýza dat modelu pomocí Power BI

1. Stáhnout a otevřít [Power BI Desktop](https://www.powerbi.com)

1. Vyberte **získat data** a klikněte na [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [nastavení ![PBI BLOB](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Přidejte název svého účtu úložiště a zadejte klíče účtu úložiště. Tyto informace najdete v **Nastavení** objektu blob > > přístupových klíčích.

1. Vyberte kontejner **modeldata** a klikněte na **Upravit** .

    [![PBI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. V editoru dotazů klikněte ve sloupci "Name" a přidejte svůj účet úložiště 1. Cesta modelu do filtru. Poznámka: Pokud chcete se díval jenom na soubory z konkrétního roku nebo měsíce, stačí rozbalte cestu filtru. Například stačí najít data v březnu:/modeldata/SubscriptionId >/ResourceGroupName >/workspacename >/WebServiceName >/ModelName >/modelversion >/Designation > za rok >/3

1. Vyfiltrujte data, která jsou pro vás důležitá na základě **názvu**. Pokud jste uložili **předpovědi** a **vstupy**, budete muset pro každý z nich vytvořit dotaz.

1. Kliknutím na šipku vedle sloupce **obsah** spojíte soubory.

    [![PBI obsahu](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klikněte na OK a data se načtou.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Nyní můžete kliknout na **Zavřít a použít** .

1.  Pokud jste přidali vstupy a předpovědi, budou se tabulky automaticky korelovat podle **RequestId** .

1. Začněte vytvářet vlastní sestavy pro data modelu


### <a name="analyzing-model-data-using-databricks"></a>Analýza dat modelu pomocí Databricks

1. Vytvoření [pracovního prostoru datacihly](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Přejít k pracovnímu prostoru datacihly

1. V pracovním prostoru datacihly vyberte **Odeslat data** .

    [nahrání ![DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Vytvořit novou tabulku a vybrat **jiné zdroje dat** – > Azure Blob Storage-> vytvořit tabulku v poznámkovém bloku

    [Tabulka ![DB](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualizace umístění vaše data. Zde naleznete příklad:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![nástroj DBSetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Pokud chcete zobrazit a analyzovat data, postupujte podle kroků uvedených v šabloně.

## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [How-to-use-AzureML/Deployment/Enable-data-Collection-for-Models-in-AKS/Enable-data-Collection-for-Models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) ukazuje koncepty v tomto článku.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
