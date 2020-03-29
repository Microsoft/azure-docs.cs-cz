---
title: Vytváření a zkoumání datových sad pomocí popisků
titleSuffix: Azure Machine Learning
description: Zjistěte, jak exportovat popisky dat z projektů označování Azure Machine Learning a použít je pro úlohy strojového učení.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549486"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Vytvoření a prozkoumání datové sady Azure Machine Learning pomocí popisků

V tomto článku se dozvíte, jak exportovat popisky dat z projektu označování dat Azure Machine Learning a načíst je do oblíbených formátů, jako je například datový rámec pandas pro zkoumání dat nebo datová sada Torchvision pro transformaci bitových obrázků. 

## <a name="what-are-datasets-with-labels"></a>Co jsou datové sady s popisky 

Datové sady Azure Machine Learning s popisky jsou [TabularDatasets](how-to-create-register-datasets.md#dataset-types) s vlastností popisku, budeme na ně odkazovat jako na datové sady s popiskem. Tyto konkrétní typy TabularDatasets jsou vytvořeny pouze jako výstup projektů označování dat Azure Machine Learning. Vytvořte projekt označování dat [pomocí těchto kroků](how-to-create-labeling-projects.md). Strojové učení podporuje projekty označování dat pro klasifikaci obrázků, buď vícepopisek nebo více tříd, a identifikaci objektů spolu s ohraničenými poli.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://aka.ms/AMLFree) než začnete.
* [Sada Azure Machine Learning SDK pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)nebo přístup k azure machine learning [studio](https://ml.azure.com/).
    * Instalace balíčku [azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Pracovní prostor Machine Learning. Viz [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).
* Přístup k projektu označování dat Azure Machine Learning. Pokud nemáte projekt označování, vytvořte jej [pomocí těchto kroků](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Export popisků dat 

Po dokončení projektu označování dat můžete exportovat data popisků z projektu popisků. Díky tomu můžete zachytit odkaz na data a jejich popisky a exportovat je ve [formátu COCO](http://cocodataset.org/#format-data) nebo jako datová sada Azure Machine Learning. Tlačítko **Exportovat** na stránce **Podrobnosti projektu** projektu projektu s popisky.

### <a name="coco"></a>Coco 

 Soubor COCO se vytvoří ve výchozím úložišti objektů blob pracovního prostoru Azure Machine Learning ve složce v *rámci exportu/kokosu*. 

### <a name="azure-machine-learning-dataset"></a>Datová sada Azure Machine Learning

Exportované datové sady Azure Machine Learning můžete přistupovat v části **Datové sady** ve vašem centru Azure Machine Learning. Stránka **Podrobnosti** datové sady také poskytuje ukázkový kód pro přístup k vašim popiskům z Pythonu.

![Exportovaná datová sada](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Prozkoumat popisované datové sady

Načtěte své popisované datové sady do datového rámce pand nebo datové sady Torchvision, abyste využili populární open-source knihovny pro zkoumání dat, stejně jako knihovny pytorch k dispozici pro transformaci obrazu a školení.

### <a name="pandas-dataframe"></a>Pandas datový rám

Můžete načíst popisované datové sady do datového rámce pandas pomocí [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metody z `azureml-contrib-dataset` třídy. Nainstalujte třídu pomocí následujícího příkazu prostředí: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Obor názvů azureml.contrib se často mění, protože pracujeme na zlepšení služby. Jako takové by mělo být cokoli v tomto oboru názvů považováno za náhled a nebylo plně podporováno společností Microsoft.

Nabízíme následující možnosti zpracování souborů pro datové proudy souborů při převodu na datový rámec pandy.
* Ke stažení: Stáhněte si datové soubory na místní cestu.
* Připojení: Připojte datové soubory k přípojné muzice. Mount funguje jenom pro výpočetní prostředky založené na Linuxu, včetně virtuálního počítače Azure Machine Learning a Azure Machine Learning Compute.

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

### <a name="torchvision-datasets"></a>Datové sady Torchvision

Můžete načíst označené datové sady do datové sady Torchvision pomocí `azureml-contrib-dataset` metody [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) také z třídy. Chcete-li použít tuto metodu, musíte mít [pytorch](https://pytorch.org/) nainstalován. 

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

* Kompletní ukázku školení najdete v [datové sadě s poznámkovým blokem popisků.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb)
