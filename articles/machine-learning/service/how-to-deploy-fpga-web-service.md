---
title: Nasazení modelu jako webové služby na FPGA službou Azure Machine Learning
description: Zjistěte, jak nasadit webovou službu pomocí modelu běží na FPGA službou Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 09/24/2018
ms.openlocfilehash: ee67585a523ab96b1442d9eee3e9dfd55a758d32
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971480"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Nasazení modelu jako webové služby na FPGA službou Azure Machine Learning

Nasazení modelu jako webové služby na [pole programmable gate Array (FPGA)](concept-accelerate-with-fpgas.md).  Použití FPGA poskytuje mimořádně nízkou latenci odvozování, dokonce i s velikostí jedné dávce.   

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Pracovní prostor služby Azure Machine Learning a Azure Machine Learning SDK for nainstalovaný Python. Další informace o získání těchto nezbytných podmínkách používání [jak nakonfigurovat prostředí pro vývoj](how-to-configure-environment.md) dokumentu.
 
  - Musí být v pracovním prostoru *USA – východ 2* oblasti.

  - Instalace funkce contrib:

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Vytvoření a nasazení modelu
Vytvoření kanálu pro předzpracování vstupního obrázku vytrénovaných pomocí modelem ResNet 50 na FPGA a pak spusťte funkce prostřednictvím classifer školení na datové sadě ImageNet.

Postupujte podle pokynů:

* Definovat modelu kanálu
* Nasazení modelu
* Používání nasazeného modelu
* Odstranění nasazené služby

> [!IMPORTANT]
> Pokud chcete optimalizovat latenci a propustnost, musí být váš klient ve stejné oblasti Azure jako koncový bod.  Rozhraní API jsou aktuálně vytvořené v oblasti Azure USA – východ.

### <a name="get-the-notebook"></a>Získání poznámkového bloku

Pro usnadnění je k dispozici jako poznámkový blok Jupyter v tomto kurzu. Použít žádnou z těchto metod ke spuštění `project-brainwave/project-brainwave-quickstart.ipynb` Poznámkový blok:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

### <a name="preprocess-image"></a>Předběžné zpracování obrazu
První fáze kanálu je předběžné zpracování imagí.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```
### <a name="add-featurizer"></a>Přidat Featurizer
Inicializovat model a stáhněte si kontrolní bod TensorFlow kvantizované verze ResNet50 má být použit jako featurizer.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Přidat třídění
Na datové sadě ImageNet byla vyškolila tento třídění.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Vytvoření definice služby
Teď, když máte definované předběžného zpracování obrazu, featurizer a třídění, na kterém běží ve službě, můžete vytvořit definice služby. Definice služby je sada soubory vygenerované z modelu, který je nasazený do FPGA služby. Definice služby se skládá z kanálu. Kanál je několika fází, které jsou spouštěny v pořadí.  Fáze TensorFlow, Keras fáze a fáze BrainWave jsou podporovány.  Fáze jsou spuštěny v pořadí na službu s výstupem každé fáze vstup do další fáze.

Chcete-li vytvořit fázi TensorFlow, zadejte relaci obsahující grafu (v tomto případě se používá výchozí graf) a vstupní a výstupní tensors do této fáze.  Tyto informace slouží k uložení grafu tak, aby ji můžete spustit ve službě.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Nasazení modelu
Vytvoření služby z definice služby.  Váš pracovní prostor musí být v umístění východní USA 2.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Pokud chcete službu otestovat
Odeslat image do rozhraní API a otestovat odpovědi, přidejte mapování z výstupu ID třídy ImageNet název třídy.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Volají službu a název souboru "your image.jpg" níže nahraďte obrázek z vašeho počítače. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Vyčištění služby
Odstraňte službu.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Zabezpečení webových služeb, FPGA

Modely Azure Machine Learning, běží na FPGA poskytovat podpora protokolu SSL a ověřování na základě klíče. To umožňuje omezit přístup k vaší služby a zabezpečit data odeslaná klienty.

> [!IMPORTANT]
> Ověřování je povoleno pouze pro služby, které jste zadali certifikát SSL a klíče. 
>
> Pokud nepovolíte SSL, bude moct provádět volání do služby kdokoli na Internetu.
>
> Pokud povolíte protokol SSL a ověřovací klíč je požadován při přístupu ke službě.

SSL šifruje data odesílaná mezi klientem a službou. Je také používá klient ověřit identitu serveru.

Můžete nasadit službu s protokol SSL povolený nebo aktualizovat už nasazenou službu, aby je. Postup je stejný:

1. Získání názvu domény.

2. Získejte certifikát SSL.

3. Nasazení nebo službu aktualizujte protokol SSL povolený.

4. Aktualizujte svoji službu DNS tak, aby odkazovala na službu.

### <a name="acquire-a-domain-name"></a>Získání názvu domény

Pokud jste již nevlastní název domény, můžete si zakoupit jeden z __registrátora názvu domény__. Proces se liší mezi registrátorů, stejně jako náklady. Doménový Registrátor také poskytuje nástroje pro správu název domény. Tyto nástroje se používají k mapování plně kvalifikovaný název domény (například www.contoso.com) na IP adresu, která je hostitelem služby.

### <a name="acquire-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL. Nejběžnější je to k nákupu z __certifikační autority__ (CA). Bez ohledu na to, kde můžete získat certifikát budete potřebovat následující soubory:

* A __certifikát__. Certifikát musí obsahovat řetězce úplný certifikát a musí být kódovaný PEM.
* A __klíč__. Klíč musí být kódovaný PEM.

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako kódovaný PEM soubory, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) změny formátu.

> [!IMPORTANT]
> Certifikáty podepsané svým držitelem by měla sloužit pouze pro vývoj. Není vhodné používat v produkčním prostředí.
>
> Pokud používáte certifikát podepsaný svým držitelem, přečtěte si [využívání služeb pomocí certifikátů podepsaných svým držitelem](#self-signed) části konkrétní pokyny.

> [!WARNING]
> Při žádosti o certifikát, musíte plně kvalifikovaný název domény (FQDN) adresy, které chcete použít pro službu. Příklad: www.contoso.com. Porovnání adres razítkem do certifikát a adresu použitou klienty při ověřování identity služby.
>
> Pokud adresy se neshodují, klienti obdrží chybu. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Nasazením nebo aktualizací služby s protokolem SSL povoleno

Chcete-li nasadit službu s protokolem SSL povoleno, nastavte `ssl_enabled` parametr `True`. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru. Následující příklad ukazuje nasazení služby s protokolem SSL povoleno:

```python
from amlrealtimeai import DeploymentClient

subscription_id = "<Your Azure Subscription ID>"
resource_group = "<Your Azure Resource Group Name>"
model_management_account = "<Your AzureML Model Management Account Name>"
location = "eastus2"

model_name = "resnet50-model"
service_name = "quickstart-service"

deployment_client = DeploymentClient(subscription_id, resource_group, model_management_account, location)

with open('cert.pem','r') as cert_file:
    with open('key.pem','r') as key_file:
        cert = cert_file.read()
        key = key_file.read()
        service = deployment_client.create_service(service_name, model_id, ssl_enabled=True, ssl_certificate=cert, ssl_key=key)
```

Odpověď `create_service` operace obsahuje IP adresu služby. IP adresa se používá při mapování názvu DNS na IP adresu služby.

Také obsahuje odpovědi __primární klíč__ a __sekundární klíč__ , která se používají k používání této služby.

### <a name="update-your-dns-to-point-to-the-service"></a>Aktualizujte svoji službu DNS tak, aby odkazovala na službu

Použijte nástroje poskytované systémem registrátora názvu domény aktualizovat záznam DNS pro název domény. Záznam musí odkazovat na IP adresu služby.

> [!NOTE]
> V závislosti na doménový Registrátor a čas live (TTL) nakonfigurovaný pro název domény, může trvat několik minut až několik hodin, než klienti mohli přeložit název domény.

### <a name="consume-authenticated-services"></a>Využívání služeb ověřený

Následující příklady ukazují, jak využívat služby ověřené pomocí jazyka Python a C#:

> [!NOTE]
> Nahraďte `authkey` s primárním nebo sekundárním klíčem Vrácená při vytváření služby.

```python
from amlrealtimeai import PredictionClient
client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
image_file = R'C:\path_to_file\image.jpg'
results = client.score_image(image_file)
```

```csharp
var client = new ScoringClient(host, 50051, useSSL, "authKey");
float[,] result;
using (var content = File.OpenRead(image))
    {
        IScoringRequest request = new ImageRequest(content);
        result = client.Score<float[,]>(request);
    }
```

Další gRPC klienti můžou ověřovat požadavky nastavením se autorizační hlavička. Obecný postup je vytvořit `ChannelCredentials` objekt, který kombinuje `SslCredentials` s `CallCredentials`. Tím se přidá do hlavičky autorizace žádosti. Další informace o implementaci podpory pro konkrétní hlavičky najdete v tématu [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Následující příklady ukazují, jak nastavit hlavičky v C# a Go:

```csharp
creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                      async (context, metadata) =>
                      {
                          metadata.Add(new Metadata.Entry("authorization", "authKey"));
                          await Task.CompletedTask;
                      }));

```

```go
conn, err := grpc.Dial(serverAddr, 
    grpc.WithTransportCredentials(credentials.NewClientTLSFromCert(nil, "")),
    grpc.WithPerRPCCredentials(&authCreds{
    Key: "authKey"}))

type authCreds struct {
    Key string
}

func (c *authCreds) GetRequestMetadata(context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "authorization": c.Key,
    }, nil
}

func (c *authCreds) RequireTransportSecurity() bool {
    return true
}
```

### <a id="self-signed"></a>Využívání služeb pomocí certifikátů podepsaných svým držitelem

Existují dva způsoby pro umožnění spolupráce klienta k ověření serveru zabezpečený pomocí certifikátu podepsaného svým držitelem:

* V klientském systému, nastavte `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` proměnnou prostředí v klientském systému tak, aby odkazoval na soubor certifikátu.

* Při vytváření `SslCredentials` objektu, předat konstruktoru obsah souboru certifikátu.

Pomocí některé z metod způsobí, že gRPC pro použití certifikátu jako kořenového certifikátu.

> [!IMPORTANT]
> gRPC nepřijímá nedůvěryhodné certifikáty. Pomocí nedůvěryhodného certifikátu se nezdaří pomocí `Unavailable` stavový kód. Podrobnosti o tomto selhání obsahovat `Connection Failed`.
