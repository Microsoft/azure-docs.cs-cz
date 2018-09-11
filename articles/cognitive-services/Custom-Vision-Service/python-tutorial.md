---
title: Vytvořit vlastní Vision Service Python kurz – Azure Cognitive Services | Dokumentace Microsoftu
description: Prozkoumejte základní aplikaci v Pythonu, který používá vlastní rozhraní API pro zpracování obrazu ve službě Microsoft Cognitive Services. Vytvoření projektu, přidání značek, nahrávat obrázky, trénování váš projekt a předpověď pomocí výchozí koncový bod.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: df0bdc0bbd2768566336323851f366c9ae280a88
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301595"
---
# <a name="custom-vision-api-python-tutorial"></a>Vlastní kurz Python API pro zpracování obrazu

Zjistěte, jak vytvořit projekt klasifikace obrázků pomocí služby Custom Vision Service a základní skript Pythonu. Po jeho vytvoření je můžete přidat značky, nahrávání obrázků, trénování projektu, získat adresu URL koncového bodu projektu výchozí předpovědi a pomocí něho můžete testovat programově bitovou kopii. Použijte tento příklad open source jako šablonu pro vytváření vlastních aplikací s použitím vlastní rozhraní API pro zpracování obrazu.



## <a name="prerequisites"></a>Požadavky

- Python 2.7 + nebo Python 3.5 +.
- Nástroje pip.

## <a name="get-the-training-and-prediction-keys"></a>Získání klíčů trénování a predikcí

Klíče používané v tomto příkladu najdete [vizi vlastní webovou stránku](https://customvision.ai) a vyberte __ikonu ozubeného kola__ v pravém horním rohu. V __účty__ tématu, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalace sady SDK služby Custom Vision

Pokud chcete nainstalovat sadu SDK služby pro zpracování obrazu vlastní, použijte následující příkaz:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Získat Image příklad

Tento příklad používá Image z `Samples/Images` adresáři [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projektu. Naklonujte nebo stáhněte a extrahujte projekt do svého vývojového prostředí.

## <a name="create-a-custom-vision-service-project"></a>Vytvořte projekt služby Custom Vision Service

Chcete-li vytvořit nový projekt služby Custom Vision Service, vytvořte nový soubor s názvem `sample.py`. Použijte následující kód jako obsah souboru:

> [!IMPORTANT]
> Nastavte `training_key` hodnotě klíče školení, který jste získali dříve.
>
> Nastavte `prediction_key` hodnotě klíče predikcí, který jste získali dříve.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Přidání značek do projektu

Přidání značek do projektu, přidejte následující kód do konce `sample.py` souboru:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Nahrávání obrázků do projektu

Chcete-li přidat do projektu ukázkové obrázky, vložte následující kód po vytvoření značky. Tento kód odešle image s odpovídající značky:

> [!IMPORTANT]
>
> Změňte cestu k bitové kopie, podle kterého jste stáhli projekt Cognitive-CustomVision – Windows, dříve.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Trénování projektu

K trénování třídění, přidejte následující kód do konce `sample.py` souboru:

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Získat a použít výchozí koncový bod predikcí

Odeslat image do koncového bodu předpovědi a načíst do predikce, přidejte následující kód do konce `sample.py` souboru:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Spustit příklad

Spusťte řešení. V konzole se zobrazí predikované výsledky.

```
python sample.py
```

Výstup aplikace se podobá následujícímu textu:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```