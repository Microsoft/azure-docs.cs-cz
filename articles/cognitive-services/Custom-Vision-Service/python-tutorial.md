---
title: 'Kurz: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK pro Python'
titlesuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, vytrénujte svůj projekt a vytvořte předpověď pomocí výchozího koncového bodu.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 96125ba1c54f742bb9ddf32a1588173217be0766
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953108"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-python"></a>Kurz: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK pro Python

Zjistěte, jak vytvořit projekt klasifikace obrázků pomocí služby Custom Vision Service a základního skriptu Pythonu. Po jeho vytvoření můžete přidat značky, nahrát obrázky, vytrénovat projekt, získat adresu URL výchozího koncového bodu předpovědi projektu a použít jej k programovému testování obrázku. Tento opensourcový příklad použijte jako šablonu pro vytvoření vlastní aplikace pomocí rozhraní Custom Vision API.



## <a name="prerequisites"></a>Požadavky

- Python 2.7+ nebo Python 3.5+.
- Nástroj pip.

## <a name="get-the-training-and-prediction-keys"></a>Získání tréninkového klíče a klíče předpovědi

Klíče používané v tomto příkladu získáte tak, že přejdete na [webovou stránku služby Custom Vision](https://customvision.ai) a vyberete __ikonu ozubeného kola__ v pravém horním rohu. V části __Účty__ zkopírujte hodnoty z polí pro __tréninkový klíč__ a __klíč předpovědi__.

![Obrázek uživatelského rozhraní klíčů](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalace sady Custom Vision Service SDK

K instalaci sady Custom Vision Service SDK použijte následující příkaz:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Získání obrázků pro příklad

Tento příklad používá obrázky z adresáře `Samples/Images` projektu [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Naklonujte nebo stáhněte a extrahujte projekt do svého vývojového prostředí.

## <a name="create-a-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision Service

Nový projekt služby Custom Vision Service vytvoříte tak, že vytvoříte nový soubor s názvem `sample.py`. Jako obsah souboru použijte následující kód:

> [!IMPORTANT]
> Nastavte pro `training_key` hodnotu tréninkového klíče, kterou jste získali dříve.
>
> Nastavte pro `prediction_key` hodnotu klíče předpovědi, kterou jste získali dříve.

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

Značky do projektu přidáte připojením následujícího kódu na konec souboru `sample.py`:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Nahrání obrázků do projektu

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód odešle obrázek s odpovídající značkou:

> [!IMPORTANT]
>
> Změňte cestu k obrázku podle toho, kam jste dříve stáhli projekt Cognitive-CustomVision-Windows.

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

Vytrénujte klasifikátor přidáním následujícího kódu na konec souboru `sample.py`:

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

## <a name="get-and-use-the-default-prediction-endpoint"></a>Získání a použití výchozího koncového bodu předpovědi

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru `sample.py` následující kód:

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

## <a name="run-the-example"></a>Spuštění příkladu

Spusťte řešení. V konzole se zobrazí výsledky předpovědi.

```
python sample.py
```

Výstup aplikace je podobný následujícímu textu:

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