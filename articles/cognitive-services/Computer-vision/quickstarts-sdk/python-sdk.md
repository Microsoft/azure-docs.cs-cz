---
title: 'Rychlý start: Python SDK'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak používat sadu Python SDK pro běžné úlohy.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/26/2019
ms.author: pafarley
ms.openlocfilehash: d14b9c88b447583eedc8b50f4f9acf80ae4e3c75
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889626"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Azure Cognitive Services pro počítačové zpracování obrazu SDK pro Python

Služba počítačového zpracování obrazu umožňuje vývojářům používat pokročilé algoritmy, které zpracovávají obrázky a vrací informace. Algoritmy pro zpracování obrazu počítače analyzovat obsah image různými způsoby v závislosti na vizuální funkce, které vás zajímají. 

* [Analýza obrázku](#analyze-an-image)
* [Seznam domén předmětu GET](#get-subject-domain-list)
* [Analýza obrázku podle domény](#analyze-an-image-by-domain)
* [Získat textový popis obrázku](#get-text-description-of-an-image)
* [Získání rukou psaný text z obrázků](#get-text-from-image)
* [Vygenerování thumbnail](#generate-thumbnail)

Další informace o této službě najdete v tématu [co je pro počítačové zpracování obrazu?] [computervision_docs].

Hledáte další dokumentaci?

* [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Dokumentace ke službě cognitive Services pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Požadavky

* [Python 3.6 +][python]
* Bezplatné [klíč pro počítačové zpracování obrazu] [ computervision_resource] a přidruženou oblastí. Tyto hodnoty budete potřebovat při vytváření instance třídy [ComputerVisionAPI] [ ref_computervisionclient] objektu klienta. Použijte jednu z následujících metod k získání těchto hodnot. 

### <a name="if-you-dont-have-an-azure-subscription"></a>Pokud nemáte předplatné Azure

Vytvořit bezplatný klíč platný po dobu 7 dní s **vyzkoušet** prostředí. Když se klíč, zkopírujte název klíče a oblast. Budete ho potřebovat k [vytvoření klienta](#create-client).

Po vytvoření klíče zachovat následující:

* Hodnotu klíče: řetězec 32 znaků ve formátu `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` 
* Klíčové oblasti: subdoménu adresu URL koncového bodu https://**westcentralus**. api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Pokud máte předplatné Azure

Pokud budete potřebovat účet rozhraní API pro počítačové zpracování obrazu, nejjednodušším způsobem, jak vytvořit v rámci vašeho předplatného je používání následujících [rozhraní příkazového řádku Azure] [ azure_cli] příkazu. Musíte zvolit název skupiny prostředků, například "my-cogserv-group" a název prostředku pro zpracování obrazu počítače, jako je například "my--pro zpracování obrazu – prostředku v počítači". 

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Instalace sady SDK

Nainstalovat Azure Cognitive Services počítače Vision SDK pro Python [balíčku] [ pypi_computervision] s [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Po vytvoření prostředku pro počítačové zpracování obrazu, budete potřebovat jeho **oblasti**a jeden z jeho **klíče účtu** pro vytvoření instance objektu klienta.

Tyto hodnoty použít při vytváření instance [ComputerVisionAPI] [ ref_computervisionclient] objektu klienta. 

<!--

For example, use the Bash terminal to set the environment variables:

```Bash
ACCOUNT_REGION=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### For Azure subscription usrs, get credentials for key and region

If you do not remember your region and key, you can use the following method to find them. If you need to create a key and region, you can use the method for [Azure subscription holders](#if-you-have-an-azure-subscription) or for [users without an Azure subscription](#if-you-dont-have-an-azure-subscription).

Use the [Azure CLI][cloud_shell] snippet below to populate two environment variables with the Computer Vision account **region** and one of its **keys** (you can also find these values in the [Azure portal][azure_portal]). The snippet is formatted for the Bash shell.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```
-->

### <a name="create-client"></a>Vytvoření klienta

Vytvořte [ComputerVisionAPI] [ ref_computervisionclient] objektu klienta. Změňte hodnoty oblasti a klíč v následujícím příkladu kódu na vaše vlastní hodnoty.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

region = "westcentralus"
key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

Je nutné [ComputerVisionAPI] [ ref_computervisionclient] objektu klienta před použitím některého z následujících úloh.

### <a name="analyze-an-image"></a>Analýza obrázku

Můžete analyzovat image pro určité funkce s [ `analyze_image` ] [ ref_computervisionclient_analyze_image]. Použití [ `visual_features` ] [ ref_computervision_model_visualfeatures] vlastnosti chcete nastavit typy analýzy provádět v imagi. Běžné hodnoty jsou `VisualFeatureTypes.tags` a `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Seznam domén předmětu GET

Zkontrolujte domény předmětu, který slouží k analýze bitovou kopii s [ `list_models` ] [ ref_computervisionclient_list_models]. Tyto názvy domény se používají při [analýza image podle domény](#analyze-an-image-by-domain). Je například domény `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analýza obrázku podle domény

Můžete analyzovat image podle domény předmětu s [ `analyze_image_by_domain` ] [ ref_computervisionclient_analyze_image_by_domain]. Získejte [seznam podporovaných domény předmětu](#get-subject-domain-list) Chcete-li použít správný název domény.  

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Získat textový popis obrázku

Můžete získat založený na jazyce textový popis image s [ `describe_image` ] [ ref_computervisionclient_describe_image]. Požádat o několik popis s `max_description` vlastnost provádíte analýzu textu pro klíčová slova spojená s touto imagí. Příklady textový popis na následujícím obrázku `a train crossing a bridge over a body of water`, `a large bridge over a body of water`, a `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Získat text z obrázků

Rukou psaný nebo tisk text můžete získat z image. To vyžaduje dvě volání sady SDK: [ `recognize_text` ] [ ref_computervisionclient_recognize_text] a [ `get_text_operation_result` ] [ ref_computervisionclient_get_text_operation_result]. Volání recognize_text je asynchronní. Ve výsledcích get_text_operation_result volání, je potřeba zkontrolovat, pokud první volání byla dokončena s [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] před extrahováním textová data. Budou výsledky obsahovat text, jakož i ohraničující pole souřadnic pro text. 

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Vygenerování thumbnail

Image s Miniatura (JPG) můžete vygenerovat [ `generate_thumbnail` ] [ ref_computervisionclient_generate_thumbnail]. Miniaturu se nemusí být ve stejné rozměry jako původní bitové kopie. 

Nainstalujte **Poduškový** používat v tomto příkladu:

```bash
pip install Pillow
``` 

Po instalaci Poduškový, vygeneruje se obrázek miniatury pomocí balíčku v následujícím příkladu kódu.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="general"></a>Obecné

Když budete moct používat [ComputerVisionAPI] [ ref_computervisionclient] objektu klienta pomocí sady Python SDK [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] třída se používá k Vrátí chyby. Chyby vrácené službou odpovídají stejné vrácené stavové kódy HTTP pro požadavky rozhraní REST API.

Například, pokud se pokusíte analyzovat bitovou kopii s neplatný klíč `401` chyba je vrácena. V následujícím fragmentu kódu [chyba] [ ref_httpfailure] řádně zařizuje služba zachycení výjimky a zobrazení dalších informací o chybě.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Zpracování přechodných chyb pomocí opakovaných pokusů

Při práci s [ComputerVisionAPI] [ ref_computervisionclient] klienta, může dojít přechodné chyby způsobené [omezení přenosové rychlosti] [ computervision_request_units] vynucena Služba, nebo jiné přechodné problémy, jako jsou výpadky sítě. Informace o manipulaci s těmito typy chyb, naleznete v tématu [modelu opakování] [ azure_pattern_retry] v příručce vzory návrhu v cloudu a související [vzoru Circuit Breaker] [azure_pattern_circuit_breaker].

### <a name="more-sample-code"></a>Další ukázkový kód

Několik vzorových počítače pro zpracování obrazu Python SDK jsou k dispozici v úložišti GitHub v sadě SDK. Tyto ukázky zadejte ukázkový kód pro další scénáře běžně došlo při práci s pro počítačové zpracování obrazu:

* [recognize_text][recognize-text]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení obsahu klíčových slov do bitové kopie](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

