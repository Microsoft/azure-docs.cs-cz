---
title: Rychlý Start pro lineární klienta Pythonu pro Python detektory
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 684c61dfb34d55681904943160ca389c19a4c8db
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732644"
---
Začněte s klientskou knihovnou anomálií lineární pro Python. Pomocí těchto kroků nainstalujete balíček Start s použitím algoritmů poskytovaných službou. Nová rozhraní API pro detekci anomálií lineární umožňují vývojářům snadnou integraci pokročilého AI pro detekci anomálií ze skupin metrik, aniž by to mělo znalosti strojového učení nebo data s popisky. Závislosti a vzájemné korelace mezi různými signály se automaticky počítají jako klíčové faktory. To vám pomůže aktivně chránit komplexní systémy před chybami.

Použijte klientskou knihovnu anomálie lineární pro Python k těmto akcím:

* Zjišťuje anomálie na úrovni systému ze skupiny časových řad.
* Když kterákoli z jednotlivých časových řad neřekne hodně a musíte se podívat na všechny signály a zjistit problém.
* Predicative údržbu drahých fyzických prostředků s desítkou až stovkami různých typů senzorů, které měří různé aspekty stavu systému.

[Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Balíček (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)

## <a name="prerequisites"></a>Požadavky

* [Python 3.x](https://www.python.org/)
* [Knihovna analýzy dat PANDAS](https://pandas.pydata.org/)
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" vytvořte prostředek pro detekci anomálií "  target="_blank"> vytvořením prostředku detektoru anomálií </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Počkejte na nasazení a klikněte na tlačítko **Přejít k prostředku** .
    * K připojení aplikace k rozhraní API detektoru anomálií budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.


## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

 Vytvořte nový soubor Pythonu a importujte následující knihovny.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Vytvořte proměnné pro klíč jako proměnnou prostředí, cestu k datovému souboru časové řady a umístění Azure vašeho předplatného. Například, `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce pomocí klientské knihovny pro detekci anomálií pro Python:

* [Ověření klienta](#authenticate-the-client)
* [Trénování modelu](#train-the-model)
* [Detekovat anomálie](#detect-anomalies)
* [Exportovat model](#export-model)
* [Odstranit model](#delete-model)

## <a name="authenticate-the-client"></a>Ověření klienta

K vytvoření instance nového klienta detektoru anomálií musíte předat klíč předplatného detektoru anomálií a přidružený koncový bod. Vytvoříme také zdroj dat.  

Aby bylo možné používat rozhraní API pro detekci anomálií lineární, musíme před použitím detekce vyškolit náš vlastní model. Data použitá pro školení jsou dávkou časových řad, přičemž každá časová řada by měla být ve formátu CSV se dvěma sloupci, časovým razítkem a hodnotou. Všechny časové řady by měly být komprimované do jednoho souboru zip a nahrály do [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Ve výchozím nastavení se název souboru použije k reprezentaci proměnné pro časovou řadu. Alternativně je možné do souboru ZIP zahrnout další meta.js, pokud chcete, aby název proměnné byl jiný než název souboru. zip. Po vygenerování [adresy URL (SAS) objektu BLOB (Shared Access signatury)](../../../../storage/common/storage-sas-overview.md)můžeme pro školení použít adresu URL souboru ZIP.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Trénování modelu

Nejprve provedeme model, provedeme kontrolu stavu modelu během školení, abyste zjistili, kdy se školení dokončilo, a potom načtete nejnovější ID modelu, které budeme potřebovat při přesunu do fáze zjišťování.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Detekovat anomálie

Pomocí `detect_anomaly` a `get_dectection_result` Určete, jestli ve zdroji dat nejsou nějaké anomálie. Pro model, který jste právě proučeni, budete muset předat ID modelu.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Exportovat model

Pokud chcete exportovat použití modelu `export_model` a předat ID modelu modelu, který chcete exportovat:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Odstranit model

Chcete-li odstranit model použití `delete_multivariate_model` a předat ID modelu modelu, který chcete odstranit:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Spuštění aplikace

Před spuštěním aplikace potřebujeme přidat nějaký kód pro volání našich nově vytvořených funkcí.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Spusťte aplikaci pomocí `python` příkazu a názvu souboru.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
