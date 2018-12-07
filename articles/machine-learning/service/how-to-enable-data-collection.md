---
title: Povolit shromažďování dat pro modely v produkčním prostředí – Azure Machine Learning
description: Zjistěte, jak shromažďovat data o vstupním modelu Azure Machine Learning v Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 11/08/2018
ms.custom: seodec18
ms.openlocfilehash: 3033b049b3d4bbe49b6a30be7ec78e1c2caf49ef
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011130"
---
# <a name="collect-data-for-models-in-production"></a>Shromažďování dat modelů v produkčním prostředí

V tomto článku se dozvíte, jak ke shromažďování dat vstupním modelu ze služby Azure Machine Learning, které jste nasadili do služby Azure Kubernetes Cluster (AKS) do služby Azure Blob storage. 

Po povolení tohoto data o vám pomůže:
* Sledování dat drifts produkčních dat v modelu

* Lepší rozhodování na tom, kdy přeučování nebo optimalizace modelu

* Přeučování s daty shromážděnými modelu

## <a name="what-is-collected-and-where-does-it-go"></a>Co je shromažďováno a kde položka přejít?

Můžete shromažďovat následující data:
* Model **vstupní** data z webové služby nasazené ve službě Azure Kubernetes Cluster (AKS) (hlasové, obrázky a video se **není** shromažďují) 
  
* Model predikce na základě vstupních dat v produkčním prostředí.

> [!Note]
> Předběžnou agregací nebo předběžné výpočty na tato data nejsou součástí služby v tuto chvíli.   

Získá výstup uložen v objektu Blob Azure. Protože data přidá do objektu Blob Azure, můžete následně vašeho oblíbeného nástroje pro spuštění analýzy. 

Cesta k výstupní data v objektu blob odpovídá této syntaxi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://aka.ms/AMLfree).

- Azure service pracovního prostoru Machine Learning, místní adresář obsahující skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

- Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS). Pokud ho nemáte, přečtěte si článek [trénování modelů klasifikace image](tutorial-train-models-with-aml.md) kurzu.

- [Clusteru AKS](how-to-deploy-to-aks.md).

- [Nastavení prostředí](how-to-configure-environment.md) a nainstalujte [monitorování SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Povolení shromažďování dat
Shromažďování dat může být povoleno bez ohledu na model nasazení prostřednictvím služby Azure Machine Learning nebo jiných nástrojů. 

Ho Pokud chcete povolit, je potřeba:

1. Otevřete soubor vyhodnocení. 

1. Přidat [následující kód](https://aka.ms/aml-monitoring-sdk) v horní části souboru:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Deklarujte proměnné kolekce dat ve vaší `init()` funkce:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *ID korelace* je volitelný parametr, není potřeba ho nastavit, pokud váš model nevyžaduje. S ID korelace v místě vám pomůže snadněji mapování s jinými daty. (Příklady: LoanNumber, ID zákazníka, atd.)
    
    *Identifikátor* je později použit k sestavení strukturu složek v objektu Blob služby, můžete se používá k rozdělení data "neupravené" a "zpracované".

3.  Přidejte následující řádky kódu, který `run(input_df)` funkce:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. Shromažďování dat je **není** automaticky nastavený na **true** při nasazování služby ve službě AKS, proto je nutné aktualizovat konfigurační soubor jako: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    AppInsights pro službu monitorování lze také zapnout tak, že změníte tuto konfiguraci:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Vytvořit novou bitovou kopii a nasazení vaší služby.](how-to-deploy-to-aks.md) 


Pokud už máte služby se závislostmi nainstalovány ve vaší **souborem prostředí** a **soubor vyhodnocení**, Povolit shromažďování dat pomocí:

1. Přejděte na [webu Azure Portal](https://portal.azure.com).

1. Otevřete pracovní prostor.

1. Přejděte na **nasazení** -> **vyberte službu** -> **upravit**.

   ![Upravit službu](media/how-to-enable-data-collection/EditService.PNG)

1. V **Upřesnit nastavení**, zrušte zaškrtnutí možnosti **shromažďování dat modelu povolit**. 

    [![Zkontrolujte shromažďování dat](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   V tomto okně můžete také "Povolit diagnostiku Appinsights" ke sledování stavu služby.  

1. Vyberte **aktualizace** na použití změny.


## <a name="disable-data-collection"></a>Zakázat shromažďování dat
Můžete zastavit shromažďování dat kdykoli. Shromažďování dat vypnout pomocí kódu v Pythonu nebo na webu Azure portal.

+ Možnost 1 - zakázat na webu Azure Portal: 
  1. Přihlaste se k [portálu Azure](https://portal.azure.com).

  1. Otevřete pracovní prostor.

  1. Přejděte na **nasazení** -> **vyberte službu** -> **upravit**.

    [![Upravit možnosti](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. V **Upřesnit nastavení**, zrušte zaškrtnutí možnosti **shromažďování dat modelu povolit**. 

    [![Zrušte zaškrtnutí políčka shromažďování dat](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Vyberte **aktualizace** na použití změny.

* Možnost 2 – použití Pythonu k zakázání shromažďování dat:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Ověření dat a analyzovat
Můžete použít libovolný nástroj dle dáváte přednost, pokud chcete analyzovat data shromáždí do objektů Blob v Azure. 

Rychle se dostat k dat z objektu blob služby:
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Otevřete pracovní prostor.
1. Klikněte na **úložiště**.

    [![Úložiště](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Postupujte podle cesty k výstupní data v objektu blob s následující syntaxí:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analýza dat modelu pomocí Power BI

1. Stáhnout a otevřít [Power BI Desktop](https://www.powerbi.com)

1. Vyberte **získat Data** a klikněte na [ **Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Instalační program PBI objektů Blob](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Přidejte název svého účtu úložiště a zadejte klíče účtu úložiště. Tyto informace můžete najít v váš objekt blob **nastavení** >> přístupové klíče. 

1. Vyberte kontejner **modeldata** a klikněte na **upravit**. 

    [![PBI Navigátor](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. V editoru dotazů klikněte ve sloupci "Name" a přidejte svůj účet úložiště 1. Cesta modelu do filtru. Poznámka: Pokud chcete se díval jenom na soubory z konkrétního roku nebo měsíce, stačí rozbalte cestu filtru. Například právě podívejte se do března dat: ID předplatného/modeldata/> / resourcegroupname > / workspacename > / webservicename > / %{ModelName/ > / modelversion > / identifikátor > / rok > / 3

1. Filtrovat data, která je relevantní pro vás na základě **název**. Pokud jste si uložili **předpovědi** a **vstupy** budete muset vytvořit dotaz, který za každé.

1. Klikněte na dvojitou šipku jste si poznamenali **obsahu** sloupec, abychom zkombinovali soubory. 

    [![PBI obsahu](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Klikněte na tlačítko OK a data budou předběžné načtení.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Nyní můžete kliknout na **zavřít a použít** .

1.  Pokud jste přidali vstupy a vaše tabulky se automaticky předpovědi korelovat podle **RequestId**.

1. Začněte vytvářet vlastní sestavy na datech modelu.


### <a name="analyzing-model-data-using-databricks"></a>Analýza dat modelu pomocí Databricks

1. Vytvoření [pracovního prostoru Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Přejděte do pracovního prostoru Databricks. 

1. Na vaše databricks vyberte pracovní prostor **nahrát Data**.

    [![Nahrávání DB](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Vytvořit novou tabulku a vyberte **jiných zdrojů dat** -> úložiště objektů Blob v Azure -> Create Table do poznámkového bloku.

    [![Databázové tabulce](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aktualizace umístění vaše data. Zde naleznete příklad:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Nástroj DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Postupujte podle kroků v šabloně, aby bylo možné zobrazit a analyzovat data. 

## <a name="example-notebook"></a>Příklad poznámkového bloku

[How-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Poznámkový blok ukazuje koncepty v tomto článku.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
