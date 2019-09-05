---
title: 'Rychlý start: Python SDK'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak používat sadu Python SDK pro běžné úkoly, jako je například analýza obrázku, získání popisu, rozpoznání textu a generování miniatury.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: pafarley
ms.openlocfilehash: bc42edc3e97aa68c5fe9d2b3162913e8925df4ee
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375655"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Sada Azure Cognitive Services Počítačové zpracování obrazu SDK pro Python

Služba počítačového zpracování obrazu umožňuje vývojářům používat pokročilé algoritmy, které zpracovávají obrázky a vrací informace. Počítačové zpracování obrazu algoritmy analyzují obsah obrázku různými způsoby v závislosti na vizuálních funkcích, které vás zajímají.

* [Analýza obrázku](#analyze-an-image)
* [Získat seznam domén předmětu](#get-subject-domain-list)
* [Analyzovat obrázek podle domény](#analyze-an-image-by-domain)
* [Získání textu s popisem obrázku](#get-text-description-of-an-image)
* [Získat rukou psaný text z obrázku](#get-text-from-image)
* [Generovat miniaturu](#generate-thumbnail)

Další informace o této službě najdete v tématu [co je počítačové zpracování obrazu?][computervision_docs].

Hledáte další dokumentaci?

* [Referenční dokumentace k sadě SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Dokumentace k Cognitive Services Počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Požadavky

* [Python 3.6 +][python]
* Bezplatný [počítačové zpracování obrazu klíč][computervision_resource] a přidružený koncový bod Tyto hodnoty budete potřebovat při vytváření instance objektu klienta [ComputerVisionClient][ref_computervisionclient] . K získání těchto hodnot použijte jednu z následujících metod.

### <a name="if-you-dont-have-an-azure-subscription"></a>Pokud nemáte předplatné Azure

Vytvořte si bezplatný klíč platný po dobu 7 dní s možností **[vyzkoušet si ho][computervision_resource]** pro službu počítačové zpracování obrazu. Po vytvoření klíče Zkopírujte klíč a název koncového bodu. Budete ho potřebovat k [Vytvoření klienta](#create-client).

Po vytvoření klíče nechejte následující:

* Hodnota klíče: řetězec znaků 32 ve formátu`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Klíčový koncový bod: adresa URL základního koncového\:bodu, https//westcentralus.API.Cognitive.Microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Pokud máte předplatné Azure

Nejjednodušší způsob, jak vytvořit prostředek v rámci vašeho předplatného, je použít následující příkaz rozhraní příkazového [řádku Azure CLI][azure_cli] . Tím se vytvoří klíč služby pro rozpoznávání, který se dá použít v mnoha službách pro rozpoznávání. Musíte zvolit _existující_ název skupiny prostředků, například "My-cogserv-Group" a nový název prostředku počítačové vize, například "My-Computer-Vision-Resource".

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

Nainstalujte [balíček][pypi_computervision] Azure COGNITIVE Services počítačové zpracování obrazu SDK pro Python s [PIP][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Ověřování

Jakmile vytvoříte prostředek Počítačové zpracování obrazu, budete potřebovat jeho **koncový bod**a jeden z jeho **klíčů účtu** pro vytvoření instance objektu klienta.

Tyto hodnoty použijte při vytváření instance objektu klienta [ComputerVisionClient][ref_computervisionclient] .

Například použijte terminál bash k nastavení proměnných prostředí:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Pro uživatele předplatného Azure Získejte přihlašovací údaje pro klíč a koncový bod.

Pokud si nepamatujete koncový bod a klíč, můžete je najít pomocí následujícího postupu. Pokud potřebujete vytvořit klíč a koncový bod, můžete použít metodu pro [držitele předplatného Azure](#if-you-have-an-azure-subscription) nebo pro [uživatele bez předplatného Azure](#if-you-dont-have-an-azure-subscription).

Pomocí níže uvedeného fragmentu [Azure CLI][cloud_shell] naplňte dvě proměnné prostředí pomocí **koncového bodu** účtu počítačové zpracování obrazu a některého z jeho **klíčů** (tyto hodnoty můžete v [Azure Portal][azure_portal]taky najít). Fragment kódu je naformátován pro prostředí bash.

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


### <a name="create-client"></a>Vytvořit klienta

Získejte koncový bod a klíč z proměnných prostředí a pak vytvořte objekt klienta [ComputerVisionClient][ref_computervisionclient] .

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

Abyste mohli použít některé z následujících úloh, potřebujete objekt klienta [ComputerVisionClient][ref_computervisionclient] .

### <a name="analyze-an-image"></a>Analýza obrázku

Pomocí [`analyze_image`][ref_computervisionclient_analyze_image]nástroje můžete analyzovat obrázek pro určité funkce. [`visual_features`][ref_computervision_model_visualfeatures] Pomocí vlastnosti nastavte typy analýzy, které mají být provedeny na obrázku. Společné hodnoty jsou `VisualFeatureTypes.tags` a `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Získat seznam domén předmětu

Zkontrolujte domény předmětu používané k analýze vaší image pomocí [`list_models`][ref_computervisionclient_list_models]nástroje. Tyto názvy domén se používají při [analýze image podle domény](#analyze-an-image-by-domain). Příkladem domény je `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analyzovat obrázek podle domény

Můžete analyzovat obrázek podle domény předmětu pomocí [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Získejte [seznam podporovaných domén předmětu](#get-subject-domain-list) , aby bylo možné použít správný název domény.

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

### <a name="get-text-description-of-an-image"></a>Získání textu s popisem obrázku

Můžete získat textový popis obrázku s [`describe_image`][ref_computervisionclient_describe_image]použitím jazyka. Pokud provádíte analýzu textu `max_description` pro klíčová slova přidružená k imagi, vyžádejte si několik popisů s vlastností. Příklady textového popisu pro následující obrázek zahrnují `a train crossing a bridge over a body of water`, `a large bridge over a body of water`a `a train crossing a bridge over a large body of water`.

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

### <a name="get-text-from-image"></a>Získat text z obrázku

Z obrázku můžete získat libovolný Rukopisný nebo vytištěný text. To vyžaduje dvě volání sady SDK: [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) a. [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) Volání `batch_read_file` je asynchronní. Ve výsledcích `get_read_operation_result` volání je nutné [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] před extrahováním textových dat ověřit, zda bylo první volání dokončeno. Výsledky obsahují text i souřadnice ohraničujícího pole pro text.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

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

### <a name="generate-thumbnail"></a>Generovat miniaturu

Pomocí [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]můžete vygenerovat miniaturu (jpg) obrázku. Miniatura nemusí být ve stejných poměrech jako původní obrázek.

Nainstalujte **Pillow** pro použití v tomto příkladu:

```bash
pip install Pillow
```

Po instalaci Pillow použijte balíček v následujícím příkladu kódu pro vygenerování miniatury.

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

Když pracujete s objektem klienta [ComputerVisionClient][ref_computervisionclient] pomocí sady Python SDK, [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] třída se používá k vrácení chyb. Chyby vracené službou odpovídají stejným stavovým kódům HTTP vráceným pro REST API požadavky.

Například pokud se pokusíte analyzovat obrázek s neplatným klíčem, `401` vrátí se chyba. V následujícím fragmentu kódu je [Chyba][ref_httpfailure] zpracována řádným zachycením výjimky a zobrazením dalších informací o chybě.

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

Při práci s klientem [ComputerVisionClient][ref_computervisionclient] může dojít k přechodným chybám způsobeným [omezeními přenosů][computervision_request_units] , které vynutila služba, nebo jinými přechodnými problémy, jako jsou výpadky sítě. Informace o zpracování těchto typů selhání najdete v tématu [vzor opakování][azure_pattern_retry] v průvodci vzory návrhu cloudu a v příslušném vzoru pro [přerušení okruhů][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Používání značek obsahu pro obrázky](../concept-tagging-images.md)

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
