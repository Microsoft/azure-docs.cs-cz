---
title: Vytvoření vlastní vize služby Python kurz – kognitivní služeb Azure | Microsoft Docs
description: Prozkoumejte základní aplikaci Python, která používá rozhraní API vize vlastní v kognitivní služby společnosti Microsoft. Vytvoření projektu, se přidat značky, odesílání bitové kopie, cvičení projektu a předpovědím pomocí výchozí koncový bod.
services: cognitive-services
author: areddish
manager: chbuehle
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/07/2018
ms.author: areddish
ms.openlocfilehash: 0359935bf266d4f2a5cf845dd0d23183f4f77b72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342999"
---
# <a name="custom-vision-api-python-tutorial"></a>Vlastní kurz vize API Python

Naučte se vytvořit projekt klasifikace obrázku se služba vlastní vize a základní skript v jazyce Python. Po vytvoření, můžete přidat značky, nahrajte Image, cvičení projektu, získat adresu URL koncového bodu projektu výchozí předpovědi a použít ho k prostřednictvím kódu programu testování bitovou kopii. Použijte tento příklad open-source jako šablonu pro vytvoření vlastní aplikace pomocí rozhraní API vize vlastní.



## <a name="prerequisites"></a>Požadavky

- Python 2.7 + nebo Python 3.5 +.
- Nástroj pip.

## <a name="get-the-training-and-prediction-keys"></a>Získání klíčů školení a předpovědi

Ke klíčů používaných v tomto příkladu najdete [vize vlastní webové stránky](https://customvision.ai) a vyberte __ozubené kolečko ikonu__ v pravém horním rohu. V __účty__ část, zkopírujte hodnoty z __školení klíč__ a __předpovědi klíč__ pole.

![Obrázek klíče uživatelského rozhraní](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalace sady SDK služby vlastní vize

K instalaci sady SDK služby vlastní vize, použijte následující příkaz:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Získat příklad obrázků

Tento příklad používá bitové kopie z `Samples/Images` adresář [ https://github.com/Microsoft/Cognitive-CustomVision-Windows ](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images) projektu. Klonovat nebo stáhnout a extrahujte projekt, do vývojového prostředí.

## <a name="create-a-custom-vision-service-project"></a>Vytvoření projektu vlastní vize služby

Chcete-li vytvořit nový projekt vlastní vize služeb, vytvořte nový soubor s názvem `sample.py`. Použijte následující kód jako obsah souboru:

> [!IMPORTANT]
> Nastavte `training_key` k hodnotě klíče školení, který jste získali dříve.
>
> Nastavte `prediction_key` k hodnotě klíče předpovědi, který jste získali dříve.

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

## <a name="add-tags-to-your-project"></a>Přidat značky do projektu

Pokud chcete přidat značky do projektu, přidejte následující kód do konce `sample.py` souboru:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Nahrajte Image do projektu

Chcete-li do projektu přidejte ukázkové obrázky, vložte následující kód po vytvoření značky. Tento kód odešle bitové kopie s odpovídající značky:

> [!IMPORTANT]
>
> Změňte cestu k bitové kopie založené na kterém jste projekt systému Windows Cognitive CustomVision předtím stáhli.

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

## <a name="train-the-project"></a>Cvičení projektu

Při školení třídění, přidejte následující kód do konce `sample.py` souboru:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Získat a použít výchozí koncový bod předpovědi

Odeslat bitovou kopii do koncového bodu předpovědi a načíst předpovědi, přidejte následující kód do konce `sample.py` souboru:

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

## <a name="run-the-example"></a>Spuštění ukázkového

Spusťte řešení. Předpověď výsledky se zobrazí v konzole.

```
python sample.py
```

Výstup aplikace je podobná následující text:

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