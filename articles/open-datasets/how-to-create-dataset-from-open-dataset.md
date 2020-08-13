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
ms.openlocfilehash: e6f3a541f1e9dbca2c9949fb0c5cde28cd43e8e5
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88183008"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Vytváření Azure Machine Learning datových sad z Azure Open DataSets
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak vytvořit datovou sadu Azure Machine Learning z [Azure Open DataSets](https://azure.microsoft.com/services/open-datasets/) pro přístup k datům pro místní nebo vzdálené experimenty. 

Vytvořením [datové sady Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md)vytvoříte odkaz na umístění zdroje dat společně s kopií jeho metadat. Vzhledem k tomu, že data zůstanou ve svém stávajícím umístění, nebudete mít žádné dodatečné náklady na úložiště a nebudete nechtěně měnit původní zdroje dat. Datové sady jsou také vyhodnocovány laxně vytvářená, což pomáhá zrychlit výkon pracovního postupu.
 
Informace o tom, kde se datové sady vejdou do celého pracovního postupu pro přístup k datům v Azure Machine Learning, najdete v článku [zabezpečený přístup k datům](../machine-learning/concept-data.md#data-workflow) .


Otevřené datové sady jsou v cloudu na Microsoft Azure a jsou zahrnuté v sadě [SDK pro Azure Machine Learning Python](#create-datasets-with-the-sdk) i v sadě [Azure Machine Learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Proč používat Azure Open DataSets? 

Otevřené datové sady v Azure jsou spravované veřejné datové sady, které můžete použít k přidání funkcí specifických pro konkrétní scénář do řešení Machine Learning pro přesnější modely. Datové sady zahrnují data z veřejných domén pro počasí, sčítání, svátky, veřejné zabezpečení a umístění, které vám pomůžou naučit modely strojového učení a rozšířit prediktivní řešení. 

Další informace najdete v tématu [co jsou otevřené datové sady](overview-what-are-open-datasets.md) .

## <a name="prerequisites"></a>Předpoklady

K vytváření a práci s datovými sadami potřebujete:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

* [Pracovní prostor Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [Nainstalovaná sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), která zahrnuje balíček AzureML-DataSet Sets.

    * Vytvořte [instanci služby Azure Machine Learning COMPUTE](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), což je plně nakonfigurované a spravované vývojové prostředí, které zahrnuje integrované poznámkové bloky a sadu SDK už je nainstalovaná.

    **ANI**

    * Pracujte na vlastním poznámkovém bloku Jupyter a nainstalujte sadu SDK sami s [těmito pokyny](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Některé třídy DataSet mají závislosti na balíčku [AzureML-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , který je kompatibilní pouze s 64-bitovým Pythonem. Pro uživatele se systémem Linux jsou tyto třídy podporovány pouze v následujících distribucích: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) a CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Vytvoření datových sad pomocí sady SDK

Chcete-li vytvořit datové sady prostřednictvím tříd Open DataSets v sadě Azure Machine Learning Python SDK, ujistěte se, že jste balíček nainstalovali pomocí nástroje `pip install azureml-opendatasets` . Jednotlivé diskrétní datové sady jsou reprezentovány vlastní třídou v sadě SDK a některé třídy jsou k dispozici jako `TabularDataset` , `FileDataset` nebo obojí. Úplný seznam tříd naleznete v [referenční dokumentaci](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Můžete načíst určité třídy jako `TabularDataset` nebo `FileDataset` , což umožňuje pracovat přímo nebo stahovat soubory. Jiné třídy mohou získat datovou sadu **pouze** pomocí jedné z `get_tabular_dataset()` `get_file_dataset()` funkcí nebo. Následující ukázka kódu ukazuje několik příkladů těchto typů tříd.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Když zaregistrujete datovou sadu vytvořenou z otevřených datových sad, nestahují se okamžitě žádná data, ale data budou k dispozici později, až se vyžádají (například během školení) z centrálního umístění úložiště.

## <a name="create-datasets-with-the-studio"></a>Vytváření datových sad pomocí studia

Datové sady můžete také vytvořit z otevřených datových sad pomocí nástroje [Azure Machine Learning Studio](https://ml.azure.com).

1. V pracovním prostoru vyberte kartu datové **sady** v části **prostředky**. V rozevírací nabídce **vytvořit datovou sadu** vyberte možnost **z otevřených datových sad**.

    ![Otevřít datovou sadu s uživatelským rozhraním](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Vyberte datovou sadu tím, že vyberete její dlaždici. (Máte možnost filtrovat pomocí panelu hledání.) Vyberte **Další**.

    ![Zvolit datovou sadu](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Vyberte název, pod kterým chcete datovou sadu zaregistrovat, a volitelně data filtrujte pomocí dostupných filtrů. V tomto případě můžete pro datovou sadu **veřejných svátků** filtrovat časové období na jeden rok a směrové číslo země pouze na nás. Vyberte **Vytvořit**.

    ![Nastavení parametrů datové sady a vytvoření datové sady](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Datová sada je teď v pracovním prostoru v části datové **sady**dostupná. Můžete ji použít stejným způsobem jako jiné datové sady, které jste vytvořili.


## <a name="access-datasets-for-your-experiments"></a>Přístup k datovým sadám pro vaše experimenty

Využijte své datové sady ve zkušebních experimentech Machine Learning pro školení modelů ML. [Přečtěte si další informace o tom, jak naučit s datovými sadami](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Příklady poznámkových bloků

Příklady a ukázky funkcí otevřít datové sady najdete v těchto [poznámkových blocích](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Další kroky

* [Výuka modelu s](../machine-learning/how-to-train-with-datasets.md)datovými sadami.

* [Vytvořte datovou sadu Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



