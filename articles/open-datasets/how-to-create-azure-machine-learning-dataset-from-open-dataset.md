---
title: Vytváření datových sad pomocí otevřených datových sad Azure
titleSuffix: Azure Open Datasets
description: Naučte se vytvořit datovou sadu Azure Machine Learning z Azure Open DataSets.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 6b9357c0fcf414c2575ca6966e8e5a3716015058
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654911"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Vytváření Azure Machine Learning datových sad z Azure Open DataSets

V tomto článku se dozvíte, jak přenést data o obohacení do místních nebo vzdálených strojových kurzů pomocí [Azure Machine Learningch](../machine-learning/overview-what-is-azure-ml.md) datových sad a [otevřených datových sad Azure](./index.yml). 

Vytvořením [datové sady Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md)vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že datové sady jsou vyhodnoceny laxně vytvářená a data zůstávají v jejím existujícím umístění, je
* Neúčtují se žádné dodatečné náklady na úložiště.
* Nehrozí neriziková neúmyslná změna původních zdrojů dat. 
* Zvýšení rychlosti výkonu pracovního postupu ML.

Informace o tom, kde se datové sady vejdou do celého pracovního postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](../machine-learning/concept-data.md#data-workflow) .

Otevřené datové sady Azure jsou podmnožinou veřejných datových sad, které můžete použít k přidání funkcí specifických pro scénář k rozšíření prediktivních řešení a zlepšení jejich přesnosti. Podívejte se na [katalog otevřených datových sad](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) pro data veřejné domény, která vám pomůžou naučit modely strojového učení, třeba:

* [před](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [statistickým](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [svátků](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [veřejné zabezpečení](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou zahrnuté v sadě [SDK pro Azure Machine Learning Python](#create-datasets-with-the-sdk) i v sadě [Azure Machine Learning Studio](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Požadavky

Pro tento článek potřebujete:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* [Pracovní prostor Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [Sada Azure Machine Learning SDK pro Python je nainstalovaná](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), včetně `azureml-datasets` balíčku.

    * Vytvořte [instanci služby Azure Machine Learning COMPUTE](../machine-learning/how-to-create-manage-compute-instance.md), což je plně nakonfigurované a spravované vývojové prostředí, které zahrnuje integrované poznámkové bloky a sadu SDK už je nainstalovaná.

    **OR**

    * Pracujte ve vlastním prostředí Pythonu a nainstalujte sadu SDK sami pomocí [těchto pokynů](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

> [!NOTE]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](/python/api/azureml-dataprep/) , který je kompatibilní pouze s 64-bitovým Pythonem. Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) a CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Vytvoření datových sad pomocí sady SDK

Pokud chcete vytvořit Azure Machine Learning datové sady prostřednictvím tříd Azure Open DataSets v sadě Python SDK, ujistěte se, že jste balíček nainstalovali pomocí nástroje `pip install azureml-opendatasets` . Jednotlivé diskrétní datové sady jsou reprezentovány vlastní třídou v sadě SDK a některé třídy jsou k dispozici jako Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)nebo obojí. Úplný seznam tříd naleznete v [referenční dokumentaci](/python/api/azureml-opendatasets/azureml.opendatasets?preserve-view=true&view=azure-ml-py) `opendatasets` .

Můžete načíst určité `opendatasets` třídy jako `TabularDataset` nebo `FileDataset` , což umožňuje pracovat přímo nebo stahovat soubory. Jiné třídy mohou získat datovou sadu **pouze** pomocí `get_tabular_dataset()` funkcí nebo `get_file_dataset()` z `Dataset` třídy v sadě Python SDK.

Následující kód ukazuje, že třída MNIST ručně zapsaných `opendatasets` může vracet buď `TabularDataset` nebo `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

V tomto příkladu `opendatasets` je třída diabetes k dispozici pouze jako `TabularDataset` , a proto použití `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Registrace datových sad

Zaregistrujte datovou sadu Azure Machine Learning s vaším pracovním prostorem, abyste je mohli sdílet s ostatními a znovu je použít ve vašem pracovním prostoru. Když zaregistrujete datovou sadu Azure Machine Learning vytvořenou z otevřených datových sad, data se okamžitě nestáhnou, ale data budou k dispozici později, až se vyžádají (například během školení) z centrálního umístění úložiště.

K registraci vašich datových sad pomocí pracovního prostoru použijte [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-&preserve-view=true ) metodu. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Vytváření datových sad pomocí studia

Můžete také vytvořit Azure Machine Learning datové sady z Azure Open DataSets pomocí [Azure Machine Learning studia](https://ml.azure.com), konsolidovaného webového rozhraní, které zahrnuje nástroje strojového učení, k provádění scénářů pro datové vědy u všech úrovní dovedností.

> [!Note]
> Datové sady vytvořené prostřednictvím Azure Machine Learning studia se automaticky zaregistrují do pracovního prostoru.

1. V pracovním prostoru vyberte kartu datové **sady** v části **prostředky**. V rozevírací nabídce **vytvořit datovou sadu** vyberte možnost **z otevřených datových sad**.

    ![Otevřít datovou sadu s uživatelským rozhraním](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Vyberte datovou sadu tím, že vyberete její dlaždici. (Máte možnost filtrovat pomocí panelu hledání.) Vyberte **Další**.

    ![Zvolit datovou sadu](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Vyberte název, pod kterým chcete datovou sadu zaregistrovat, a volitelně data filtrujte pomocí dostupných filtrů. V tomto případě můžete pro datovou sadu **veřejných svátků** filtrovat časové období na jeden rok a směrové číslo země pouze na nás. Podrobnosti o datech najdete v [katalogu Open DataSets v Azure](https://azure.microsoft.com/services/open-datasets/catalog) , jako jsou popisy polí a rozsahy dat. Vyberte **Vytvořit**.

    ![Nastavení parametrů datové sady a vytvoření datové sady](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Datová sada je teď v pracovním prostoru v části datové **sady** dostupná. Můžete ji použít stejným způsobem jako jiné datové sady, které jste vytvořili.


## <a name="access-datasets-for-your-experiments"></a>Přístup k datovým sadám pro vaše experimenty

Využijte své datové sady ve zkušebních experimentech Machine Learning pro školení modelů ML. [Přečtěte si další informace o tom, jak naučit s datovými sadami](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Příklady a ukázky funkcí otevřít datové sady najdete v těchto [ukázkových poznámkových blocích](samples.md).

## <a name="next-steps"></a>Další kroky

* [Nahlaste svůj první model ml](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Výuka s datovými sadami](../machine-learning/how-to-train-with-datasets.md)

* [Vytvořte datovou sadu Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).
