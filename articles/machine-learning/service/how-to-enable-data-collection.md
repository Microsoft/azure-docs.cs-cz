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
ms.date: 09/24/2018
ms.openlocfilehash: 70c023fc8fe996060d3eff3d5a700b5f910097b4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113627"
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

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Azure service pracovního prostoru Machine Learning, místní adresář obsahující skripty a sady SDK Azure Machine Learning pro Python nainstalován. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

- Model trénovaného strojového učení nasadit do Azure Kubernetes Service (AKS). Pokud ho nemáte, přečtěte si článek [trénování modelů klasifikace image](tutorial-train-models-with-aml.md) kurzu.

- [Clusteru AKS](how-to-deploy-to-aks.md).

- Následující závislosti a nainstalovaným modulem [ve vašem prostředí](how-to-configure-environment.md):
  + V Linuxu:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + Ve Windows:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Povolení shromažďování dat
Shromažďování dat může být povoleno bez ohledu na model nasazení prostřednictvím služby Azure Machine Learning nebo jiných nástrojů. 

Ho Pokud chcete povolit, je potřeba:

1. Otevřete soubor vyhodnocení. 

1. Na začátek souboru přidejte následující kód:

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

   ![Upravit službu](media/how-to-enable-data-collection/EditService.png)

1. V **Upřesnit nastavení**, zrušte zaškrtnutí možnosti **shromažďování dat modelu povolit**. 

   ![Zrušte zaškrtnutí políčka shromažďování dat](media/how-to-enable-data-collection/CheckDataCollection.png)

   V tomto okně můžete také "Povolit diagnostiku Appinsights" ke sledování stavu služby.  

1. Vyberte **aktualizace** na použití změny.


## <a name="disable-data-collection"></a>Zakázat shromažďování dat
Můžete zastavit shromažďování dat kdykoli. Shromažďování dat vypnout pomocí kódu v Pythonu nebo na webu Azure portal.

+ Možnost 1 - zakázat na webu Azure Portal: 
  1. Přihlaste se k [portálu Azure](https://portal.azure.com).

  1. Otevřete pracovní prostor.

  1. Přejděte na **nasazení** -> **vyberte službu** -> **upravit**.

     ![Upravit službu](media/how-to-enable-data-collection/EditService.png)

  1. V **Upřesnit nastavení**, zrušte zaškrtnutí možnosti **shromažďování dat modelu povolit**. 

     ![Zrušte zaškrtnutí políčka shromažďování dat](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Vyberte **aktualizace** na použití změny.

* Možnost 2 – použití Pythonu k zakázání shromažďování dat:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Příklad poznámkového bloku

[00. Začínáme Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) Poznámkový blok ukazuje koncepty v tomto článku.  

Získáte tento poznámkový blok:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]