---
title: Zabezpečené webové služby pomocí TLS
titleSuffix: Azure Machine Learning
description: Zjistěte, jak povolit protokol HTTPS, abyste zabezpečili webovou službu, která se nasadí prostřednictvím Azure Machine Learning. Azure Machine Learning používá TLS verze 1.2 k zabezpečení modelů nasazených jako webové služby.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287335"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Tento článek ukazuje, jak zabezpečit webovou službu, která se nasadí prostřednictvím Azure Machine Learning.

Protokol [HTTPS](https://en.wikipedia.org/wiki/HTTPS) slouží k omezení přístupu k webovým službám a zabezpečení dat, která klienti odesílají. Protokol HTTPS pomáhá zabezpečit komunikaci mezi klientem a webovou službou šifrováním komunikace mezi těmito dvěma. Šifrování používá [zabezpečení transportní vrstvy (TLS).](https://en.wikipedia.org/wiki/Transport_Layer_Security) TLS je někdy stále označován jako *SSL (Secure Sockets L),* což byl předchůdce TLS.

> [!TIP]
> Sada Azure Machine Learning SDK používá termín "SSL" pro vlastnosti, které souvisejí se zabezpečenou komunikací. To neznamená, že vaše webová služba nepoužívá *TLS*. SSL je jen běžně uznávaný termín.
>
> Konkrétně webové služby nasazené prostřednictvím Azure Machine Learning podporují jenom TLS verze 1.2.

TLS i SSL spoléhají na *digitální certifikáty*, které pomáhají s šifrováním a ověřováním identity. Další informace o tom, jak digitální certifikáty fungují, najdete v tématu Wikipedie [Infrastruktura veřejných klíčů](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Pokud pro webovou službu nepoužíváte protokol HTTPS, data odeslaná do služby a ze služby mohou být viditelná ostatním uživatelům na internetu.
>
> Protokol HTTPS také umožňuje klientovi ověřit pravost serveru, ke kterému se připojuje. Tato funkce chrání klienty před útoky [prostředníkem.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Toto je obecný proces zabezpečení webové služby:

1. Získejte název domény.

2. Získejte digitální certifikát.

3. Nasazení nebo aktualizace webové služby s povoleným protokolem TLS

4. Aktualizujte službu DNS tak, aby přectola na webovou službu.

> [!IMPORTANT]
> Pokud nasazujete do služby Azure Kubernetes Service (AKS), můžete si zakoupit vlastní certifikát nebo použít certifikát, který poskytuje Microsoft. Pokud používáte certifikát od společnosti Microsoft, nemusíte získat název domény nebo certifikát TLS/SSL. Další informace naleznete v části [Povolit TLS a nasazení](#enable) tohoto článku.

Existují mírné rozdíly při zabezpečení s mezi [cíli nasazení](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Získání názvu domény

Pokud název domény ještě nevlastníte, zakupte si jej u *registrátora názvů domén*. Proces a cena se liší mezi registrátory. Registrátor poskytuje nástroje pro správu názvu domény. Pomocí těchto nástrojů můžete namapovat plně kvalifikovaný název domény (například www\.contoso.com) na ADRESU IP, která je hostitelem vaší webové služby.

## <a name="get-a-tlsssl-certificate"></a>Získání certifikátu TLS/SSL

Existuje mnoho způsobů, jak získat certifikát TLS/SSL (digitální certifikát). Nejběžnější je nákup od *certifikační autority* (CA). Bez ohledu na to, kde certifikát získáte, potřebujete následující soubory:

* **Certifikát**. Certifikát musí obsahovat celý řetěz certifikátů a musí být kódován jako PEM.
* **Klíč**. Klíč musí být také kódován PEM.

Při žádosti o certifikát je nutné zadat hlavní faktickou adresu, kterou chcete pro webovou\.službu použít (například www contoso.com). Adresa, která je vyznačena do certifikátu a adresa, kterou klienti používají, jsou porovnány s ověřením identity webové služby. Pokud se tyto adresy neshodují, zobrazí se chybová zpráva klienta.

> [!TIP]
> Pokud certifikační autorita nemůže poskytnout certifikát a klíč jako soubory kódované PEM, můžete formát změnit pomocí nástroje, jako je [OpenSSL.](https://www.openssl.org/)

> [!WARNING]
> Certifikáty *podepsané svým držitelem* používejte pouze pro vývoj. Nepoužívejte je v produkčním prostředí. Certifikáty podepsané svým držitelem mohou způsobit problémy v klientských aplikacích. Další informace naleznete v dokumentaci k síťovým knihovnám, které používá klientská aplikace.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Povolení TLS a nasazení

Chcete-li nasadit (nebo znovu nasadit) službu s povolenou službou TLS, nastavte parametr *ssl_enabled* na hodnotu True, ať už je použitelná. Nastavte *parametr ssl_certificate* na hodnotu souboru *certifikátu.* Nastavte *ssl_key* na hodnotu souboru *klíče.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Nasazení na AKS a pole-programovatelné brány pole (FPGA)

  > [!NOTE]
  > Informace v této části platí také při nasazení zabezpečené webové služby pro návrháře. Pokud nejste obeznámeni s používáním sady Python SDK, přečtěte [si, co je sada Azure Machine Learning SDK pro Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Při nasazení do AKS můžete vytvořit nový cluster AKS nebo připojit existující. Další informace o vytváření nebo připojování clusteru najdete v [tématu Nasazení modelu do clusteru služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  Pokud vytvoříte nový cluster, použijete **[AksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Pokud připojíte existující cluster, použijete **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Oba vrátí konfigurační objekt, který má **metodu enable_ssl.**

Metoda **enable_ssl** můžete použít certifikát, který je poskytován společností Microsoft nebo certifikát, který zakoupíte.

  * Při použití certifikátu od společnosti Microsoft je nutné použít *parametr leaf_domain_label.* Tento parametr generuje název DNS pro službu. Například hodnota "contoso" vytvoří název domény "contoso\<šest náhodných znaků>. \<azureregion>.cloudapp.azure.com", \<kde azureregion> je oblast, která obsahuje službu. Volitelně můžete použít *parametr overwrite_existing_domain* k přepsání *existujícíleaf_domain_label*.

    Chcete-li nasadit (nebo znovu nasadit) službu s povolenou službou TLS, nastavte parametr *ssl_enabled* na hodnotu True, ať už je použitelná. Nastavte *parametr ssl_certificate* na hodnotu souboru *certifikátu.* Nastavte *ssl_key* na hodnotu souboru *klíče.*

    > [!IMPORTANT]
    > Při použití certifikátu od společnosti Microsoft není nutné kupovat vlastní certifikát nebo název domény.

    Následující příklad ukazuje, jak vytvořit konfiguraci, která umožňuje certifikát TLS/SSL od společnosti Microsoft:

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

  * Při použití *zakoupeného certifikátu používáte*parametry *ssl_cert_pem_file*, *ssl_key_pem_file*a *ssl_cname.* Následující příklad ukazuje, jak pomocí souborů *PEM* vytvořit konfiguraci, která používá zakoupený certifikát TLS/SSL:

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

Další informace o *enable_ssl*naleznete [v tématech AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) a [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Nasazení v instanci kontejnerů Azure

Při nasazení do azure container instances zadáte hodnoty pro parametry související s TLS, jak ukazuje následující fragment kódu:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Další informace naleznete [v tématu AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aktualizace DNS

Dále je nutné aktualizovat službu DNS tak, aby přecštovala na webovou službu.

+ **Pro instance kontejneru:**

  Pomocí nástrojů od registrátora názvů domén aktualizujte záznam DNS pro název domény. Záznam musí ukazovat na IP adresu služby.

  Může dojít ke zpoždění minut nebo hodin, než klienti mohou přeložit název domény, v závislosti na registrátorovi a "time to live" (TTL), který je nakonfigurován pro název domény.

+ **Pro AKS:**

  > [!WARNING]
  > Pokud jste k vytvoření služby pomocí certifikátu od společnosti Microsoft *použili leaf_domain_label,* neaktualizujte ručně hodnotu DNS pro cluster. Hodnota by měla být nastavena automaticky.

  Aktualizujte DNS veřejné IP adresy clusteru AKS na kartě **Konfigurace** v části **Nastavení** v levém podokně. (Viz následující obrázek.) Veřejná IP adresa je typ prostředku, který je vytvořen v rámci skupiny prostředků, která obsahuje uzly agenta AKS a další síťové prostředky.

  [![Azure Machine Learning: Zabezpečení webových služeb pomocí TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Aktualizace certifikátu TLS/SSL

Platnost certifikátů TLS/SSL vyprší a musí být obnovena. Obvykle se to děje každý rok. Informace v následujících částech použijte k aktualizaci a obnovení certifikátu pro modely nasazené do služby Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aktualizace certifikátu generovaného společností Microsoft

Pokud byl certifikát původně vygenerován společností Microsoft (při použití *leaf_domain_label* k vytvoření služby), použijte k aktualizaci certifikátu jeden z následujících příkladů:

**Použití sady SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Použití rozhraní příkazového řádku**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Další informace naleznete v následujících referenčních dokumentech:

* [Konfigurace Ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Konfigurace AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Aktualizace vlastního certifikátu

Pokud byl certifikát původně vygenerován certifikační autoritou, postupujte takto:

1. K obnovení certifikátu použijte dokumentaci dopouvedenou certifikační autoritou. Tento proces vytvoří nové soubory certifikátů.

1. Pomocí sady SDK nebo cli aktualizujte službu novým certifikátem:

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

Další informace naleznete v následujících referenčních dokumentech:

* [Konfigurace Ssl](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Konfigurace AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Zakázat tls

Chcete-li zakázat tls pro model nasazený do služby `SslConfiguration` `status="Disabled"`Azure Kubernetes, vytvořte s , pak proveďte aktualizaci:

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
+ [Využití modelu strojového učení nasazeného jako webová služba](how-to-consume-web-service.md)
+ [Bezpečné spouštění experimentů a odvození uvnitř virtuální sítě Azure](how-to-enable-virtual-network.md)
