---
title: Zabezpečení webových služeb pomocí protokolu TLS
titleSuffix: Azure Machine Learning
description: Naučte se, jak povolit protokol HTTPS s TLS verze 1,2 k zabezpečení webové služby nasazené prostřednictvím Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 01/04/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 14e4fda6ef36dd8fc57529046473d8afa916ef2c
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880966"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Zabezpečení webové služby prostřednictvím služby Azure Machine Learning s využitím protokolu TLS


V tomto článku se dozvíte, jak zabezpečit webovou službu, která je nasazená prostřednictvím Azure Machine Learning.

Pomocí [protokolu HTTPS](https://en.wikipedia.org/wiki/HTTPS) můžete omezit přístup k webovým službám a zabezpečit data, která klienti odesílají. Protokol HTTPS pomáhá zabezpečit komunikaci mezi klientem a webovou službou tím, že šifruje komunikaci mezi nimi. Šifrování používá [protokol TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Protokol TLS se někdy označuje jako *SSL (Secure Sockets Layer)* (SSL), což bylo předchůdce TLS.

> [!TIP]
> Sada Azure Machine Learning SDK používá pojem "SSL" pro vlastnosti, které se vztahují k zabezpečené komunikaci. To neznamená, že webová služba nepoužívá protokol *TLS*. SSL je jenom častěji rozpoznaná doba.
>
> Konkrétně webové služby nasazené prostřednictvím Azure Machine Learning podporují protokol TLS verze 1,2 pro AKS a ACI. V případě nasazení ACI, pokud používáte starší verzi TLS, doporučujeme opětovné nasazení a získat nejnovější verzi TLS.

Protokoly TLS a SSL závisí na *digitálních certifikátech*, které vám pomůžou se šifrováním a ověřením identity. Další informace o tom, jak digitální certifikáty fungují, najdete v tématu [Infrastruktura veřejných klíčů](https://en.wikipedia.org/wiki/Public_key_infrastructure)tématu Wikipedii.

> [!WARNING]
> Pokud pro webovou službu nepoužíváte protokol HTTPS, data odesílaná do a ze služby mohou být viditelná i pro ostatní na internetu.
>
> Protokol HTTPS taky umožňuje klientovi ověřit pravost serveru, ke kterému se připojuje. Tato funkce chrání klienty proti [útokům prostředníkem](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Toto je obecný proces zabezpečení webové služby:

1. Získá název domény.

2. Získejte digitální certifikát.

3. Nasaďte nebo aktualizujte webovou službu s povoleným protokolem TLS.

4. Aktualizujte DNS tak, aby odkazovalo na webovou službu.

> [!IMPORTANT]
> Pokud nasazujete do služby Azure Kubernetes Service (AKS), můžete si koupit vlastní certifikát nebo použít certifikát, který poskytuje Microsoft. Pokud používáte certifikát od Microsoftu, nemusíte mít k dispozici certifikát s názvem domény ani protokol TLS/SSL. Další informace najdete v části [Povolení TLS a nasazení](#enable) v tomto článku.

Existují mírné rozdíly při zabezpečení napříč [cíli nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud název domény ještě nemáte, kupte si ho od *registrátora názvu domény*. Proces a cena se v rámci registrátorů liší. Registrátor poskytuje nástroje pro správu názvu domény. Tyto nástroje slouží k mapování plně kvalifikovaného názvu domény (FQDN) (například webové \. contoso.com) na IP adresu, která je hostitelem vaší webové služby.

## <a name="get-a-tlsssl-certificate"></a>Získání certifikátu TLS/SSL

Existuje mnoho způsobů, jak získat certifikát TLS/SSL (digitální certifikát). Nejběžnější je koupit si ho od certifikační *autority* (CA). Bez ohledu na to, kde certifikát obdržíte, potřebujete následující soubory:

* **Certifikát**. Certifikát musí obsahovat úplný řetěz certifikátů a musí být "PEM-encodeded".
* **Klíč**. Klíč musí být také zakódovaný v PEM.

Když vyžádáte certifikát, musíte zadat plně kvalifikovaný název domény adresy, kterou chcete používat pro webovou službu (například www \. contoso.com). Adresa, která je vyražena na certifikát a adresu, kterou používají klienti, je porovnána s cílem ověřit identitu webové služby. Pokud se tyto adresy neshodují, klient obdrží chybovou zprávu.

> [!TIP]
> Pokud certifikační autorita nemůže certifikát a klíč zadat jako soubory kódované PEM, můžete změnit formát pomocí nástroje, jako je třeba [OpenSSL](https://www.openssl.org/) .

> [!WARNING]
> Certifikáty *podepsané svým držitelem* používejte jenom pro vývoj. Nepoužívejte je v produkčních prostředích. Certifikáty podepsané svým držitelem můžou způsobit problémy v klientských aplikacích. Další informace naleznete v dokumentaci pro síťové knihovny, které používá vaše klientská aplikace.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Povolení TLS a nasazení

V případě **nasazení AKS** můžete povolit ukončení protokolu TLS, když [vytvoříte nebo připojíte cluster AKS](how-to-create-attach-kubernetes.md) v pracovním prostoru AML. V době nasazení modelu AKS můžete zrušit ukončení protokolu TLS pomocí objektu konfigurace nasazení, jinak bude v případě, že je v clusteru AKS povolený čas, vytvořit nebo připojit čas.

Pro nasazení ACI můžete povolit ukončení protokolu TLS v době nasazení modelu s objektem Konfigurace nasazení.


### <a name="deploy-on-azure-kubernetes-service"></a>Nasazení ve službě Azure Kubernetes

  > [!NOTE]
  > Informace v této části platí také při nasazení zabezpečené webové služby pro návrháře. Pokud nejste obeznámeni s používáním sady Python SDK, přečtěte si téma [co je Azure Machine Learning SDK pro Python?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Když [vytvoříte nebo připojíte cluster AKS](how-to-create-attach-kubernetes.md) v pracovním prostoru AML, můžete povolit ukončení protokolu TLS s objekty konfigurace **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** a **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Obě metody vrátí objekt konfigurace, který má metodu **Enable_ssl** , a můžete použít metodu **Enable_ssl** k povolení protokolu TLS.

Protokol TLS můžete povolit buď s certifikátem společnosti Microsoft, nebo s vlastním certifikátem zakoupeným od certifikační autority. 

* Použijete **-li certifikát od společnosti Microsoft**, je nutné použít parametr *leaf_domain_label* . Tento parametr vygeneruje název DNS pro službu. Například hodnota "contoso" vytvoří název domény "contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com ", kde \<azureregion> je oblast, která obsahuje službu. Volitelně můžete pomocí parametru *overwrite_existing_domain* přepsat existující *leaf_domain_label*. Následující příklad ukazuje, jak vytvořit konfiguraci, která umožňuje protokol TLS s certifikátem Microsoftu:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Pokud používáte certifikát od Microsoftu, nemusíte kupovat svůj vlastní certifikát ani název domény.

    > [!WARNING]
    > Pokud je váš cluster AKS nakonfigurovaný s interním nástrojem pro vyrovnávání zatížení, nepodporuje se certifikát poskytnutý společností __Microsoft a musíte__ použít vlastní certifikát k povolení TLS.

* **Když použijete vlastní certifikát, který jste zakoupili**, použijete parametry *ssl_cert_pem_file*, *ssl_key_pem_file* a *ssl_cname* . Následující příklad ukazuje, jak pomocí souborů. pem vytvořit konfiguraci, která používá certifikát TLS/SSL, který jste zakoupili:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Další informace o *Enable_ssl* naleznete v tématu [AksProvisioningConfiguration.Enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) a [AksAttachConfiguration.Enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Nasazení v Azure Container Instances

Při nasazení na Azure Container Instances zadáte hodnoty parametrů souvisejících s protokolem TLS, jak ukazuje následující fragment kódu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Další informace najdete v tématu [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aktualizace DNS

Pro nasazení AKS s vlastním certifikátem nebo nasazením ACI je potřeba aktualizovat záznam DNS tak, aby odkazoval na IP adresu bodování koncového bodu.

  > [!IMPORTANT]
  > Když použijete certifikát od nasazení Microsoft pro AKS, nemusíte ručně aktualizovat hodnotu DNS pro cluster. Hodnota by měla být nastavena automaticky.

Pomocí následujících kroků můžete aktualizovat záznam DNS pro vlastní název domény:
* Získejte IP adresu koncového bodu vyhodnocování z identifikátoru URI koncového bodu, který je obvykle ve formátu *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Pomocí nástrojů z registrátora názvu domény aktualizujte záznam DNS pro název domény. Záznam musí ukazovat na IP adresu koncového bodu bodování.
* Po aktualizaci záznamu DNS můžete překlad DNS ověřit pomocí příkazu *nslookup Custom-Domain-Name* . Pokud je záznam DNS správně aktualizovaný, bude název vlastní domény ukazovat na IP adresu bodování koncového bodu.
* Může dojít ke zpoždění minut nebo hodin, než klienti budou moci přeložit název domény v závislosti na registrátoru a TTL (Time to Live), který je nakonfigurován pro název domény.


## <a name="update-the-tlsssl-certificate"></a>Aktualizace certifikátu TLS/SSL

Platnost certifikátů protokolu TLS/SSL vyprší a je třeba je obnovit. K tomu obvykle dochází každý rok. Informace v následujících částech použijte k aktualizaci a obnovení certifikátu pro modely nasazené do služby Azure Kubernetes Service:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizace certifikátu vygenerovaného společností Microsoft

Pokud byl certifikát původně vygenerován společností Microsoft (při použití *leaf_domain_label* k vytvoření služby), bude v případě potřeby **automaticky obnoven** . Pokud ho chcete ručně obnovit, použijte k aktualizaci certifikátu jeden z následujících příkladů:

> [!IMPORTANT]
> * Pokud je stávající certifikát stále platný, použijte `renew=True` (SDK) nebo `--ssl-renew` (CLI), aby konfigurace vynutila jeho obnovení. Pokud je například stávající certifikát stále platný po dobu 10 dnů a nepoužijete `renew=True` , certifikát se nemusí prodloužit.
> * Při původním nasazení této služby se `leaf_domain_label` pomocí vzoru vytvoří název DNS `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Chcete-li zachovat stávající název (včetně 6 číslic původně vygenerovaných), použijte původní `leaf_domain_label` hodnotu. Nezahrnujte 6 číslic, které byly vygenerovány.

**Použití sady SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Použití rozhraní příkazového řádku**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Další informace najdete v následujících dokumentech k dokumentaci:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualizovat vlastní certifikát

Pokud byl certifikát původně vygenerován certifikační autoritou, použijte následující postup:

1. K obnovení certifikátu použijte dokumentaci poskytovanou certifikační autoritou. Tento proces vytvoří nové soubory certifikátů.

1. Pomocí sady SDK nebo rozhraní příkazového řádku aktualizujte službu pomocí nového certifikátu:

    **Použití sady SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Použití rozhraní příkazového řádku**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Další informace najdete v následujících dokumentech k dokumentaci:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

## <a name="disable-tls"></a>Zakázat TLS

Pokud chcete protokol TLS zakázat pro model nasazený do služby Azure Kubernetes, vytvořte `SslConfiguration` pomocí `status="Disabled"` a pak proveďte aktualizaci:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Další kroky
Naučte se:
+ [Využití modelu strojového učení nasazeného jako webové služby](how-to-consume-web-service.md)
+ [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)