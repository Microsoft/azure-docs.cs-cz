---
title: Vytváření a zkoumání datových sad pomocí popisků
titleSuffix: Azure Machine Learning
description: Naučte se exportovat popisky dat z Azure Machine Learning pro označování projektů a použít je pro úlohy strojového učení.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 1293534849c98cee51349bbefd3073cc8b94f876
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647203"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Vytvoření a zkoumání Azure Machine Learning datové sady s popisky

V tomto článku se dozvíte, jak exportovat popisky dat z Azure Machine Learning projektu označování dat a načíst je do oblíbených formátů, jako je například PANDAS dataframe pro zkoumání dat nebo Torchvision datovou sadu pro transformaci obrázku. 

## <a name="what-are-datasets-with-labels"></a>Co jsou datové sady s popisky 

Odkazujeme na Azure Machine Learning datových sad s popisky jako s označenými datovými sadami. Tyto konkrétní typy datových sad s označenými datovými sadami jsou vytvořeny pouze jako výstup Azure Machine Learning projektů pro označování dat. Pomocí [těchto kroků](how-to-create-labeling-projects.md)vytvořte projekt pro označování dat. Machine Learning podporuje popisky dat pro klasifikaci obrázků, a to buď s více popisky, nebo s více třídami, a identifikacemi objektů společně s ohraničenými poli.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) před tím, než začnete.
* [Sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)nebo přístup k [Azure Machine Learning Studiu](https://ml.azure.com/).
    * Instalace balíčku [Azure-contrib-DataSet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py&preserve-view=true)
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
* Přístup k Azure Machine Learning projektu popisků dat. Pokud nemáte projekt s popisem, vytvořte ho pomocí [těchto kroků](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportovat popisky dat 

Po dokončení projektu označování dat můžete exportovat data popisku z projektu označení. Díky tomu můžete zachytit odkaz na data a její popisky a exportovat je ve [formátu díky Coco](http://cocodataset.org/#format-data) nebo jako datovou sadu Azure Machine Learning. Použijte tlačítko **exportovat** na stránce Project **Details (podrobnosti projektu** ) vašeho projektu s popisem.

### <a name="coco"></a>Díky Coco 

 Soubor díky Coco se vytvoří ve výchozím úložišti objektů BLOB v pracovním prostoru Azure Machine Learning ve složce v rámci *exportu/díky Coco*. 

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning datová sada

Exportovaný Azure Machine Learning datovou sadu můžete zpřístupnit v části datové **sady** vaší aplikace Azure Machine Learning Studio. Stránka s **podrobnostmi** datové sady také poskytuje vzorový kód pro přístup k popiskům z Pythonu.

![Exportovaná datová sada](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Prozkoumat datové sady s popisky

Načtěte vaše označené datové sady do PANDAS dataframe nebo Torchvision DataSet k využití oblíbených open source knihoven pro zkoumání dat a také PyTorch poskytovaných knihoven pro transformaci a školení imagí.

### <a name="pandas-dataframe"></a>PANDAS – datový rámec

Můžete načíst označené datové sady do PANDAS dataframe pomocí [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody z `azureml-contrib-dataset` třídy. Nainstalujte třídu pomocí následujícího příkazu prostředí: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Obor názvů AzureML. contrib se často mění, protože budeme spolupracovat na vylepšení služby. V takovém případě by se cokoli v tomto oboru názvů měly považovat za verzi Preview a společnost Microsoft je plně Nepodporovaná.

Při převodu na PANDAS dataframe nabízíme následující možnosti zpracování souborů pro datové proudy.
* Stáhnout: Stáhněte si datové soubory do místní cesty.
* Připojit: Připojte datové soubory k přípojnému bodu. Mount funguje jenom pro výpočetní služby založené na systému Linux, včetně virtuálního počítače s Azure Machine Learning poznámkového bloku a Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision datové sady

Pomocí metody [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#&preserve-view=trueto-torchvision--) lze z třídy načítat datové sady s popisky do datové sady Torchvision `azureml-contrib-dataset` . Pokud chcete použít tuto metodu, musíte mít nainstalovanou [PyTorch](https://pytorch.org/) . 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Další kroky

* Kompletní ukázku školení najdete v [poznámkovém bloku s popisem pro sadu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) .
