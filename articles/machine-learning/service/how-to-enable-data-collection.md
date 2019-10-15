---
title: Shromažďování dat v produkčních modelech
titleSuffix: Azure Machine Learning
description: Naučte se shromažďovat data vstupních modelů Azure Machine Learning v úložišti objektů BLOB v Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 109db23976f6332b24bcfa565812bd9491062691
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330737"
---
# <a name="collect-data-for-models-in-production"></a>Shromažďování dat pro modely v produkčním prostředí

>[!IMPORTANT]
> Tato sada SDK brzy vychází z provozu. Tato sada SDK je stále vhodná pro vývojáře, kteří sledují posun dat v modelech, ale většina vývojářů by měla používat zjednodušené [monitorování dat s Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

V tomto článku se dozvíte, jak shromažďovat data vstupního modelu z Azure Machine Learning jste nasadili do clusteru Azure Kubernetes (AKS) do úložiště objektů BLOB v Azure. 

Po povolení vám tato data, která shromáždíte, pomůže:
* [Monitorování posunu dat](how-to-monitor-data-drift.md) jako provozních dat do modelu

* Zajištění lepších rozhodnutí o tom, kdy se má model znovu naučit nebo optimalizovat

* Přeučení modelu s shromažďovanými daty

## <a name="what-is-collected-and-where-does-it-go"></a>Co je shromažďováno a kde to funguje?

Shromažďovat lze následující data:
* Model **vstupních** dat z webových služeb nasazených v clusteru Azure KUBERNETES (AKS) (hlas, obrázky a **video se neshromažďují)** 
  
* Předpovědi modelu s použitím vstupních produkčních dat.

> [!Note]
> Předběžná agregace nebo předběžná kalkulace těchto dat v tuto chvíli není součástí služby.   

Výstup se uloží do objektu blob Azure. Vzhledem k tomu, že se data přidají do objektu blob Azure, můžete pro spuštění analýzy zvolit oblíbený nástroj. 

Cesta k výstupním datům v objektu BLOB se řídí touto syntaxí:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

- Azure Machine Learning pracovní prostor, místní adresář obsahující vaše skripty a nainstalovanou sadu Azure Machine Learning SDK pro Python. Naučte se tyto požadavky získat pomocí [postupu konfigurace dokumentu vývojového prostředí](how-to-configure-environment.md) .

- Školený model strojového učení, který se má nasadit do služby Azure Kubernetes (AKS). Pokud ho nemáte, přečtěte si kurz pro [model klasifikace imagí v výukovém](tutorial-train-models-with-aml.md) programu.

- Cluster služby Azure Kubernetes. Informace o tom, jak vytvořit a nasadit do jednoho, najdete v tématu [nasazení a umístění](how-to-deploy-and-where.md) dokumentu.

- [Nastavte prostředí](how-to-configure-environment.md) a nainstalujte [sadu monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Povolení shromažďování dat
Shromažďování dat lze povolit bez ohledu na model, který je nasazen prostřednictvím Azure Machine Learning nebo jiných nástrojů. 

Pokud ho chcete povolit, musíte:

1. Otevřete soubor bodování. 

1. Do horní části souboru přidejte [následující kód](https://aka.ms/aml-monitoring-sdk) :

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarace proměnných shromažďování dat ve funkci `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *ID korelace* je nepovinný parametr, nemusíte ho nastavovat, pokud ho model nevyžaduje. Máte-li na místě ID korelace, může vám to usnadnit mapování s ostatními daty. (Příklady zahrnují: LoanNumber, CustomerId atd.)
    
    *Identifikátor* se později používá k vytvoření struktury složek v objektu blob, dá se použít k rozdělení nezpracovaných dat vs.

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
    AppInsights pro monitorování služby je také možné zapnout změnou této konfigurace:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. Postup vytvoření nové image a nasazení služby najdete v tématu [nasazení a umístění](how-to-deploy-and-where.md) dokumentu.


Pokud již máte v souboru **prostředí** a v **souboru bodování**nainstalovanou službu se závislostmi, povolte shromažďování dat pomocí:

1. Přejít na [Azure Portal](https://portal.azure.com).

1. Otevřete pracovní prostor.

1. Přejít na **nasazení** -> **Vyberte službu** -> **Upravit**.

   ![Upravit službu](media/how-to-enable-data-collection/EditService.PNG)

1. V **rozšířeném nastavení**zrušte výběr možnosti **Povolit shromažďování dat modelu**. 

    [@no__t – shromažďování dat pro 1check](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   V tomto okně můžete také vybrat možnost Povolit diagnostiku Appinsights a sledovat stav služby.  

1. Pokud chcete změnu použít, vyberte **aktualizovat** .


## <a name="disable-data-collection"></a>Zakázat shromažďování dat
Shromažďování dat můžete kdykoli zastavit. Pro zakázání shromažďování dat použijte kód Pythonu nebo Azure Portal.

+ Možnost 1 – zakázat v Azure Portal: 
  1. Přihlaste se k [portálu Azure](https://portal.azure.com).

  1. Otevřete pracovní prostor.

  1. Přejít na **nasazení** -> **Vyberte službu** -> **Upravit**.

     [@no__t – možnost 1Edit](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. V **rozšířeném nastavení**zrušte výběr možnosti **Povolit shromažďování dat modelu**. 

     [@no__t – shromažďování dat pro 1Uncheck](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Pokud chcete změnu použít, vyberte **aktualizovat** .

  K těmto nastavením můžete přistupovat také na [cílové stránce pracovního prostoru (Preview)](https://ml.azure.com).

+ Možnost 2 – použití Pythonu k zakázání shromažďování dat:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Ověření dat a jejich analýza
Můžete vybrat libovolný nástroj z vaší předvolby a analyzovat data shromážděná do objektu blob Azure. 

Rychlý přístup k datům z objektu BLOB:
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Otevřete pracovní prostor.
1. Klikněte na **úložiště**.

    [@no__t – 1Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Použijte cestu k výstupním datům v objektu BLOB s touto syntaxí:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analýza dat modelu prostřednictvím Power BI

1. Stáhnout a otevřít [Power BI Desktop](https://www.powerbi.com)

1. Vyberte **získat data** a klikněte na [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [nastavení objektu BLOB @no__t 1PBI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Přidejte název svého účtu úložiště a zadejte svůj klíč úložiště. Tyto informace najdete v **Nastavení** objektu blob > > přístupových klíčích. 

1. Vyberte kontejner **modeldata** a klikněte na **Upravit**. 

    [@no__t – 1PBI Navigator](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. V editoru dotazů klikněte pod sloupcem název a přidejte svůj účet úložiště 1. Cesta k modelu do filtru. Poznámka: Pokud chcete hledat pouze soubory z konkrétního roku nebo měsíce, stačí rozšířit cestu filtru. Například stačí najít data v březnu:/modeldata/SubscriptionId >/ResourceGroupName >/workspacename >/WebServiceName >/ModelName >/modelversion >/Identifier > za rok >/3

1. Vyfiltrujte data, která jsou pro vás důležitá na základě **názvu**. Pokud jste uložili **předpovědi** a **vstupy**, budete si muset vytvořit dotaz pro každý z nich.

1. Kliknutím na šipku vedle sloupce **obsah** spojíte soubory. 

    [@no__t – obsah 1PBI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klikněte na OK a data se načtou.

    [@no__t – 1pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Nyní můžete kliknout na **Zavřít a použít** .

1.  Pokud jste přidali vstupy a předpovědi, budou se tabulky automaticky korelovat podle **RequestId**.

1. Začněte vytvářet vlastní sestavy pro data modelu.


### <a name="analyzing-model-data-using-databricks"></a>Analýza dat modelu pomocí datacihlů

1. Vytvořte [pracovní prostor datacihly](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Přejdete do pracovního prostoru datacihly. 

1. V pracovním prostoru datacihly vyberte **Odeslat data**.

    [@no__t – nahrávání 1DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Vytvořte novou tabulku a vyberte **jiné zdroje dat** – > Azure Blob Storage-> vytvořit tabulku v poznámkovém bloku.

    [@no__t – tabulka 1DB](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualizujte umístění vašich dat. Zde naleznete příklad:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [@no__t – 1DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Pokud chcete zobrazit a analyzovat data, postupujte podle kroků uvedených v šabloně. 

## <a name="example-notebook"></a>Příklad poznámkového bloku

Poznámkový blok [How-to-use-AzureML/Deployment/Enable-data-Collection-for-Models-in-AKS/Enable-data-Collection-for-Models-in-AKS. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) ukazuje koncepty v tomto článku.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
