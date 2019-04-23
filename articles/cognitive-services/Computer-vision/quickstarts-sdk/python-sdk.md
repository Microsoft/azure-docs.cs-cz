---
title: 'Rychlý start: Python SDK'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak pomocí sady Python SDK pro běžné úlohy, jako například Analýza obrázků, získat popis, rozpoznání textu a generování miniatur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 95705e7b7a372867e33c86826f44e380407dfee1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60336809"
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
* Bezplatné [klíč pro počítačové zpracování obrazu] [ computervision_resource] a související koncový bod. Tyto hodnoty budete potřebovat při vytváření instance třídy [ComputerVisionClient] [ ref_computervisionclient] objektu klienta. Použijte jednu z následujících metod k získání těchto hodnot.

### <a name="if-you-dont-have-an-azure-subscription"></a>Pokud nemáte předplatné Azure

Vytvořit bezplatný klíč platný po dobu 7 dní s **[vyzkoušet] [ computervision_resource]** prostředí pro službu pro počítačové zpracování obrazu. Když se klíč, zkopírujte název klíče a koncových bodů. Budete ho potřebovat k [vytvoření klienta](#create-client).

Po vytvoření klíče zachovat následující:

* Hodnotu klíče: řetězec 32 znaků ve formátu `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Klíče koncového bodu: adresu URL základního koncového bodu https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Pokud máte předplatné Azure

Nejjednodušším způsobem, jak vytvořit prostředek v rámci vašeho předplatného je používání následujících [rozhraní příkazového řádku Azure] [ azure_cli] příkazu. Tím se vytvoří klíč služby Cognitive Services, který lze použít v rámci mnoha služeb cognitive services. Musíte zvolit _existující_ název skupiny prostředků, například "my-cogserv-group" a nový prostředek pro zpracování obrazu počítač pojmenovat, jako je například "my--pro zpracování obrazu – prostředku v počítači".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
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

Po vytvoření prostředku pro počítačové zpracování obrazu, budete potřebovat jeho **koncový bod**a jeden z jeho **klíče účtu** pro vytvoření instance objektu klienta.

Tyto hodnoty použít při vytváření instance [ComputerVisionClient] [ ref_computervisionclient] objektu klienta.

Například k nastavení proměnných prostředí použijte Bash terminálu:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Uživatelé předplatného Azure získání přihlašovacích údajů pro klíč a koncový bod

Pokud si nepamatujete koncového bodu a klíče, můžete je najít následující metodu. Pokud je potřeba vytvořit klíč a koncový bod, můžete použít metodu pro [držiteli předplatného Azure](#if-you-have-an-azure-subscription) nebo [uživatelé bez předplatného Azure](#if-you-dont-have-an-azure-subscription).

Použití [rozhraní příkazového řádku Azure] [ cloud_shell] fragment k naplnění dvou proměnných prostředí s účtem pro počítačové zpracování obrazu **koncový bod** a jeden z jeho **klíče**(můžete také najít tyto hodnoty [webu Azure portal][azure_portal]). Fragment kódu je ve formátu pro prostředí Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Vytvoření klienta

Získání koncového bodu a klíče z proměnných prostředí a vytvořit [ComputerVisionClient] [ ref_computervisionclient] objektu klienta.

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Příklady

Je nutné [ComputerVisionClient] [ ref_computervisionclient] objektu klienta před použitím některého z následujících úloh.

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

Rukou psaný nebo tisk text můžete získat z image. To vyžaduje dvě volání sady SDK: [ `batch_read_file` ](https://docs.microsoft.com/en-us/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#batch-read-file-url--mode--custom-headers-none--raw-false----operation-config-) a [ `get_read_operation_result` ](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python#get-read-operation-result-operation-id--custom-headers-none--raw-false----operation-config-). Volání `batch_read_file` je asynchronní. Ve výsledcích `get_read_operation_result` volání, je potřeba zkontrolovat, pokud první volání byla dokončena s [ `TextOperationStatusCodes` ] [ ref_computervision_model_textoperationstatuscodes] před extrahováním textová data. Budou výsledky obsahovat text, jakož i ohraničující pole souřadnic pro text.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextRecognitionMode
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
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

Když budete moct používat [ComputerVisionClient] [ ref_computervisionclient] objektu klienta pomocí sady Python SDK [ `ComputerVisionErrorException` ] [ ref_computervision_computervisionerrorexception] třída se používá Chcete-li vrátit chyby. Chyby vrácené službou odpovídají stejné vrácené stavové kódy HTTP pro požadavky rozhraní REST API.

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
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Zpracování přechodných chyb pomocí opakovaných pokusů

Při práci s [ComputerVisionClient] [ ref_computervisionclient] klienta, může dojít přechodné chyby způsobené [omezení přenosové rychlosti] [ computervision_request_units] vynucuje služby nebo jiné přechodné problémy, jako jsou výpadky sítě. Informace o manipulaci s těmito typy chyb, naleznete v tématu [modelu opakování] [ azure_pattern_retry] v příručce vzory návrhu v cloudu a související [vzoru Circuit Breaker] [azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení obsahu klíčových slov do bitové kopie](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
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


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
