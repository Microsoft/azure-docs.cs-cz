---
title: 'Rychlý start: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision SDK pro Python'
titleSuffix: Azure Cognitive Services
description: Vytvořte projekt, přidejte značky, nahrajte obrázky, natrénujte svůj projekt a vytvořte předpověď pomocí sady Python SDK.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: areddish
ms.openlocfilehash: 2654b61ac4489d8257020923a77800745027f303
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403485"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Rychlý start: Vytvoření projektu klasifikace obrázků pomocí sady Custom Vision Python SDK

Tento článek ukazuje, jak začít používat vlastní vision SDK s Pythonem k vytvoření modelu klasifikace obrázků. Po jeho vytvoření můžete přidat značky, nahrát obrázky, trénovat projekt, získat adresu URL koncového bodu publikované předpovědi projektu a použít koncový bod k programovému testování bitové kopie. Tento příklad použijte jako šablonu pro vytvoření vlastní aplikace v Pythonu. Pokud chcete procesem vytvoření a používání modelu klasifikace projít _bez_ kódu, přečtěte si místo toho [pokyny s využitím prohlížeče](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Požadavky

- [Python 2.7+ nebo 3.5+](https://www.python.org/downloads/)
- Nástroj [pip](https://pip.pypa.io/en/stable/installing/)
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="install-the-custom-vision-sdk"></a>Instalace sady Custom Vision SDK

Pokud chcete nainstalovat sadu SDK služby Custom Vision pro Python, spusťte v PowerShellu následující příkaz:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]

## <a name="add-the-code"></a>Přidání kódu

V upřednostňovaném adresáři projektu vytvořte nový soubor *sample.py*.

### <a name="create-the-custom-vision-service-project"></a>Vytvoření projektu služby Custom Vision

Přidáním následujícího kódu do svého skriptu vytvořte nový projekt služby Custom Vision. Do odpovídajících definic vložte své klíče předplatného. Adresu URL koncového bodu si také můžete stáhnout ze stránky nastavení na webu Custom Vision.

Podívejte se na [metodu create_project,](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.custom_vision_training_client.customvisiontrainingclient?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config- ) která určuje další možnosti při vytváření projektu (vysvětleno v příručce Vytvořit průvodce [webovým portálem třídění).](getting-started-build-a-classifier.md)  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Vytvoření značek v projektu

Pokud chcete pro svůj projekt vytvořit klasifikační značky, přidejte na konec souboru *sample.py* následující kód:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Nahrávání a označování obrázků

Ukázkové obrázky do projektu přidáte tak, že po vytvoření značky vložíte následující kód. Tento kód nahraje jednotlivé obrázky s odpovídající značkou. V jedné dávce můžete nahrát až 64 obrázků.

> [!NOTE]
> Budete muset změnit cestu k obrázkům podle toho, kde jste stáhli repo ukázky sady Python v Pythonu.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Trénování třídění a publikování

Tento kód vytvoří první iteraci modelu předpověď a potom publikuje tuto iteraci do koncového bodu předpověď. Název zadaný pro publikovanou iteraci lze použít k odeslání požadavků předpovědi. Iterace není k dispozici v koncovém bodu předpověď, dokud je publikován.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Získat a použít publikovanou iteraci v koncovém bodě předpověď

Pokud chcete odeslat obrázek do koncového bodu předpovědi a načíst předpověď, přidejte na konec souboru následující kód:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte soubor *sample.py*.

```powershell
python sample.py
```

Výstup aplikace by se měl podobat následujícímu textu:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Poté můžete ověřit, zda je testovací obrázek (nalezený v **<base_image_url>obrázků/Test/**) odpovídajícím způsobem označen. Můžete se také vrátit na [web služby Custom Vision](https://customvision.ai) a zobrazit aktuální stav nově vytvořeného projektu.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Další kroky

Nyní jste viděli, jak lze provést každý krok procesu detekce objektů v kódu. Tato ukázka provede jednu trénovací iteraci, ale často budete muset trénovat a testovat model vícekrát, aby byl přesnější.

> [!div class="nextstepaction"]
> [Testování a přetrénování modelu](test-your-model.md)
