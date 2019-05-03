---
title: Zabezpečení webových služeb pomocí SSL
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak zabezpečit webové služby nasazené ve službě Azure Machine Learning povolením protokolu HTTPS. HTTPS zabezpečuje data odeslaná klienty, kteří používají zabezpečení transportní vrstvy (TLS), můžou nahradit aktuální soubor vrstvy zabezpečení soketu (SSL). Je také používají klienti k ověření identity webové služby.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 50e42172af6ca6b966f9f60d3e037f9ae3dc5cbe
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023768"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Použití protokolu SSL pro zabezpečení webové služby pomocí služby Azure Machine Learning

V tomto článku se dozvíte, jak zabezpečit webovou službu nasazenou ve službě Azure Machine Learning. Můžete omezit přístup k webovým službám a zabezpečit data odeslaná klienty, kteří používají [protokol zabezpečení HTTPS (Hypertext Transfer)](https://en.wikipedia.org/wiki/HTTPS).

Protokol HTTPS se používá k zabezpečení komunikace mezi klientem a webovou službu pomocí šifrování komunikace mezi nimi. Šifrování se určují pomocí šablon [zabezpečení TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS je stále někdy se jako vrstva SSL (Secure Sockets), který byl předchůdce protokol TLS.

> [!TIP]
> Sada SDK Azure Machine Learning používá termín "protokol SSL' pro vlastnosti týkající se povolení zabezpečené komunikace. To neznamená, že webová služba nepoužívá protokol TLS, stačí tento protokol SSL je více rozpoznatelných termín pro mnoho čtenáři.

TLS a SSL oba spoléhají na __digitální certifikáty__, které jsou používány k provádění ověření šifrování a identity. Další informace o práci způsob, jakým digitální certifikáty, naleznete v příspěvku Wikipedia na [infrastruktury veřejných klíčů (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Pokud nemáte povolení a používání protokolu HTTPS pro webovou službu, může být k ostatním na Internetu vidět data odesílaná do a ze služby.
>
> HTTPS také umožňuje klientovi k ověření pravosti, která se připojuje k serveru. Tím se zajistí ochrana klientů proti [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) útoky.

Obecný postup zabezpečení nové webové služby nebo některý z existujících vypadá takto:

1. Získáte název domény.

2. Získáte digitální certifikát.

3. Nasazení nebo aktualizovat webovou službu s povoleným nastavením SSL.

4. Aktualizujte svoji službu DNS tak, aby odkazoval na webovou službu.

> [!IMPORTANT]
> Pokud provádíte nasazení do Azure Kubernetes Service (AKS), můžete poskytnout vlastní certifikát nebo použít certifikát od Microsoftu. Pokud používáte certifikát dodaný společností Microsoft, není potřeba získat název domény nebo certifikát SSL. Další informace najdete v tématu [povolit SSL a nasadit](#enable) oddílu.

Existují mírné rozdíly při zabezpečování webových služeb napříč [cíle nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud jste již nevlastní název domény, můžete si zakoupit jeden z __registrátora názvu domény__. Proces se liší mezi registrátorů, stejně jako náklady. Doménový Registrátor také poskytuje nástroje pro správu název domény. Tyto nástroje se používají k mapování plně kvalifikovaný název domény (jako je například www\.contoso.com) na IP adresu, který je hostitelem webové služby.

## <a name="get-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL (certifikát). Nejběžnější je to k nákupu z __certifikační autority__ (CA). Bez ohledu na to, kde můžete získat certifikát budete potřebovat následující soubory:

* A __certifikát__. Certifikát musí obsahovat řetězce úplný certifikát a musí být kódovaný PEM.
* A __klíč__. Klíč musí být kódovaný PEM.

Při žádosti o certifikát, musíte plně kvalifikovaný název domény (FQDN) adresy, které chcete použít pro webovou službu. Například www\.contoso.com. Porovnání adres razítkem do certifikát a adresu použitou klienty při ověřování identity webové služby. Pokud adresy se neshodují, klienti obdrží chybu.

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako kódovaný PEM soubory, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) změny formátu.

> [!WARNING]
> Certifikáty podepsané svým držitelem by měla sloužit pouze pro vývoj. Není vhodné používat v produkčním prostředí. Certifikáty podepsané svým držitelem může způsobovat problémy v klientovi aplikace. Další informace naleznete v dokumentaci pro knihovny sítě použité v klientské aplikaci.

## <a id="enable"></a> Povolení protokolu SSL a nasazení

Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte `ssl_enabled` parametr `True`, bez ohledu na to použít. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru.

+ **Vizuální rozhraní – vytvoření zabezpečeného Azure Kubernetes Service (AKS) pro nasazení** 
    
    Odkazovat na to, pokud se pokoušíte vytvořit zabezpečené nasazování výpočetní prostředky pro vizuální rozhraní. Při zřizování clusteru AKS, zadejte hodnoty pro parametry související s protokolem SSL pak vytvořte nové AKS.  Podrobnosti najdete na následujících fragment kódu:
    

    > [!TIP]
    >  Pokud nejste obeznámeni s využitím Python SDK, spusťte z [Přehled služby Azure Machine Learning Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Provide SSL-related parameters when provisioning the AKS cluster
    prov_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")   
 
    aks_name = 'secure-aks'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws,
                                        name = aks_name,
                                        provisioning_configuration = prov_config)
    
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```
    
   

+ **Nasazení ve službě Azure Kubernetes Service (AKS) a FPGA**

  Při nasazení do AKS, můžete vytvořit nový cluster AKS nebo připojení existující. Vytváří se nový cluster používá [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) při připojování k existujícímu clusteru používá [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Oba vracejí objekt konfigurace, který má `enable_ssl` metody.

  `enable_ssl` Metodu můžete použít buď certifikát poskytovány společností Microsoft nebo ten, který zadáte.

  * Při použití certifikátu __poskytovaných microsoftem__, je nutné použít `leaf_domain_label` parametru. Pomocí tohoto parametru se vytvoření služby pomocí certifikátu od Microsoftu. `leaf_domain_label` Slouží ke generování názvu DNS pro danou službu. Například hodnota `myservice` vytvoří název domény `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, kde `<azureregion>` je oblast, která obsahuje službu. Pokud chcete, můžete použít `overwrite_existing_domain` parametr přepsat existující popisek domény typu list.

    Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte `ssl_enabled` parametr `True`, bez ohledu na to použít. Nastavte `ssl_certificate` parametr na hodnotu __certifikát__ souboru a `ssl_key` na hodnotu __klíč__ souboru.

    > [!IMPORTANT]
    > Pokud používáte certifikát od Microsoftu, není potřeba koupit vlastní certifikát nebo názvu domény.

    Následující příklad ukazuje, jak vytvořit konfigurace, které umožňují certifikát SSL, který je vytvořen microsoftem:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name).enable_ssl(leaf_domain_label = "myservice")
    ```

  * Při použití __certifikát, který jste si koupili__, použijte `ssl_cert_pem_file`, `ssl_key_pem_file`, a `ssl_cname` parametry.  Následující příklad ukazuje, jak vytvořit konfigurace, které používají certifikát SSL je zadat pomocí `.pem` soubory:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name).enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Další informace o `enable_ssl`, naleznete v tématu [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) a [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Nasazení do Azure Container Instances (ACI)**

  Při nasazování do služby ACI, zadejte hodnoty pro parametry související s protokolem SSL, jak znázorňuje fragment kódu:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Další informace najdete v tématu [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Aktualizujte svoji službu DNS

V dalším kroku je nutné aktualizovat DNS tak, aby odkazoval na webovou službu.

+ **Pro ACI**:

  Použijte nástroje poskytované systémem registrátora názvu domény aktualizovat záznam DNS pro název domény. Záznam musí odkazovat na IP adresu služby.

  V závislosti na doménový Registrátor a čas live (TTL) nakonfigurovaný pro název domény, může trvat několik minut až několik hodin, než klienti mohli přeložit název domény.

+ **Pro AKS**:

  > [!WARNING]
  > Pokud jste použili `leaf_domain_label` k vytvoření služby pomocí certifikátu poskytovaných microsoftem, neaktualizují ručně hodnotu DNS pro cluster. Hodnota by měla být nastaví automaticky.

  Aktualizace DNS na kartě "Konfigurace" z "Veřejné IP adresy" clusteru AKS, jak je znázorněno na obrázku. Veřejnou IP adresu najdete jako jeden z typů prostředků vytvořené v rámci skupiny prostředků, která obsahuje agentské uzly AKS a jiných síťových prostředků.

  ![Služba Azure Machine Learning: Zabezpečení webové služby s protokolem SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Další postup
Naučte se:
+ [Využívání služby machine learning model nasadit jako webovou službu](how-to-consume-web-service.md)
+ [Bezpečné spuštění experimentů a odvozování uvnitř virtuální sítě Azure](how-to-enable-virtual-network.md)

