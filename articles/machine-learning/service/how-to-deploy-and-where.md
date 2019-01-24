---
title: Nasazení modelů jako webové služby
titleSuffix: Azure Machine Learning service
description: 'Zjistěte, jak a kde k nasazení vašich modelů služby Azure Machine Learning, včetně: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge a Field-programmable gate Array.'
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 4d62885743a4e50ece1c032c7b3405d8766d95cd
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850582"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Nasazujte modely pomocí služby Azure Machine Learning

Služby Azure Machine Learning poskytuje několik způsobů, jak můžete nasadit trénovaného modelu pomocí sady SDK. V tomto dokumentu zjistěte, jak model nasadit jako webovou službu v cloudu Azure nebo na hraničních zařízeních IoT.

> [!IMPORTANT]
> Nasazení modelu jako webové služby se momentálně nepodporuje prostředků mezi zdroji (CORS) pro sdílení obsahu.

Můžete nasadit modely do následující cílových výpočetních prostředí:

| Cílové výpočetní prostředí | Typ nasazení | Popis |
| ----- | ----- | ----- |
| [Azure Container Instances (ACI)](#aci) | Webová služba | Rychlé nasazení. Je vhodný pro vývoj a testování. |
| [Azure Kubernetes Service (AKS)](#aks) | Webová služba | Vhodné pro nasazení v produkčním prostředí vysoce škálovatelné. Nabízí automatické škálování a krátké doby odezvy. |
| [Azure IoT Edge](#iotedge) | Modul IoT | Nasaďte modely na zařízeních IoT. Odvozování se stane v zařízení. |
| [Pole programmable gate array (FPGA)](#fpga) | Webová služba | Mimořádně nízkou latenci pro odvozování v reálném čase. |

Proces nasazení modelu se podobá všech cílových výpočetních prostředí:

1. Trénování a zaregistrujte model.
1. Konfigurace a registrace bitovou kopii, která používá model.
1. Nasazení bitové kopie do cílového výpočetního prostředí.
1. Otestování nasazení

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Další informace o konceptech pracovního postupu nasazení, najdete v části [spravovat, nasazovat a monitorovat modely pomocí služby Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](http://aka.ms/AMLFree) ještě dnes.

- Pracovní prostor služby Azure Machine Learning service a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [Začínáme s Azure Machine Learning quickstart](quickstart-get-started.md).

- Trénovaného modelu. Pokud nemáte trénovaného modelu, postupujte podle kroků v [trénování modelů](tutorial-train-models-with-aml.md) kurzu pro trénování a zaregistrovat ve službě Azure Machine Learning.

    > [!NOTE]
    > Zatímco služba Azure Machine Learning můžete pracovat s obecný model, který lze načíst v Python 3, příklady v tomto dokumentu ukazují uložené ve formátu pickle modelu.
    > 
    > Další informace o použití modely ONNX, najdete v článku [ONNX a Azure Machine Learning](how-to-build-deploy-onnx.md) dokumentu.

## <a id="registermodel"></a> Registrace trénovaného modelu

Model registru je způsob, jak ukládat a uspořádat natrénované modely v cloudu Azure. Modely jsou registrované ve vašem pracovním prostoru služby Azure Machine Learning. Model lze trénuje pomocí Azure Machine Learning nebo jiné služby. K registraci modelu ze souboru, použijte následující kód:

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Časový odhad**: Přibližně 10 sekund.

Další informace najdete v tématu v referenční dokumentaci [třída modelu](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Vytvoření a registrace bitovou kopii

Nasazených modelů jsou dodávány jako obrázek. Bitová kopie obsahuje závislosti potřebné ke spuštění modelu.

Pro **Azure Container Instance**, **Azure Kubernetes Service**, a **Azure IoT Edge** nasazení, [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) třída se používá k vytvořit konfiguraci bitové kopie. Obrázek konfigurace, pak se k vytvoření nové image Dockeru. 

Následující kód ukazuje, jak vytvořit novou konfiguraci bitové kopie:

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**Časový odhad**: Přibližně 10 sekund.

Důležité parametry v tomto příkladu jsou popsané v následující tabulce:

| Parametr | Popis |
| ----- | ----- |
| `execution_script` | Určuje skript v jazyce Python, který se používá pro příjem požadavků odeslaných do služby. V tomto příkladu je součástí skriptu `score.py` souboru. Další informace najdete v tématu [provádění skriptu](#script) oddílu. |
| `runtime` | Označuje, že image používá Python. Další možností je `spark-py`, která používá Python s Apache Sparkem. |
| `conda_file` | Umožňuje poskytnout soubor prostředí conda. Tento soubor definuje prostředí conda nasazeného modelu. Další informace o vytvoření tohoto souboru najdete v tématu [vytvořit soubor prostředí (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Další informace najdete v tématu v referenční dokumentaci [ContainerImage třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)

### <a id="script"></a> Provádění skriptu

Spuštění skriptu přijímá data odeslaná do nasazené bitové kopie a předává je do modelu. Potom trvá odpovědi vrácené modelu a vrátí ji do klienta. Skript je specifický pro váš model; data, která očekává, že model a vrátí ji musíte rozumět. Skript obvykle obsahuje dvě funkce, které načíst a spustit model:

* `init()`: Tuto funkci obvykle načte modelu do globálního objektu. Tato funkce se spustí jenom jednou, a to při spuštění kontejneru Dockeru. 

* `run(input_data)`: Tato funkce využívá model k predikci hodnoty založené na vstupní data. K serializaci a rušení serializace, vstupy a výstupy spustit obvykle používají JSON. Můžete také pracovat s Nezpracovaná binární data. Můžete transformovat data, před odesláním do modelu, nebo před vrácením klientovi. 

#### <a name="working-with-json-data"></a>Práce s daty JSON

Tady je ukázkový skript, který přijímá a vrací JSON data. `run` Funkce transformuje data z JSON do formátu, očekává, že model a potom transformuje před jeho vrácením odpovědi do formátu JSON:

```python
# import things required by this script
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Práce s binárními daty

Pokud váš model přijímá __binární data__, použijte `AMLRequest`, `AMLResponse`, a `rawhttp`. Následuje příklad skriptu, který přijímá binárních dat a vrátí obrácený bajtů pro požadavky POST. Pro požadavky GET vrátí úplnou adresu URL v těle odpovědi:

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` Oboru názvů změní často, protože pracujeme na vylepšení služby. V důsledku toho cokoli, co je v tomto oboru názvů by měl být považovány za verzi preview a nejsou plně podporovány společností Microsoft.
>
> Pokud je potřeba otestovat na místním vývojovém prostředí, můžete nainstalovat komponenty `contrib` oboru názvů pomocí následujícího příkazu: 
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Registrace na obrázku

Po vytvoření bitové kopie konfigurace můžete zaregistrovat bitovou kopii. Tento image je uložen v registru kontejneru pro váš pracovní prostor. Po vytvoření můžete nasadit stejnou bitovou kopii k více službám.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Časový odhad**: Přibližně 3 minuty.

Image se systémovou správou verzí automaticky při registraci více bitových kopií se stejným názvem. Například první obrázek zaregistrovaný jako `myimage` je přiřazena ID `myimage:1`. Při příštím zaregistrujete bitovou kopii jako `myimage`, ID nové image je `myimage:2`.

Další informace najdete v tématu v referenční dokumentaci [ContainerImage třídy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Nasazení bitové kopie

Při přechodu na krok nasazení se mírně liší v závislosti na cílové výpočetní prostředí, který nasadíte do procesu. Použijte informace v následujících částech Další informace o nasazení do:

* [Azure Container Instances](#aci)
* [Služby Azure Kubernetes](#aks)
* [Project Brainwave (pole programmable gate Array)](#fpga)
* [Zařízení Azure IoT Edge](#iotedge)

> [!NOTE]
> Když **nasadit jako webovou službu**, existují tři způsoby nasazení můžete použít:
>
> | Metoda | Poznámky |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | Je potřeba zaregistrujte model a vytvořit bitovou kopii před použitím této metody. |
> | [Nasazení](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | Při použití této metody, není potřeba zaregistrujte model nebo vytvoření této image. Ale nemůžeme mít pod kontrolou název modelu nebo bitové kopie nebo přidružené značky a popisy. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | Při použití této metody, není potřeba vytvořit bitovou kopii. Ale nemáte kontrolu nad název obrázku, který je vytvořen. |
>
> V příkladech v tomto dokumentu pomocí `deploy_from_image`.

### <a id="aci"></a> Nasazení do služby Azure Container Instances

Použití Azure Container Instances pro nasazení modelů jako webové služby, pokud jeden nebo více z následujících podmínek je splněných:

- Potřebujete k rychlému nasazení a ověření modelu. Dokončení nasazení ACI za méně než 5 minut.
- Testování modelu, který je ve vývoji. Kvóty a regionální dostupnosti ACI najdete v tématu [kvóty a dostupnost oblastí pro Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) dokumentu.

Pokud chcete nasadit do služby Azure Container Instances, postupujte následovně:

1. Definujte konfiguraci nasazení. Následující příklad definuje konfiguraci, která používá jedno Procesorové jádro a 1 GB paměti:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Pokud chcete nasadit image vytvořené v [vytvoření bitové kopie](#createimage) části tohoto dokumentu, použijte následující kód:

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Časový odhad**: Přibližně 3 minuty.

    > [!TIP]
    > Pokud nejsou chyby během nasazení, použijte `service.get_logs()` k zobrazení protokolů služby. Zaznamenané informace může ukazovat na příčinu chyby.

Další informace najdete v tématu v referenční dokumentaci [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) třídy.

### <a id="aks"></a> Nasazení do služby Azure Kubernetes

Nasazení modelu jako produkční vysoce škálovatelnou webovou službu, použijte Azure Kubernetes Service (AKS). Můžete použít existující cluster AKS, nebo vytvořte novou pomocí sady SDK Azure Machine Learning, rozhraní příkazového řádku nebo na webu Azure portal.

Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Tento cluster pro více nasazení můžete znovu použít. Při odstranění clusteru, pak musíte vytvořit nový cluster, které se budete muset nasadit.

Azure Kubernetes Service poskytuje následující možnosti:

* Automatické škálování
* Protokolování
* Shromažďování dat modelů
* Rychlá doba odezvy pro webové služby

#### <a name="create-a-new-cluster"></a>Vytvoření nového clusteru

Chcete-li vytvořit nový cluster Azure Kubernetes Service, použijte následující kód:

> [!IMPORTANT]
> Vytvoření clusteru AKS je čas procesu pro váš pracovní prostor. Po vytvoření můžete znovu použít tento cluster pro více nasazení. Při odstranění clusteru nebo skupinu prostředků, který jej obsahuje, pak musíte vytvořit nový cluster, které se budete muset nasadit.
> Pro [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), pokud vyberete vlastní hodnoty agent_count a vm_size, je třeba Ujistěte se, že agent_count vynásobené vm_size je větší než nebo roven 12 virtuálních procesorů. Například pokud použijete vm_size "Standard_D3_v2", který má 4 virtuální procesory, pak měli byste vybrat agent_count 3 nebo vyšší.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Časový odhad**: Přibližně 20 minut.

#### <a name="use-an-existing-cluster"></a>Použít existující cluster

Pokud už máte AKS cluster ve vašem předplatném Azure, a je verze 1.11. *, ve kterém můžete nasadit svou image. Následující kód ukazuje, jak se připojit k existujícímu clusteru do pracovního prostoru:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Časový odhad**: Přibližně 3 minuty.

#### <a name="deploy-the-image"></a>Nasazení bitové kopie

K nasazení bitové kopie vytvořené v [vytvoření bitové kopie](#createimage) část tohoto dokumentu na Azure Kubernetes Server cluster pomocí následujícího kódu:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Časový odhad**: Přibližně 3 minuty.

> [!TIP]
> Pokud nejsou chyby během nasazení, použijte `service.get_logs()` k zobrazení protokolů služby. Zaznamenané informace může ukazovat na příčinu chyby.

Další informace najdete v tématu v referenční dokumentaci [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) a [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) třídy.

### <a id="fpga"></a> Nasazení do pole programmable gate Array (FPGA)

Project Brainwave umožňuje dosáhnout mimořádně nízkou latenci pro požadavky v reálném čase odvozování. Project Brainwave zrychluje neuronových sítí (DNN) nasadit na pole programmable gate Array v cloudu Azure. Nejčastěji používané dopředné jsou k dispozici jako featurizers pro přenos učení nebo přizpůsobitelná váhy Trénink probíhal svoje vlastní data.

Postup nasazení model pomocí aplikace Project Brainwave, najdete v části [nasadit do FPGA](how-to-deploy-fpga-web-service.md) dokumentu.

### <a id="iotedge"></a> Nasazení do Azure IoT Edge

Zařízení Azure IoT Edge je na Linuxu nebo zařízení se systémem Windows, který spustí modul runtime Azure IoT Edge. Modely strojového učení je možné nasadit do těchto zařízení jako moduly IoT Edge. Nasazení modelu do zařízení IoT Edge umožňuje zařízení využívat model přímo, namísto toho, aby k zasílání dat do cloudu pro zpracování. Získáte rychlejší reakční dobu a menší datové přenosy.

Moduly Azure IoT Edge se nasadí do zařízení z registru kontejneru. Po vytvoření image z modelu je uložen v registru kontejneru pro váš pracovní prostor.

#### <a name="set-up-your-environment"></a>Nastavení prostředí

* Vývojové prostředí. Další informace najdete v tématu [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.

* [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure. 

* Trénovaného modelu. Příklad toho, jak pro trénování modelu, najdete v článku [trénování modelu klasifikace obrázků s Azure Machine Learning](tutorial-train-models-with-aml.md) dokumentu. Předem natrénovaných modelů je k dispozici na [AI Toolkit pro Azure IoT Edge na Githubu úložiště](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

#### <a name="prepare-the-iot-device"></a>Připravte zařízení IoT
Musíte vytvořit služby IoT hub a registrace zařízení nebo opakovaně používat jeden s [tento skript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Uložit výsledný řetězec připojení po "cs": "{zkopírovat tento řetězec}".

Inicializovat zařízení stažením [tento skript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) do UbuntuX64 IoT hraničního uzlu nebo DSVM spuštěním následujících příkazů:

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

IoT Edge uzel je připraven přijmout připojovací řetězec služby IoT hub. Vyhledejte řádek ```device_connection_string:``` a vložte připojovací řetězec z výše mezi uvozovky.

Můžete také zjistěte, jak zaregistrovat zařízení a nainstalovat modul runtime IoT krok za krokem podle [rychlý start: Nasazení prvního modulu IoT Edge do zařízení Linux x64](../../iot-edge/quickstart-linux.md) dokumentu.


#### <a name="get-the-container-registry-credentials"></a>Získání přihlašovacích údajů registru kontejneru
Azure IoT pro nasazení modulu IoT Edge do zařízení, potřebuje přihlašovací údaje pro registr kontejneru, který ukládá Image dockeru ve službě Azure Machine Learning.

Můžete snadno načíst přihlašovací údaje registru nezbytné kontejnerové dvěma způsoby:

+ **Na webu Azure Portal**:

  1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/signin/index).

  1. Přejděte do pracovního prostoru služby Azure Machine Learning a vyberte __přehled__. Chcete-li přejít do nastavení registru kontejneru, vyberte __registru__ odkaz.

     ![Obrázek položky registru kontejneru](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Jednou v registru kontejneru, vyberte **přístupové klíče** a pak povolte uživatele s rolí správce.
 
     ![Snímek obrazovky klíčů přístup](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Uložte příslušné hodnoty pro **přihlašovací server**, **uživatelské jméno**, a **heslo**. 

+ **Pomocí skriptu v jazyce Python**:

  1. Pomocí následujícího skriptu Pythonu za kód, který jste spustili výše vytvořte kontejner:

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Uložte hodnoty ContainerURL, servername, uživatelské jméno a heslo. 

     Tyto přihlašovací údaje jsou nezbytné k zajištění na hraničních zařízeních IoT zařízení přístup k imagím v váš privátní registr kontejnerů.

#### <a name="deploy-the-model-to-the-device"></a>Model nasadit do zařízení

Můžete snadno nasadit model spuštěním [tento skript](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) a zadejte následující informace z výše uvedených kroků: registr název, uživatelské jméno, heslo, adresa url obrázku umístění, název požadovaného nasazení, název služby IoT Hub a ID zařízení, které jste vytvořili. Můžete to udělat ve virtuálním počítači pomocí následujících kroků: 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

Alternativně můžete podle kroků v [moduly nasazení Azure IoT Edge z portálu Azure portal](../../iot-edge/how-to-deploy-modules-portal.md) dokumentu k nasazení image do svého zařízení. Při konfiguraci __nastavení registru__ pro zařízení, použijte __přihlašovací server__, __uživatelské jméno__, a __heslo__ pro váš pracovní prostor registr kontejnerů.

> [!NOTE]
> Pokud nejste obeznámeni s Azure IoT, naleznete v následujících dokumentech informace pro zahájení práce se službou:
>
> * [Rychlé zprovoznění: Nasazení prvního modulu IoT Edge k Linuxovému zařízení](../../iot-edge/quickstart-linux.md)
> * [Rychlé zprovoznění: Nasazení prvního modulu IoT Edge na zařízení s Windows](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Testování nasazením webových služeb

Chcete-li otestovat nasazení webové služby, můžete použít `run` metodu objektu webové služby. V následujícím příkladu dokument JSON je nastavena na webové služby a zobrazí výsledek. Data odeslaná musí odpovídat hodnotám modelu očekává. V tomto příkladu formát dat odpovídá vstup očekává modelem cukrovka.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Webové služby je rozhraní REST API, abyste mohli vytvořit klientských aplikací v řadě programovacích jazyků. Další informace najdete v tématu [vytvořit klientskou aplikaci vykreslující](how-to-consume-web-service.md).

## <a id="update"></a> Aktualizovat webovou službu

Pokud chcete aktualizovat webovou službu, použijte `update` metody. Následující kód ukazuje, jak aktualizovat webovou službu, která používá novou bitovou kopii:

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

> [!NOTE]
> Když aktualizujete bitovou kopii, webová služba se neaktualizuje automaticky. Každá služba, kterou chcete použít novou bitovou kopii je nutné ručně aktualizovat.

Další informace najdete v tématu v referenční dokumentaci [webová služba](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) třídy.

## <a name="clean-up"></a>Vyčištění

Chcete-li odstranit nasazenou webovou službu, použijte `service.delete()`.

Chcete-li odstranit bitovou kopii, použijte `image.delete()`.

Chcete-li odstranit registrovaný model, použijte `model.delete()`.

Další informace najdete v tématu v referenční dokumentaci [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--), a [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Další postup

* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Jak spustit predikcí služby batch](how-to-run-batch-predictions.md)
* [Monitorování vašich modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md)
* [Sady SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití služby Azure Machine Learning s Azure Virtual Network](how-to-enable-virtual-network.md)
