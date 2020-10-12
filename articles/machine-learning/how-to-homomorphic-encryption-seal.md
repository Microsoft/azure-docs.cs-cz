---
title: Nasazení šifrované služby Inferencing (Preview)
titleSuffix: Azure Machine Learning
description: Naučte se používat Microsoft SEAL k nasazení šifrované služby předpovědi pro klasifikaci imagí.
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b241220168a5cd81ce23be729944df387e87e567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844538"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Nasazení šifrované webové služby Inferencing (Preview)

Naučte se nasadit model klasifikace imagí jako šifrovanou webovou službu Inferencing v [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Webová služba je image kontejneru Docker, která obsahuje logiku modelu a bodování.

V této příručce použijete službu Azure Machine Learning k těmto akcím:

> [!div class="checklist"]
> * Konfigurace prostředí
> * Nasazení šifrované webové služby Inferencing
> * Příprava testovacích dat
> * Nastavit šifrované předpovědi
> * Vyčištění prostředků

ACI je skvělé řešení pro testování a porozumění pracovnímu postupu nasazení modelu. Pro škálovatelná produkční nasazení zvažte použití Azure Kubernetes Service. Další informace naleznete v tématu [Jak nasadit a kde](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

Metoda šifrování použitá v této ukázce je [homomorphic šifrování](https://github.com/Microsoft/SEAL#homomorphic-encryption). Homomorphic šifrování umožňuje provádět výpočty na šifrovaných datech bez nutnosti přístupu ke tajnému (dešifrovacímu) klíči. Výsledky výpočtů jsou zašifrované a dají se odhalet jenom vlastníkem tajného klíče. 

## <a name="prerequisites"></a>Požadavky

V tomto průvodci se předpokládá, že máte v Azure Machine Learning zaregistrovaný model klasifikace imagí. Pokud ne, zaregistrujte model pomocí předvedeného [modelu](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) nebo si vytvořte vlastní tím, že zadáte [model klasifikace obrázků pomocí Azure Machine Learning kurzu](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Konfigurace místního prostředí

V poznámkovém bloku Jupyter

1. Importujte balíčky Pythonu potřebné pro tuto ukázku.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Nainstalujte knihovnu šifrování homomorphic pro zabezpečený Inferencing.

    > [!NOTE]
    > `encrypted-inference`Balíček je momentálně ve verzi Preview.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) je knihovna, která obsahuje vazby pro šifrované Inferencing založené na [Microsoft Seal](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Konfigurace prostředí Inferencing

Vytvořte prostředí pro Inferencing a přidejte `encrypted-inference` balíček jako závislost conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Nasazení šifrované webové služby Inferencing

Model nasaďte jako webovou službu hostovanou v ACI.

K vytvoření správného prostředí pro ACI je potřeba zadat tyto položky:

* Hodnoticí skript, který ukáže, jak se daný model používá
* Konfigurační soubor pro sestavení ACI
* Školený model

### <a name="create-scoring-script"></a>Vytvoření hodnoticího skriptu

Vytvořte hodnoticí skript `score.py` používaný webovou službou pro Inferencing.

Do hodnoticího skriptu je nutné zahrnout dvě povinné funkce:

* Funkce `init()`, která obvykle načítá daný model do globálního objektu. Tato funkce se spustí jenom jednou, a to při spuštění kontejneru Dockeru.
* Funkce `run(input_data)` používá daný model k předpovědi hodnoty na základě vstupních dat. Vstupy a výstupy spuštění obvykle pro serializaci a deserializaci používají JSON, ale podporují se i další formáty. Funkce načte veřejné klíče založené na šifrování homomorphic, které jsou odeslány volajícím služby.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Vytvoření konfiguračního souboru

Vytvořte konfigurační soubor nasazení a zadejte počet procesorů a velikost paměti RAM v GB, které vyžaduje kontejner ACI. Tyto hodnoty jsou závislé na příslušném modelu, ale pro mnoho modelů obvykle postačují výchozí hodnoty 1 jádro a 1 GB RAM. Pokud později zjistíte, že je potřeba tyto hodnoty navýšit, bude nutné znovu vytvořit danou image a znovu nasadit danou službu.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Nasazení do Azure Container Instances

Odhadovaný čas dokončení: **přibližně 2-5 minut**

Nakonfigurujte image a nasaďte ji. Následující kód provede tyto kroky:

1. Vytvoření objektu prostředí obsahujícího závislosti, které model potřebuje, pomocí souboru prostředí ( `myenv.yml` )
1. Vytvořte odvozenou konfiguraci nezbytnou k nasazení modelu jako webové služby pomocí:
   * Soubor vyhodnocení (`score.py`)
   * Objekt prostředí vytvořený v předchozím kroku
1. Nasaďte model do kontejneru ACI.
1. Získání koncového bodu HTTP webové služby

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Získejte koncový bod HTTP hodnoticí webové služby, který přijímá volání klienta REST. Tento koncový bod lze sdílet s kýmkoli, kdo chce danou webovou službu otestovat nebo ji integrovat do aplikace.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Příprava testovacích dat

1. Stáhněte si testovací data. V tomto případě je uložen do adresáře s názvem *data*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Načtěte testovací data z *datového* adresáře.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Nastavit šifrované předpovědi

K získání předpovědi použijte testovací datovou sadu s modelem.

Postup při provádění šifrovaných předpovědi:

1. Vytvořte nového `EILinearRegressionClient` , klienta založeného na šifrování homomorphic a veřejné klíče.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Nahrajte veřejné klíče vygenerované šifrováním homomorphic do výchozího úložiště objektů BLOB v pracovním prostoru. To vám umožní sdílet klíče s odvozeným serverem.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Zašifrujte testovací data.

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. `run`K vyvolání služby použijte rozhraní API sady SDK a poskytněte testovací datovou sadu modelu, aby bylo možné získat předpovědi. Budeme muset poslat připojovací řetězec do úložiště objektů blob, ve kterém se nahrály veřejné klíče.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Dešifrujte výsledky pomocí klienta.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte webovou službu vytvořenou v této ukázce:

```python
service.delete()
```

Pokud už neplánujete používat prostředky Azure, které jste vytvořili, odstraňte je. Tím zabráníte tomu, aby se vám účtují nevyužité prostředky, které jsou pořád spuštěné. V této příručce se dozvíte, jak [vyčistit prostředky](how-to-manage-workspace.md#clean-up-resources) , abyste se dozvěděli víc.
