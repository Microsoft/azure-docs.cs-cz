---
title: Zabezpečení webových služeb pomocí protokolu SSL
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak zabezpečit webovou službu, která je nasazena prostřednictvím služby Azure Machine Learning povolením protokolu HTTPS. HTTPS zabezpečuje data z klienty pomocí zabezpečení transportní vrstvy (TLS), můžou nahradit aktuální soubor vrstvy zabezpečení soketu (SSL). Klienti dále používají protokol HTTPS k ověření identity webové služby.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393831"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Použití protokolu SSL pro zabezpečení webové služby pomocí Azure Machine Learning

V tomto článku se dozvíte, jak zabezpečit webovou službu, která je nasazena prostřednictvím služby Azure Machine Learning.

Použijete [HTTPS](https://en.wikipedia.org/wiki/HTTPS) omezit přístup k webovým službám a zabezpečit data, která odešlete klientů. HTTPS pomáhá zabezpečenou komunikaci mezi klientem a webovou službu pomocí šifrování komunikace mezi nimi. Šifrování používá [zabezpečení TLS (Transport Layer)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS je stále někdy označovány jako *Secure Sockets Layer* (SSL), který byl předchůdce TLS.

> [!TIP]
> Sada SDK Azure Machine Learning používá termín "Šifrování SSL" pro vlastnosti, které se vztahují k zabezpečení komunikace. To neznamená, že webová služba nepoužívá *TLS*. SSL je právě běžně rozpoznaný termín.

TLS a SSL oba spoléhají na *digitální certifikáty*, které pomáhají s ověřováním šifrování a identity. Další informace o práci způsob, jakým digitální certifikáty, naleznete v tématu Wikipedia [infrastruktury veřejných klíčů](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Pokud nepoužíváte protokol HTTPS pro webovou službu, může být viditelné pro ostatní uživatele na Internetu data odesílaná do a ze služby.
>
> HTTPS také umožňuje klientovi k ověření pravosti, která se připojuje k serveru. Tato funkce chrání klienty před [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) útoky.

Toto je obecný postup zabezpečení webové služby:

1. Získáte název domény.

2. Získáte digitální certifikát.

3. Nasazení nebo aktualizovat webovou službu s protokol SSL povolený.

4. Aktualizujte svoji službu DNS tak, aby odkazoval na webovou službu.

> [!IMPORTANT]
> Pokud nasazujete do Azure Kubernetes Service (AKS), můžete koupit vlastní certifikát nebo použít certifikát, který je k dispozici společností Microsoft. Pokud používáte certifikát od společnosti Microsoft, není nutné získat název domény nebo certifikát SSL. Další informace najdete v tématu [povolit SSL a nasadit](#enable) části tohoto článku.

Existují mírné rozdíly při zabezpečování webových služeb napříč [cíle nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud již není vlastní název domény, zakoupit jeden z *registrátora názvu domény*. Proces a ceny se liší mezi registrátorů. Doménový Registrátor poskytuje nástroje ke správě názvu domény. Pomocí těchto nástrojů můžete namapovat plně kvalifikovaný název domény (FQDN) (jako je například www\.contoso.com) na IP adresu, který je hostitelem webové služby.

## <a name="get-an-ssl-certificate"></a>Získat certifikát SSL

Existuje mnoho způsobů, jak získat certifikát SSL (certifikát). Nejběžnější je to k nákupu z *certifikační autorita* (CA). Bez ohledu na to, kde můžete získat certifikát budete potřebovat následující soubory:

* A **certifikát**. Certifikát musí obsahovat řetězce úplný certifikát, a musí být "Kódovaný PEM."
* A **klíč**. Klíč musí být také kódovaný PEM.

Když požádáte o certifikát, musíte zadat plně kvalifikovaný název domény adresy, který chcete použít pro webovou službu (například www\.contoso.com). K ověření identity webové služby jsou porovnány adresu, která má na certifikát a adresu klienti používat. Pokud se tyto adresy se neshodují, klient získá chybovou zprávu.

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako kódovaný PEM soubory, můžete použít nástroj, jako [OpenSSL](https://www.openssl.org/) změny formátu.

> [!WARNING]
> Použití *podepsaný držitelem* certifikáty jenom pro vývoj. Nepoužívejte je v produkčním prostředí. Certifikáty podepsané svým držitelem může způsobovat problémy v klientovi aplikace. Další informace naleznete v dokumentaci pro knihovny sítě, které používá vaše klientská aplikace.

## <a id="enable"></a> Povolení protokolu SSL a nasazení

Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte *ssl_enabled* parametr "PRAVDA", bez ohledu na to se vztahuje. Nastavte *ssl_certificate* parametr na hodnotu *certifikát* souboru. Nastavte *ssl_key* na hodnotu *klíč* souboru.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Nasazení do AKS a pole programmable gate array (FPGA)

  > [!NOTE]
  > Informace v této části se vztahují také při nasazení zabezpečené webové služby pro vizuální rozhraní. Pokud nejste obeznámeni s pomocí sady Python SDK, přečtěte si téma [co je Azure Machine Learning SDK pro Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Při nasazení do AKS, můžete vytvořit nový cluster AKS nebo připojení existující.
  
-  Pokud vytvoříte nový cluster, můžete použít  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** .
- Pokud připojíte existující cluster, můžete použít  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** . Oba vracejí objekt konfigurace, který má **enable_ssl** metody.

**Enable_ssl** metodu můžete použít certifikát, který je k dispozici společností Microsoft nebo certifikát, který si zakoupíte.

  * Pokud používáte certifikát od společnosti Microsoft, je nutné použít *leaf_domain_label* parametru. Tento parametr generuje názvu DNS pro danou službu. Například hodnota "Moje_služba" vytvoří název domény "Moje_služba\<šest náhodných znaků >.\< oblast_azure >. cloudapp.azure.com ", kde \<oblast_azure > je oblast, která obsahuje službu. Pokud chcete, můžete použít *overwrite_existing_domain* parametr přepsat existující *leaf_domain_label*.

    Chcete-li nasadit (nebo znovu nasadit) na službu s protokol SSL povolený, nastavte *ssl_enabled* parametr "PRAVDA", bez ohledu na to se vztahuje. Nastavte *ssl_certificate* parametr na hodnotu *certifikát* souboru. Nastavte *ssl_key* na hodnotu *klíč* souboru.

    > [!IMPORTANT]
    > Pokud používáte certifikát od společnosti Microsoft, nemusíte zakoupit vlastní certifikát nebo názvu domény.

    Následující příklad ukazuje, jak vytvořit konfiguraci, která umožňuje certifikát SSL od společnosti Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Při použití *certifikát, který jste si koupili*, je použít *ssl_cert_pem_file*, *ssl_key_pem_file*, a *ssl_cname* Parametry. Následující příklad ukazuje, jak používat *.pem* soubory pro vytvoření konfigurace, který používá certifikát SSL, který jste si koupili:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Další informace o *enable_ssl*, naleznete v tématu [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) a [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Nasazení do Azure Container Instances

Když nasadíte do služby Azure Container Instances, zadejte hodnoty pro parametry související s protokolem SSL, jak ukazuje následující fragment kódu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Další informace najdete v tématu [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Aktualizujte svoji službu DNS

V dalším kroku je nutné aktualizovat DNS tak, aby odkazoval na webovou službu.

+ **Pro Container Instances:**

  Použití nástrojů z vašeho registrátora názvu domény aktualizovat záznam DNS pro název domény. Záznam musí odkazovat na IP adresu služby.

  Může docházet k prodlevám minut nebo hodin, než klienti lze přeložit název domény, v závislosti na doménový Registrátor a "time to live" (TTL), který je nakonfigurovaný pro název domény.

+ **Pro AKS:**

  > [!WARNING]
  > Pokud jste použili *leaf_domain_label* k vytvoření služby pomocí certifikátu od společnosti Microsoft, nemusíte aktualizovat ručně hodnotu DNS pro cluster. Hodnota by měla být nastaví automaticky.

  Aktualizace služby DNS **konfigurace** kartu veřejnou IP adresu clusteru AKS. (Viz následující obrázek). Veřejná IP adresa je typ prostředku, který se vytvoří ve skupině prostředků, který obsahuje agentské uzly AKS a jiných síťových prostředků.

  ![Služba Azure Machine Learning: Zabezpečení webové služby s protokolem SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Další postup
Naučte se:
+ [Využívání služby machine learning model nasadit jako webovou službu](how-to-consume-web-service.md)
+ [Bezpečné spuštění experimentů a odvození uvnitř virtuální sítě Azure](how-to-enable-virtual-network.md)
