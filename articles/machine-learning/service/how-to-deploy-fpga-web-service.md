---
title: Jak nasadit model jako webovou službu na FPGA pomocí Azure Machine Learning
description: Informace o nasazení webové služby s modelem systémem FPGA pomocí Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: f3237980a1ad1969b5cf8d42d547ddf96608dd97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789394"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Nasadit model jako webovou službu na FPGA pomocí Azure Machine Learning

V tomto dokumentu, zjistíte, jak nastavit prostředí pracovní stanice a nasadit model jako webovou službu na [pole programovatelný brány pole (FPGA)](concept-accelerate-with-fpgas.md). Webová služba používá ke spuštění modelu v FPGA Brainwave projektu.

Pomocí FPGAs poskytuje inferencing s velmi nízkou latencí, i když jedné dávkové velikost.

## <a name="create-an-azure-machine-learning-model-management-account"></a>Vytvoření účtu Azure Machine Learning Model správy

1. Přejděte na stránku vytvoření modelu správy účtu [portálu Azure](https://aka.ms/aml-create-mma).

2. Na portálu, vytvořte účet Model správy v nástroji **východní USA 2** oblast.

   ![Obrázek obrazovce vytvořit účet pro správu modelu](media/how-to-deploy-fpga-web-service/azure-portal-create-mma.PNG)

3. Zadejte název účtu Model správy, zvolte předplatné a zvolte skupinu prostředků.

   >[!IMPORTANT]
   >Pro umístění, je nutné vybrat **východní USA 2** jako oblast.  Žádné jiné oblasti jsou aktuálně k dispozici.

4. Zvolte cenovou úroveň (S1 bude stačit, ale je možné použít S2 a S3).  DevTest úroveň se nepodporuje.  

5. Klikněte na tlačítko **vyberte** potvrďte cenovou úroveň.

6. Klikněte na tlačítko **vytvořit** Management modelu ML na levé straně.

## <a name="get-model-management-account-information"></a>Získat informace o účtu Model správy

Chcete-li získat informace o účtu správy modelu (MMA), klikněte na tlačítko __účet pro správu modelu__ na portálu Azure.

Zkopírujte hodnoty z následujících položek:

+ Název modelu účet pro správu (v v levém horním rohu)
+ Název skupiny prostředků
+ ID předplatného
+ Umístění (použijte "eastus2")

![Informace o modelu správy účtu](media/how-to-deploy-fpga-web-service/azure-portal-mma-info.PNG)

## <a name="set-up-your-machine"></a>Nastavit svůj počítač

Nastavit pracovní stanice pro FPGA nasazení, použijte tyto kroky:

1. Stáhněte a nainstalujte nejnovější verzi [Git](https://git-scm.com/downloads).

2. Nainstalujte [Anaconda (Python 3.6)](https://conda.io/miniconda.html).

3. Pokud chcete stáhnout Anaconda prostředí, použijte následující příkaz z řádku Git:

    ```
    git clone https://aka.ms/aml-real-time-ai
    ```

4. Chcete-li vytvořit prostředí, otevřete **Anaconda řádku** (ne Azure Machine Learning Workbench řádku) a spusťte následující příkaz:

    > [!IMPORTANT]
    > `environment.yml` Soubor je v úložišti git klonovat v předchozím kroku. Změňte cestu podle potřeby tak, aby odkazoval na pracovní stanici do souboru.

    ```
    conda env create -f environment.yml
    ```

5. Pokud chcete aktivovat prostředí, použijte následující příkaz:

    ```
    conda activate amlrealtimeai
    ```

6. Start pro server Poznámkový blok Jupyter, použijte následující příkaz:

    ```
    jupyter notebook
    ```

    Výstup tohoto příkazu je podobná následující text:

    ```text
    Copy/paste this URL into your browser when you connect for the first time, to login with a token:
        http://localhost:8888/?token=bb2ce89cc8ae931f5df50f96e3a6badfc826ff4100e78075
    ```

    > [!TIP]
    > Zobrazí se různé token pokaždé, když spustíte příkaz.

    Pokud váš prohlížeč do poznámkového bloku Jupyter nespustí automaticky, použijte adresu URL protokolu HTTP, který je vrácen předchozím příkazem otevřete stránku.

    ![Obrázek webové stránky poznámkového bloku Jupyter](./media/how-to-deploy-fpga-web-service/jupyter-notebook.png)

## <a name="deploy-your-model"></a>Nasazení modelu

Pomocí poznámkového bloku Jupyter, otevřete `00_QuickStart.ipynb` poznámkového bloku z `notebooks/resnet50` adresáře. Postupujte podle pokynů v poznámkovém bloku na:

* Zadejte službu
* Nasazení modelu
* Využívat nasazené modelu
* Odstranění nasazené služby

> [!IMPORTANT]
> K optimalizaci latence a propustnosti, pracovní stanice by měl být ve stejné oblasti Azure jako koncový bod.  Rozhraní API jsou aktuálně vytvořené v oblasti Azure USA – východ.

## <a name="ssltls-and-authentication"></a>Ověřování a SSL/TLS

Azure Machine Learning nabízí podporu protokolu SSL a ověřování na základě klíčů. To umožňuje omezit přístup k vaší služby a zabezpečená data odeslaná klienty.

> [!NOTE]
> Postup v této části se uplatní jenom na Azure Machine Learning hardwaru Accelerated modelů. Standardní služby Azure Machine Learning, najdete v článku [postup nastavení protokolu SSL na Azure Machine Learning výpočetní](https://docs.microsoft.com/azure/machine-learning/preview/how-to-setup-ssl-on-mlc) dokumentu.

> [!IMPORTANT]
> Ověřování je povoleno pouze pro služby, které poskytovaly služby certifikát SSL a klíč. 
>
> Pokud nepovolíte SSL, bude moct provádět volání do služby všechny uživatele na Internetu.
>
> Pokud povolíte protokol SSL a ověřovací klíč je požadován při přístupu ke službě.

SSL šifruje data odesílaná mezi klientem a služby. Je také používají klienta k ověření identity serveru.

Můžete nasadit službu pomocí protokolu SSL povoleno, nebo aktualizovat již nasazenou službu povolit. Kroky jsou stejné:

1. Získejte název domény.

2. Získejte certifikát SSL.

3. Nasaďte, nebo aktualizace služby pomocí protokolu SSL povolen.

4. Aktualizujte DNS tak, aby odkazoval na službu.

### <a name="acquire-a-domain-name"></a>Získat název domény

Pokud již nevlastníte název domény, si můžete zakoupit z __registrátorem názvu domény__. Proces se liší mezi registrátorů, jak to dělá náklady. Registrátora také poskytuje nástroje pro správu názvu domény. Tyto nástroje se používají k mapování plně kvalifikovaný název domény (např. www.contoso.com) na IP adresu, který je hostitelem služby.

### <a name="acquire-an-ssl-certificate"></a>Získání certifikátu protokolu SSL

Existuje mnoho způsobů, jak získat certifikát SSL. Nejběžnější je zakoupit u __certifikační autority__ (CA). Bez ohledu na to, kde získat certifikát potřebujete následující soubory:

* A __certifikát__. Certifikát musí obsahovat řetěz certifikátů úplné a musí být kódovaný PEM.
* A __klíč__. Klíč musí být kódovaný PEM.

> [!TIP]
> Pokud certifikační autorita nelze zadat certifikát a klíč jako soubory s kódováním PEM, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) pro formátování.

> [!IMPORTANT]
> Certifikáty podepsané svým držitelem by měl použít pouze pro vývoj. Není vhodné používat v produkčním prostředí.
>
> Pokud používáte certifikát podepsaný svým držitelem, najdete v článku [využívání služeb s certifikáty podepsané svým držitelem](#self-signed) části konkrétní pokyny.

> [!WARNING]
> Při žádosti o certifikát, je nutné zadat název plně kvalifikované domény (FQDN) adresy, ke které máte v úmyslu použít pro službu. Například www.contoso.com. Při ověření identity služby jsou porovnávány adresy razítkem do certifikátu a adresy používané klienty.
>
> Pokud adresy se neshodují, klienti dojde k chybě. 

### <a name="deploy-or-update-the-service-with-ssl-enabled"></a>Nasazení nebo aktualizace služby pomocí protokolu SSL povoleno

Chcete-li nasadit službu pomocí protokolu SSL povoleno, nastavte `ssl_enabled` parametru `True`. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru. Následující příklad ukazuje, nasazení služby pomocí protokolu SSL povoleno:

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

Odpověď obsahuje také __primární klíč__ a __sekundární klíč__ používané využívat službu.

### <a name="update-your-dns-to-point-to-the-service"></a>Aktualizovat DNS tak, aby odkazoval na službu

Použijte nástroje poskytované subsystémem vaším registrátorem názvu domény k aktualizaci záznamu DNS pro název domény. Záznam musí odkazovat na IP adresu služby.

> [!NOTE]
> V závislosti na registrátora a čas live (TTL) nakonfigurovaný pro název domény, může trvat několik minut na několik hodin, než klienti mohou překlad názvu domény.

### <a name="consuming-authenticated-services"></a>Využívání služeb ověřený

Následující příklady ukazují, jak používat služby ověřené pomocí Pythonu a C#:

> [!NOTE]
> Nahraďte `authkey` s primární nebo sekundární klíč Vrácená při vytváření služby.

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

Ostatní klienty gRPC ověřit pomocí nastavení autorizační hlavičky žádosti. Je obecný přístup k vytvoření `ChannelCredentials` objekt, který kombinuje `SslCredentials` s `CallCredentials`. Tím se přidá k hlavičce autorizace požadavku. Další informace o implementaci podpory pro vaše konkrétní hlavičky najdete v tématu [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Následující příklady ukazují, jak nastavit hlavičky v C# a přejděte:

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

### <a id="self-signed"></a>Využívání služeb s certifikáty podepsané svým držitelem

Existují dva způsoby, jak povolit klientovi ověřit na serveru zabezpečen certifikát podepsaný svým držitelem:

* V klientském systému, nastavte `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` proměnné prostředí v klientském systému tak, aby odkazoval na soubor certifikátu.

* Při vytváření `SslCredentials` objektu, předejte obsah souboru certifikátu do konstruktoru.

Pomocí těchto metod způsobí, že gRPC na použití certifikátu jako kořenový certifikát.

> [!IMPORTANT]
> gRPC nebude přijetí nedůvěryhodných certifikátů. Používá nedůvěryhodný certifikát se nezdaří s `Unavailable` stavový kód. Podrobnosti o tomto selhání obsahovat `Connection Failed`.
