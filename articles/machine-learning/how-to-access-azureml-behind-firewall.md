---
title: Použít bránu firewall
titleSuffix: Azure Machine Learning
description: Řízení přístupu k pracovním prostorům Azure Machine Learning pomocí bran Azure firewall. Přečtěte si o hostitelích, které musíte přes bránu firewall umožňovat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 8560acd9c5a11004c5144441d395863c8b85edba
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461404"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Pro Azure Machine Learning použít pracovní prostor za bránou firewall

V tomto článku se dozvíte, jak nakonfigurovat Azure Firewall pro řízení přístupu k pracovnímu prostoru Azure Machine Learning a k veřejnému Internetu. Další informace o zabezpečení Azure Machine Learning najdete v tématu [Enterprise Security for Azure Machine Learning](concept-enterprise-security.md)

## <a name="azure-firewall"></a>Azure Firewall

Při použití Azure Firewall použijte __cílovou síťovou adresu (DNAT)__ a vytvořte pravidla překladu adres (NAT) pro příchozí provoz. U odchozích přenosů vytvořte pravidla __sítě__ nebo __aplikace__ . Tyto kolekce pravidel jsou podrobněji popsány v tématu [co je několik Azure firewall konceptů](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Příchozí konfigurace

Pokud používáte __výpočetní instanci__ nebo __výpočetní cluster__ Azure Machine Learning, přidejte do podsítě [definované uživatelem (udr)](../virtual-network/virtual-networks-udr-overview.md) pro podsíť, která obsahuje prostředky Azure Machine Learning. Tato trasa vynutí provoz __z__ IP adres `BatchNodeManagement` prostředků a, `AzureMachineLearning` do veřejné IP adresy vaší výpočetní instance a z výpočetního clusteru.

Tyto udr umožňují, aby služba Batch komunikovala s výpočetními uzly pro plánování úloh. Přidejte také IP adresu pro službu Azure Machine Learning, kde existují prostředky, jak je to nutné pro přístup k výpočetním instancím. Chcete-li získat seznam IP adres služby Batch a služby Azure Machine Learning, použijte jednu z následujících metod:

* Stáhněte si [rozsahy IP adres Azure a značky služby](https://www.microsoft.com/download/details.aspx?id=56519) a vyhledejte v něm soubor `BatchNodeManagement.<region>` a `AzureMachineLearning.<region>` , kde `<region>` je vaše oblast Azure.

* Informace si můžete stáhnout pomocí [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) . Následující příklad stáhne informace o IP adrese a odfiltruje informace o Východní USA 2 oblasti:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > Pokud používáte oblasti US-) – Virginia, US-Arizona oblasti nebo Čínu – východ-2, tyto příkazy nevrátí žádné IP adresy. Místo toho použijte jeden z následujících odkazů ke stažení seznamu IP adres:
    >
    > * [Rozsahy IP adres a značek služeb Azure pro Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Rozsahy IP adres a značky služeb Azure pro Azure Čína](https://www.microsoft.com//download/details.aspx?id=57062)

Když přidáte udr, definujte trasu pro každou související předponu IP adresy dávky a nastavte __typ dalšího segmentu směrování__ na __Internet__. Následující obrázek ukazuje příklad tohoto UDR v Azure Portal:

![Příklad UDR pro předponu adresy](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP adresy se můžou v průběhu času měnit.

Další informace najdete v tématu [Vytvoření fondu Azure Batch ve virtuální síti](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Odchozí konfigurace

1. Přidat __Síťová pravidla__, povolit provoz __do__ a __z__ následujících značek služeb:

    * Azureactivedirectory selhala
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. region
    * Trezor klíčů. region
    * ContainerRegistry. region

    Pokud plánujete používat výchozí image Docker poskytované Microsoftem a povolit závislosti spravované uživatelem, musíte taky přidat tyto značky služby:

    * MicrosoftContainerRegistry. region
    * AzureFrontDoor.FirstParty

    Pro položky, které obsahují `region` , nahraďte oblastí Azure, kterou používáte. Například, `keyvault.westus`.

    Pro __protokol__ vyberte `TCP` . Pro zdrojový a cílový __port__ vyberte `*` .

1. Přidejte __pravidla aplikací__ pro následující hostitele:

    > [!NOTE]
    > Nejedná se o úplný seznam hostitelů potřebných pro všechny prostředky Pythonu na internetu, a to pouze nejčastěji používané. Pokud například potřebujete přístup k úložišti GitHub nebo jinému hostiteli, je nutné v tomto scénáři identifikovat a přidat požadované hostitele.

    | **Název hostitele** | **Účel** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*. anaconda.com** | Používá se k instalaci výchozích balíčků. |
    | **\*. anaconda.org** | Slouží k získání dat úložiště. |
    | **pypi.org** | Slouží k vypsání závislostí z výchozího indexu, pokud existují, a index není přepsán uživatelským nastavením. Pokud je index přepsán, je nutné také povolte **\* . pythonhosted.org**. |
    | **cloud.r-project.org** | Používá se při instalaci balíčků CRAN pro vývoj pro R. |
    | **\*pytorch.org** | Používá se v některých příkladech založených na PyTorch. |
    | **\*. tensorflow.org** | Používá se v některých příkladech založených na Tensorflow. |

    Pro __protokol: port__ vyberte použít __http, https__.

    Další informace o konfiguraci pravidel aplikací najdete v tématu [nasazení a konfigurace Azure firewall](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Pokud chcete omezit přístup k modelům nasazeným ve službě Azure Kubernetes Service (AKS), přečtěte si téma [omezení odchozího provozu ve službě Azure Kubernetes Service](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Další brány firewall

Pokyny v této části jsou obecné, protože každá brána firewall má svou vlastní terminologii a konkrétní konfiguraci. Pokud máte dotazy týkající se povolení komunikace přes bránu firewall, přečtěte si prosím dokumentaci pro bránu firewall, kterou používáte.

Pokud není správně nakonfigurovaný, může Brána firewall způsobovat problémy s vaším pracovním prostorem. K dispozici je celá řada názvů hostitelů, které používá Azure Machine Learning pracovní prostor. V následujících oddílech jsou uvedeny hostitelé, kteří jsou Azure Machine Learning.

### <a name="microsoft-hosts"></a>Hostitelé Microsoftu

Hostitelé v této části vlastní Microsoft a poskytují služby vyžadované pro správné fungování pracovního prostoru. Následující tabulky obsahují seznam názvů hostitelů pro oblasti Azure Public, Azure Government a Azure Čína 21Vianet.

**Obecné hostitelé Azure**

| **Vyžaduje se pro** | **Veřejný Azure** | **Azure Government** | **Azure (Čína) 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| portál Azure | management.azure.com | management.azure.us | management.azure.cn |

**Azure Machine Learning hostitelé**

| **Vyžaduje se pro** | **Veřejný Azure** | **Azure Government** | **Azure (Čína) 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| Rozhraní API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Experimentování, historie, Hyperdrive, označování | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Správa modelů | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Kanál | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Návrhář (Studio Service) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Integrovaný Poznámkový blok | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Integrovaný Poznámkový blok | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Integrovaný Poznámkový blok | \*. dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Integrovaný Poznámkový blok | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Integrovaný Poznámkový blok | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integrovaný Poznámkový blok | \*. aznbcontent.net |  | |

**Azure Machine Learning výpočetní instance a hostitele výpočetního clusteru**

| **Vyžaduje se pro** | **Veřejný Azure** | **Azure Government** | **Azure (Čína) 21Vianet** |
| ----- | ----- | ----- | ----- |
| Výpočetní cluster/instance | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Instance služby Compute | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Instance služby Compute | \*. instances.azureml.ms |  |  |

**Přidružené prostředky používané pomocí Azure Machine Learning**

| **Vyžaduje se pro** | **Veřejný Azure** | **Azure Government** | **Azure (Čína) 21Vianet** |
| ----- | ----- | ----- | ----- |
| Účet služby Azure Storage | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Container Registry Microsoftu | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Pokud plánujete použití federované identity, postupujte podle [osvědčených postupů pro zabezpečení Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) článku.

K přidání IP adres pro a použijte taky informace v části [vynucené tunelování](how-to-secure-training-vnet.md#forced-tunneling) `BatchNodeManagement` `AzureMachineLearning` .

Informace o omezení přístupu k modelům nasazeným do služby Azure Kubernetes Service (AKS) najdete v tématu [omezení odchozího provozu ve službě Azure Kubernetes Service](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Hostitelé Pythonu

Hostitelé v této části se používají k instalaci balíčků Pythonu. Jsou požadovány během vývoje, školení a nasazení. 

> [!NOTE]
> Nejedná se o úplný seznam hostitelů potřebných pro všechny prostředky Pythonu na internetu, a to pouze nejčastěji používané. Pokud například potřebujete přístup k úložišti GitHub nebo jinému hostiteli, je nutné v tomto scénáři identifikovat a přidat požadované hostitele.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Používá se k instalaci výchozích balíčků. |
| **\*. anaconda.org** | Slouží k získání dat úložiště. |
| **pypi.org** | Slouží k vypsání závislostí z výchozího indexu, pokud existují, a index není přepsán uživatelským nastavením. Pokud je index přepsán, je nutné také povolte **\* . pythonhosted.org**. |
| **\*pytorch.org** | Používá se v některých příkladech založených na PyTorch. |
| **\*. tensorflow.org** | Používá se v některých příkladech založených na Tensorflow. |

### <a name="r-hosts"></a>Hostitelé R

Hostitelé v této části se používají k instalaci balíčků R. Jsou požadovány během vývoje, školení a nasazení.

> [!NOTE]
> Nejedná se o úplný seznam hostitelů potřebných pro všechny prostředky R na internetu, a to pouze nejčastěji používané. Pokud například potřebujete přístup k úložišti GitHub nebo jinému hostiteli, je nutné v tomto scénáři identifikovat a přidat požadované hostitele.

| **Název hostitele** | **Účel** |
| ---- | ---- |
| **cloud.r-project.org** | Používá se při instalaci balíčků CRAN. |

> [!IMPORTANT]
> Sada R SDK pro Azure Machine Learning interně používá balíčky Pythonu. Proto musíte taky umožnit hostitelům Pythonu přes bránu firewall.
## <a name="next-steps"></a>Další kroky

* [Kurz: Nasazení a konfigurace brány Azure Firewall pomocí webu Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Zabezpečení experimentů a úloh odvozování v Azure ML ve virtuální síti Azure](how-to-network-security-overview.md)