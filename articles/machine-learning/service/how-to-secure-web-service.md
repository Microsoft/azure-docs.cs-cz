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
ms.openlocfilehash: ec7b956f080837b297bac56e6237ac0672601ce7
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344480"
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

> [!WARNING]
> Certifikáty podepsané svým držitelem by měla sloužit pouze pro vývoj. Není vhodné používat v produkčním prostředí. Certifikáty podepsané svým držitelem může způsobovat problémy v klientovi aplikace. Další informace naleznete v dokumentaci pro knihovny sítě použité v klientské aplikaci.

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

+ **Nasazení na pole Programmable Gate Array (FPGA)**

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

  ![Služba Azure Machine Learning: zabezpečení webové služby s protokolem SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)Self –

## <a name="next-steps"></a>Další postup

Zjistěte, jak [využívání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md).