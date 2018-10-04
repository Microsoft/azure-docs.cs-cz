---
title: Zabezpečení webových služeb Azure Machine Learning s protokolem SSL
description: Zjistěte, jak zabezpečit webovou službu nasazenou ve službě Azure Machine Learning. Můžete omezit přístup k webovým službám a zabezpečit data odeslaná klienty, kteří používají zabezpečené soketu vrstvy (SSL) a ověřování na základě klíče.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 10/02/2018
ms.openlocfilehash: 8a26491acc7215598e57ce6074fffe26a4374a96
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251012"
---
# <a name="secure-azure-machine-learning-web-services-with-ssl"></a>Zabezpečení webových služeb Azure Machine Learning s protokolem SSL

V tomto článku se dozvíte, jak zabezpečit webovou službu nasazenou ve službě Azure Machine Learning. Můžete omezit přístup k webovým službám a zabezpečit data odeslaná klienty, kteří používají zabezpečené soketu vrstvy (SSL) a ověřování na základě klíče.

> [!Warning]
> Pokud nepovolíte SSL, kdokoli na Internetu bude mít možnost provádět volání webové služby.

SSL šifruje data odesílaná mezi klientem a webovou službu. Je také používá klient ověřit identitu serveru. Ověřování je povoleno pouze pro služby, které jste zadali certifikát SSL a klíče.  Pokud povolíte protokol SSL, ověřovací klíč je povinný při přístupu k webové službě.

Nasazení webové služby povolena s protokolem SSL, nebo povolíte protokol SSL pro existující nasazenou webovou službu, postup je stejný:

1. Získáte název domény.

2. Získejte certifikát SSL.

3. Nasazení nebo aktualizovat webovou službu s povoleným nastavením SSL.

4. Aktualizujte svoji službu DNS tak, aby odkazoval na webovou službu.

Existují mírné rozdíly při zabezpečování webových služeb napříč [cíle nasazení](how-to-deploy-and-where.md). 

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud jste již nevlastní název domény, můžete si zakoupit jeden z __registrátora názvu domény__. Proces se liší mezi registrátorů, stejně jako náklady. Doménový Registrátor také poskytuje nástroje pro správu název domény. Tyto nástroje se používají k mapování plně kvalifikovaný název domény (například www.contoso.com) na IP adresu, který je hostitelem webové služby.

## <a name="get-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL. Nejběžnější je to k nákupu z __certifikační autority__ (CA). Bez ohledu na to, kde můžete získat certifikát budete potřebovat následující soubory:

* A __certifikát__. Certifikát musí obsahovat řetězce úplný certifikát a musí být kódovaný PEM.
* A __klíč__. Klíč musí být kódovaný PEM.

Při žádosti o certifikát, musíte plně kvalifikovaný název domény (FQDN) adresy, které chcete použít pro webovou službu. Příklad: www.contoso.com. Porovnání adres razítkem do certifikát a adresu použitou klienty při ověřování identity webové služby. Pokud adresy se neshodují, klienti obdrží chybu. 

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako kódovaný PEM soubory, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) změny formátu.

> [!IMPORTANT]
> Certifikáty podepsané svým držitelem by měla sloužit pouze pro vývoj. Není vhodné používat v produkčním prostředí. Pokud používáte certifikát podepsaný svým držitelem, přečtěte si [používání webových služeb s certifikáty podepsanými držitelem](#self-signed) části konkrétní pokyny.


## <a name="enable-ssl-and-deploy"></a>Povolení protokolu SSL a nasazení

Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte `ssl_enabled` parametr `True`, bez ohledu na to použít. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru. 

+ **Nasazení ve službě Azure Kubernetes Service (AKS)**
  
  Při zřizování clusteru AKS, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.core.compute import AksCompute
    
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Nasazení do Azure Container Instances (ACI)**
 
  Při nasazování do služby ACI, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.core.webservice import AciWebservice
    
    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Nasazení v rámci pole funkce gamma programovatelný polí (FPGA)**

  Odpověď `create_service` operace obsahuje IP adresu služby. IP adresa se používá při mapování názvu DNS na IP adresu služby. Také obsahuje odpovědi __primární klíč__ a __sekundární klíč__ , která se používají k používání této služby. Zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

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

## <a name="update-your-dns"></a>Aktualizujte svoji službu DNS

V dalším kroku je nutné aktualizovat DNS tak, aby odkazoval na webovou službu.

+ **ACI a FPGA**:  

  Použijte nástroje poskytované systémem registrátora názvu domény aktualizovat záznam DNS pro název domény. Záznam musí odkazovat na IP adresu služby.  

  V závislosti na doménový Registrátor a čas live (TTL) nakonfigurovaný pro název domény, může trvat několik minut až několik hodin, než klienti mohli přeložit název domény.

+ **Pro AKS**: 

  Aktualizace DNS na kartě "Konfigurace" z "Veřejné IP adresy" clusteru AKS, jak je znázorněno na obrázku. Veřejnou IP adresu najdete jako jeden z typů prostředků vytvořené v rámci skupiny prostředků, která obsahuje agentské uzly AKS a jiných síťových prostředků.

  [ ![Služba Azure Machine Learning: zabezpečení webové služby s protokolem SSL](./media/how-to-secure-web-service/aks-public-ip-address.png) ] ((.media/how-to-secure-web-service/aks-public-ip-address.png#lightbox)

## <a name="consume-authenticated-services"></a>Využívání služeb ověřený

### <a name="how-to-consume"></a>Jak využívat 
+ **ACI a AKS**: 

  Webové služby ACI a AKS Další informace o využívání webových služeb v těchto článcích:
  + [Postup nasazení do služby ACI](how-to-deploy-to-aci.md)

  + [Postup nasazení do AKS](how-to-deploy-to-aks.md)

+ **ACI a FPGA**:  

  Následující příklady ukazují, jak využívat služby ověřené FPGA na Python a C#.
  Nahraďte `authkey` s primárním nebo sekundárním klíčem, který byl vrácen, pokud byla nasazená služba.

  Příklad v Pythonu:
    ```python
    from amlrealtimeai import PredictionClient
    client = PredictionClient(service.ipAddress, service.port, use_ssl=True, access_token="authKey")
    image_file = R'C:\path_to_file\image.jpg'
    results = client.score_image(image_file)
    ```

  Příklad jazyka C#:
    ```csharp
    var client = new ScoringClient(host, 50051, useSSL, "authKey");
    float[,] result;
    using (var content = File.OpenRead(image))
        {
            IScoringRequest request = new ImageRequest(content);
            result = client.Score<float[,]>(request);
        }
    ```

### <a name="set-the-authorization-header"></a>Nastavit hlavičku autorizace
Další gRPC klienti můžou ověřovat požadavky nastavením se autorizační hlavička. Obecný postup je vytvořit `ChannelCredentials` objekt, který kombinuje `SslCredentials` s `CallCredentials`. Tím se přidá do hlavičky autorizace žádosti. Další informace o implementaci podpory pro konkrétní hlavičky najdete v tématu [ https://grpc.io/docs/guides/auth.html ](https://grpc.io/docs/guides/auth.html).

Následující příklady ukazují, jak nastavit hlavičky v C# a Go:

+ Použití jazyka C# k nastavení hlavičky:
    ```csharp
    creds = ChannelCredentials.Create(baseCreds, CallCredentials.FromInterceptor(
                          async (context, metadata) =>
                          {
                              metadata.Add(new Metadata.Entry("authorization", "authKey"));
                              await Task.CompletedTask;
                          }));
    
    ```

+ Použití jazyka Go k nastavení hlavičky:
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

<a id="self-signed"></a>

## <a name="consume-services-with-self-signed-certificates"></a>Využívání služeb s certifikáty podepsané svým držitelem

Existují dva způsoby pro umožnění spolupráce klienta k ověření serveru zabezpečený pomocí certifikátu podepsaného svým držitelem:

* V klientském systému, nastavte `GRPC_DEFAULT_SSL_ROOTS_FILE_PATH` proměnnou prostředí v klientském systému tak, aby odkazoval na soubor certifikátu.

* Při vytváření `SslCredentials` objektu, předat konstruktoru obsah souboru certifikátu.

Pomocí některé z metod způsobí, že gRPC pro použití certifikátu jako kořenového certifikátu.

> [!IMPORTANT]
> gRPC nepřijímá nedůvěryhodné certifikáty. Pomocí nedůvěryhodného certifikátu se nezdaří pomocí `Unavailable` stavový kód. Podrobnosti o tomto selhání obsahovat `Connection Failed`.
