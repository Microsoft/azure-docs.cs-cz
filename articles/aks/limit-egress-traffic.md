---
title: Omezení odchozího provozu ve službě Azure Kubernetes (AKS)
description: Informace o tom, jaké porty a adresy se vyžadují k řízení odchozího provozu ve službě Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765588"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Řízení přenosů dat pro uzly clusteru ve službě Azure Kubernetes (AKS)

Tento článek poskytuje potřebné informace, které vám umožní zabezpečit odchozí přenosy ze služby Azure Kubernetes (AKS). Obsahuje požadavky na cluster pro základní nasazení AKS a další požadavky na volitelné doplňky a funkce. [Příklad vám poskytneme na konci postupu konfigurace těchto požadavků pomocí Azure firewall](#restrict-egress-traffic-using-azure-firewall). Tyto informace ale můžete použít na jakékoli odchozí metody omezení nebo zařízení.

## <a name="background"></a>Pozadí

Clustery AKS se nasazují ve virtuální síti. Tuto síť můžete spravovat (vytvořenou pomocí AKS) nebo vlastní (předem nakonfigurovanou uživatelem). V obou případech má cluster **odchozí** závislosti na službách mimo tuto virtuální síť (služba nemá žádné příchozí závislosti).

Pro účely správy a provozu musí uzly v clusteru AKS přistupovat k určitým portům a plně kvalifikovaným názvům domén (FQDN). Tyto koncové body jsou vyžadovány pro uzly ke komunikaci se serverem rozhraní API nebo ke stažení a instalaci základních součástí clusteru Kubernetes a aktualizací zabezpečení uzlů. Cluster například potřebuje načíst základní image kontejnerů z Microsoft Container Registry (MCR).

Odchozí závislosti AKS jsou téměř zcela definovány s plně kvalifikovanými názvy domén, které nemají statické adresy za nimi. Nedostatek statických adres znamená, že skupiny zabezpečení sítě nelze použít k uzamknutí odchozího provozu z clusteru AKS. 

Ve výchozím nastavení mají clustery AKS neomezený odchozí (výstupní) přístup k Internetu. Tato úroveň přístupu k síti umožňuje uzlům a službám, které spouštíte pro přístup k externím prostředkům, podle potřeby. Pokud chcete omezit výstupní přenos dat, musí být k dispozici omezený počet portů a adres, aby bylo možné udržovat v pořádku úlohy údržby clusteru. Nejjednodušším řešením pro zabezpečení odchozích adres je použití zařízení brány firewall, které může řídit odchozí přenosy na základě názvů domén. Azure Firewall například může omezit odchozí přenosy HTTP a HTTPS na základě plně kvalifikovaného názvu domény cílového umístění. Můžete taky nakonfigurovat upřednostňovanou bránu firewall a pravidla zabezpečení, abyste tyto požadované porty a adresy povolili.

> [!IMPORTANT]
> Tento dokument popisuje pouze to, jak uzamknout provoz opustí AKS podsíť. AKS ve výchozím nastavení nemá žádné požadavky na příchozí přenosy.  Blokování **provozu interní podsítě** pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) a brány firewall se nepodporuje. K řízení a blokování provozu v rámci clusteru použijte [**_zásady sítě_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Požadovaná odchozí síťová pravidla a plně kvalifikované názvy domény pro clustery AKS

Pro cluster AKS se vyžadují následující pravidla sítě a plně kvalifikovaného názvu domény nebo aplikace, která můžete použít, pokud chcete nakonfigurovat řešení jiné než Azure Firewall.

* Závislosti IP adres pro přenos bez HTTP/S (provoz TCP i UDP)
* Do zařízení brány firewall lze umístit koncové body s plně kvalifikovaným názvem domény (FQDN) HTTP/HTTPS.
* Koncové body HTTP/HTTPS se zástupnými znaky jsou závislosti, které se můžou u vašeho clusteru AKS lišit, a to na základě řady kvalifikátorů.
* AKS pomocí kontroleru pro přístup vloží plně kvalifikovaný název domény jako proměnnou prostředí do všech nasazení v rámci Kube systému a serveru gatekeeper. tím zajistíte, aby veškerá systémová komunikace mezi uzly a serverem API používala plně kvalifikovaný název domény serveru API a ne IP Server API. 
* Pokud máte aplikaci nebo řešení, které potřebuje komunikovat se serverem rozhraní API, je nutné přidat **Další** síťové pravidlo, které umožní *komunikaci TCP s portem 443 IP serveru rozhraní API*.
* Ve výjimečných případech, pokud existuje operace údržby, vaše IP adresa serveru API se může změnit. Plánované operace údržby, které můžou měnit IP adresy serveru API, se vždycky sdělují předem.


### <a name="azure-global-required-network-rules"></a>Globální požadovaná síťová pravidla Azure

Požadovaná síťová pravidla a závislosti IP adres:

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Nebo* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. Nevyžaduje se pro [privátní clustery](private-clusters.md) .|
| **`*:9000`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Nebo* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. Nevyžaduje se pro [privátní clustery](private-clusters.md) . |
| **`*:123`** nebo **`ntp.ubuntu.com:123`** (Pokud používáte Azure firewall síťových pravidel)  | UDP      | 123     | Vyžadováno pro synchronizaci času NTP (Network Time Protocol) na uzlech se systémem Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Pokud používáte vlastní servery DNS, musíte zajistit, aby byly přístupné pro uzly clusteru. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vyžaduje se, aby při spuštění lusků a nasazení, které přistupují k serveru rozhraní API, používala tato lusky nebo nasazení rozhraní API IP. Nevyžaduje se pro [privátní clustery](private-clusters.md) .  |

### <a name="azure-global-required-fqdn--application-rules"></a>Globální požadovaný plně kvalifikovaný název domény nebo pravidla aplikace v Azure 

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény                 | Port            | Použití      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Vyžaduje se pro komunikaci mezi uzly < > serveru API. Nahraďte *\<location\>* oblastí, ve které je nasazený cluster AKS. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Vyžaduje se pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje obrázky nebo grafy první strany (například coreDNS atd.). Tyto image jsou potřebné ke správnému vytvoření a fungování clusteru, včetně operací škálování a upgradu.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Vyžaduje se pro MCR úložiště, které zajišťuje služba Azure Content Delivery Network (CDN). |
| **`management.azure.com`**       | **`HTTPS:443`** | Vyžaduje se pro operace Kubernetes s rozhraním API Azure. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Vyžaduje se pro Azure Active Directory ověřování. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Tato adresa je určena pro úložiště potřebné ke stažení a instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Požadovaná síťová pravidla pro Azure Čína 21Vianet

Požadovaná síťová pravidla a závislosti IP adres:

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Nebo* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
| **`*:9000`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Nebo* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
| **`*:22`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Nebo* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
| **`*:123`** nebo **`ntp.ubuntu.com:123`** (Pokud používáte Azure firewall síťových pravidel)  | UDP      | 123     | Vyžadováno pro synchronizaci času NTP (Network Time Protocol) na uzlech se systémem Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Pokud používáte vlastní servery DNS, musíte zajistit, aby byly přístupné pro uzly clusteru. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vyžaduje se v případě, že se používají lusky nebo nasazení, které přistupují k serveru rozhraní API. tyto položky nebo nasazení by používaly IP adresu rozhraní API.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Čína 21Vianet vyžaduje plně kvalifikovaný název domény nebo pravidla použití aplikace

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény                               | Port            | Použití      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vyžaduje se pro komunikaci mezi uzly < > serveru API. Nahraďte *\<location\>* oblastí, ve které je nasazený cluster AKS. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Vyžaduje se pro komunikaci mezi uzly < > serveru API. Nahraďte *\<location\>* oblastí, ve které je nasazený cluster AKS. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Vyžaduje se pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje obrázky nebo grafy první strany (například coreDNS atd.). Tyto image jsou potřebné ke správnému vytvoření a fungování clusteru, včetně operací škálování a upgradu. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Vyžaduje se pro MCR úložiště, které zajišťuje Azure Content Delivery Network (CDN). |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Vyžaduje se pro operace Kubernetes s rozhraním API Azure. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Vyžaduje se pro Azure Active Directory ověřování. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Tato adresa je určena pro úložiště potřebné ke stažení a instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Požadovaná síťová pravidla pro státní správu USA Azure

Požadovaná síťová pravidla a závislosti IP adres:

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Nebo* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
| **`*:9000`** <br/> *Nebo* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Nebo* <br/> [Oblastní CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Nebo* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Pro Tunelově zabezpečenou komunikaci mezi uzly a rovinou ovládacího prvku. |
| **`*:123`** nebo **`ntp.ubuntu.com:123`** (Pokud používáte Azure firewall síťových pravidel)  | UDP      | 123     | Vyžadováno pro synchronizaci času NTP (Network Time Protocol) na uzlech se systémem Linux.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Pokud používáte vlastní servery DNS, musíte zajistit, aby byly přístupné pro uzly clusteru. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Vyžaduje se, aby při spuštění lusků a nasazení, které přistupují k serveru rozhraní API, používala tato lusky nebo nasazení rozhraní API IP.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure USA – požadovaná plně kvalifikovaný název domény/pravidla použití 

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény                                        | Port            | Použití      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Vyžaduje se pro komunikaci mezi uzly < > serveru API. Nahraďte *\<location\>* oblastí, ve které je nasazený cluster AKS.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Vyžaduje se pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje obrázky nebo grafy první strany (například coreDNS atd.). Tyto image jsou potřebné ke správnému vytvoření a fungování clusteru, včetně operací škálování a upgradu. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Vyžaduje se pro MCR úložiště, které zajišťuje služba Azure Content Delivery Network (CDN). |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Vyžaduje se pro operace Kubernetes s rozhraním API Azure. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Vyžaduje se pro Azure Active Directory ověřování. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Tato adresa je určena pro úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Volitelné Doporučené plně kvalifikovaný název domény nebo pravidla použití pro clustery AKS

Následující plně kvalifikovaný název domény nebo pravidla použití jsou volitelné, ale doporučuje se pro clustery AKS:

| Cílový plně kvalifikovaný název domény                                                               | Port          | Použití      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Tato adresa umožní uzlům clusteru se systémem Linux stáhnout požadované opravy a aktualizace zabezpečení. |

Pokud se rozhodnete blokovat/Nepovolit tyto plně kvalifikované názvy domén, uzly budou přijímat pouze aktualizace operačního systému, když provedete [Upgrade bitové kopie uzlu](node-image-upgrade.md) nebo [upgrade clusteru](upgrade-cluster.md).

## <a name="gpu-enabled-aks-clusters"></a>AKS clustery s podporou GPU

### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace

Pro clustery AKS s povoleným grafickým procesorem jsou vyžadovány následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény                        | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |

## <a name="windows-server-based-node-pools"></a>Fondy uzlů založené na Windows serveru 

### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace

Pro použití fondů uzlů na bázi Windows serveru jsou potřeba následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový plně kvalifikovaný název domény                                                           | Port      | Použití      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Instalace binárních souborů souvisejících s Windows |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Instalace binárních souborů souvisejících s Windows |

## <a name="aks-addons-and-integrations"></a>Doplňky a integrace AKS

### <a name="azure-monitor-for-containers"></a>Azure Monitor pro kontejnery

Existují dvě možnosti, jak zajistit přístup k Azure Monitor kontejnerů, můžete umožnit, aby Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) **nebo** poskytovali přístup k požadovaným plně kvalifikovanému názvu domény nebo pravidlům aplikace.

#### <a name="required-network-rules"></a>Požadovaná síťová pravidla

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Tento koncový bod slouží k posílání dat metrik a protokolů pro Azure Monitor a Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace

Pro clustery AKS s povoleným Azure Monitor pro kontejnery jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Tento koncový bod se používá pro metriky a monitorování telemetrie pomocí Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Tento koncový bod používá Azure Monitor k ingestování dat Log Analytics. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Tento koncový bod používá omsagent, který se používá k ověření služby Log Analytics. |
| *. monitoring.azure.com | **`HTTPS:443`** | Tento koncový bod slouží k odesílání dat metrik do Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Aktualizujte bránu firewall nebo konfiguraci zabezpečení tak, aby povolovaly síťový provoz do a ze všech níže uvedených plně kvalifikovaných názvů domény a [služby Azure dev Spaces infrastruktury][dev-spaces-service-tags].

#### <a name="required-network-rules"></a>Požadovaná síťová pravidla

| Cílový koncový bod                                                             | Protokol | Port    | Použití  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Tento koncový bod slouží k posílání dat metrik a protokolů pro Azure Monitor a Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace 

Pro clustery AKS s povoleným Azure Dev Spaces jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Tato adresa se používá pro získání systému Linux Alpine a dalších Azure Dev Spacesch imagí. |
| `gcr.io` | **`HTTPS:443`** | Tato adresa se používá k získání imagí Helm/do pokladny. |
| `storage.googleapis.com` | **`HTTPS:443`** | Tato adresa se používá k získání imagí Helm/do pokladny. |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Požadovaný plně kvalifikovaný název domény nebo pravidla použití aplikace 

Pro clustery AKS s povoleným Azure Policy jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití.

| FQDN                                          | Port      | Použití      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání zásad Kubernetes a k hlášení stavu dodržování předpisů clusteru službě zásad. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání artefaktů integrovaných zásad na serveru gatekeeper. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Tato adresa se používá pro správnou operaci Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Tato adresa se používá k vyžádání předdefinovaných zásad z GitHubu, aby bylo zajištěno správné fungování Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy doplněk, který odesílá data telemetrie do koncového bodu Application Insights. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure Čína 21Vianet vyžaduje plně kvalifikovaný název domény nebo pravidla použití aplikace 

Pro clustery AKS s povoleným Azure Policy jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití.

| FQDN                                          | Port      | Použití      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání zásad Kubernetes a k hlášení stavu dodržování předpisů clusteru službě zásad. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání artefaktů integrovaných zásad na serveru gatekeeper. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure USA – požadovaná plně kvalifikovaný název domény/pravidla použití

Pro clustery AKS s povoleným Azure Policy jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití.

| FQDN                                          | Port      | Použití      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání zásad Kubernetes a k hlášení stavu dodržování předpisů clusteru službě zásad. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Tato adresa se používá k vyžádání artefaktů integrovaných zásad na serveru gatekeeper. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Omezení odchozího provozu pomocí brány Azure firewall

Azure Firewall poskytuje `AzureKubernetesService` pro zjednodušení této konfigurace značku plně kvalifikovaného názvu domény služby Azure Kubernetes. 

> [!NOTE]
> Značka plně kvalifikovaného názvu domény obsahuje všechny plně kvalifikované názvy domény uvedené výše a je automaticky udržovány v aktuálním stavu.
>
> Doporučujeme, abyste v Azure Firewall produkčních scénářů aspoň 20 front-endové IP adresy, abyste se vyhnuli problémům s vyčerpáním portů SNAT.

Níže je uvedená ukázková architektura nasazení:

![Zamčená topologie](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Příchozí přenos dat do filtrů brány firewall se nuceně přesměruje na veřejné příchozí přenosy
  * Uzly agenta AKS jsou izolované ve vyhrazené podsíti.
  * [Azure firewall](../firewall/overview.md) je nasazená ve vlastní podsíti.
  * Pravidlo DNAT Překládá veřejnou IP adresu FW na IP adresu front-endu.
* Odchozí požadavky začínají z uzlů agentů na Azure Firewall interní IP adresu pomocí [uživatelem definované trasy](egress-outboundtype.md) .
  * Požadavky z uzlů agentů AKS se řídí UDR, který byl umístěn v podsíti, do které byl nasazen cluster AKS.
  * VyAzure Firewall výstup z virtuální sítě z front-endu veřejné IP adresy
  * Přístup k veřejným internetům nebo jiným službám Azure, ke kterým se dostanete, do a z IP adresy front-endu brány firewall
  * Volitelně je přístup k rovině ovládacího prvku AKS chráněn pomocí [rozsahů IP adres autorizovaných serverem API](./api-server-authorized-ip-ranges.md), které zahrnují veřejnou IP adresu front-endu brány firewall.
* Interní provoz
  * Volitelně můžete místo toho nebo kromě [veřejných Load Balancer](load-balancer-standard.md) použít [interní Load Balancer](internal-lb.md) pro interní provoz, který byste měli izolovat i v vlastní podsíti.


V následujících krocích se používá `AzureKubernetesService` značka plně kvalifikovaného názvu domény Azure firewall k omezení odchozího provozu z clusteru AKS a k dispozici je příklad konfigurace veřejného příchozího provozu přes bránu firewall.


### <a name="set-configuration-via-environment-variables"></a>Nastavení konfigurace pomocí proměnných prostředí

Definujte sadu proměnných prostředí, které se mají použít při vytváření prostředků.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Vytvoření virtuální sítě s několika podsítěmi

Zřídit virtuální síť se dvěma samostatnými podsítěmi, jednu pro cluster, jednu pro bránu firewall. Volitelně můžete také vytvořit jednu pro příchozí příchozí provoz služby.

![Prázdná topologie sítě](media/limit-egress-traffic/empty-network.png)

Vytvořte skupinu prostředků pro uložení všech prostředků.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Vytvořte virtuální síť se dvěma podsítěmi pro hostování clusteru AKS a Azure Firewall. Každá z nich bude mít vlastní podsíť. Pojďme začít používat AKS síť.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Vytvoření a nastavení Azure Firewall s UDR

Musí být nakonfigurovaná pravidla příchozího a odchozího Azure Firewall. Hlavním účelem brány firewall je umožnit organizacím konfigurovat podrobná pravidla příchozího provozu a odchozí přenos dat do clusteru AKS a z něj.

![Brána firewall a UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Pokud váš cluster nebo aplikace vytvoří velký počet odchozích připojení směrovaných na stejnou nebo malou podmnožinu cílů, budete možná potřebovat víc IP adres front-endu, abyste se vyhnuli maxingům portů na front-endu.
> Další informace o tom, jak vytvořit bránu firewall Azure s více IP adresami, najdete [ **tady** .](../firewall/quick-create-multiple-ip-template.md)

Vytvořte prostředek veřejné IP adresy standardní SKU, který bude použit jako Azure Firewall adresa front-endu.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Zaregistrujte verzi Preview rozhraní příkazového řádku pro vytvoření Azure Firewall.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
IP adresa, kterou jste vytvořili dříve, se teď dá přiřadit ke front-endu brány firewall.

> [!NOTE]
> Nastavení veřejné IP adresy na Azure Firewall může trvat několik minut.
> Aby bylo možné využít plně kvalifikovaný název domény u pravidel sítě, potřebujeme povolený proxy server DNS, pokud je povolená brána firewall, bude naslouchat na portu 53 a předává požadavky DNS na výše uvedený server DNS. To umožní bráně firewall automaticky přeložit tento plně kvalifikovaný název domény.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Po úspěšném dokončení předchozího příkazu uložte IP adresu front-endu firewallu pro pozdější konfiguraci.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Pokud používáte zabezpečený přístup k serveru rozhraní AKS API s [povolenými rozsahy IP adres](./api-server-authorized-ip-ranges.md), musíte přidat veřejnou IP adresu brány firewall do povoleného rozsahu IP adres.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Vytvoření UDR s segmentem směrování na Azure Firewall

Azure automaticky směruje provoz mezi podsítěmi Azure, virtuálními sítěmi a místními sítěmi. Pokud chcete změnit některý z výchozích směrování Azure, provedete to tak, že vytvoříte směrovací tabulku.

Vytvořte prázdnou směrovací tabulku, kterou chcete přidružit k dané podsíti. Směrovací tabulka bude definovat další segment směrování, jak Azure Firewall vytvořili výše. Každá podsíť může mít přidruženou žádnou nebo jednu směrovací tabulku.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Informace o tom, jak můžete přepsat výchozí systémové trasy Azure nebo přidat další trasy do směrovací tabulky podsítě, najdete v [dokumentaci k tabulce směrování virtuální sítě](../virtual-network/virtual-networks-udr-overview.md#user-defined) .

### <a name="adding-firewall-rules"></a>Přidávání pravidel brány firewall

Níže jsou uvedená tři Síťová pravidla, která můžete použít ke konfiguraci brány firewall, možná budete muset tato pravidla přizpůsobit v závislosti na vašem nasazení. První pravidlo umožňuje přístup k portu 9000 přes protokol TCP. Druhé pravidlo umožňuje přístup k portům 1194 a 123 přes protokol UDP (Pokud provádíte nasazení do Azure Čína 21Vianet, budete možná potřebovat [Další](#azure-china-21vianet-required-network-rules)). Obě tato pravidla budou umožňovat jenom provoz směřující do oblasti Azure s CIDR, kterou používáme, v tomto případě Východní USA. Nakonec přidáme třetí pravidlo sítě, ve kterém se otevírají porty 123 až do `ntp.ubuntu.com` plně kvalifikovaného názvu domény přes UDP (přidání plně kvalifikovaného názvu domény jako pravidlo sítě je jednou z konkrétních funkcí Azure firewall a budete je muset při použití vlastních možností přizpůsobit).

Po nastavení síťových pravidel přidáme také pravidlo aplikace pomocí nástroje `AzureKubernetesService` , které pokrývá všechny potřebné plně kvalifikované názvy domén, které jsou přístupné prostřednictvím portu TCP 443 a portu 80.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Další informace o službě Azure Firewall najdete v [dokumentaci k Azure firewall](../firewall/overview.md) .

### <a name="associate-the-route-table-to-aks"></a>Přidružení směrovací tabulky k AKS

K přidružení clusteru k bráně firewall musí vyhrazená podsíť pro podsíť clusteru odkazovat na tabulku směrování vytvořenou výše. Přidružení se dá udělat vyvoláním příkazu do virtuální sítě, která drží cluster i bránu firewall k aktualizaci směrovací tabulky podsítě clusteru.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Nasazení AKS s odchozím typem UDR do existující sítě

Cluster AKS se teď dá nasadit do existující virtuální sítě. Použijeme také [odchozí typ `userDefinedRouting` ](egress-outboundtype.md). Tato funkce zajišťuje, že veškerý odchozí provoz bude vynucený přes bránu firewall a nebudou existovat žádné další výstupní cesty (ve výchozím nastavení se dá použít Load Balancer odchozího typu).

![AKS – nasazení](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Vytvoření instančního objektu s přístupem ke zřízení v existující virtuální síti

Identitu clusteru (spravovanou identitu nebo instanční objekt) používá AKS k vytváření prostředků clusteru. Instanční objekt, který se předává v čase vytvoření, se používá k vytvoření základních prostředků AKS, jako jsou prostředky úložiště, IP adresy a nástroje pro vyrovnávání zatížení používané v AKS (místo toho můžete také použít [spravovanou identitu](use-managed-identity.md) ). Pokud nejsou níže udělená příslušná oprávnění, nebudete moct zřídit cluster AKS.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Nyní nahraďte `APPID` a `PASSWORD` níže objektem AppID a heslo instančního objektu, které jsou automaticky generovány předchozím výstupem příkazu. Odkaz na ID prostředku virtuální sítě vám poskytne oprávnění k instančnímu objektu, aby AKS mohli do něj nasadit prostředky.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

[Tady](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)můžete ověřit podrobná oprávnění, která jsou potřeba.

> [!NOTE]
> Pokud používáte modul plug-in kubenet Network, budete muset pro předem vytvořenou směrovací tabulku přidělit oprávnění instančního objektu AKS nebo spravovaná identita, protože kubenet vyžaduje, aby tabulka směrování přidala pravidla směrování nezbytné. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Nasazení AKS

Cluster AKS se nakonec dá nasadit do existující podsítě, kterou jsme pro cluster vyhrazujeme. Cílová podsíť, do které se má nasadit, je definována s proměnnou prostředí `$SUBNETID` . V předchozích krocích jsme nedefinovali `$SUBNETID` proměnnou. Chcete-li nastavit hodnotu ID podsítě, můžete použít následující příkaz:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Budete definovat typ odchozího připojení pro použití UDR, který již v podsíti existuje. Tato konfigurace umožní AKS přeskočit nastavení a zřizování IP adres pro nástroj pro vyrovnávání zatížení.

> [!IMPORTANT]
> Další informace o odchozím typu UDR, včetně omezení, najdete v tématu [**odchozí výstupní typ udr**](egress-outboundtype.md#limitations).


> [!TIP]
> Do nasazení clusteru, jako je například [**soukromý cluster**](private-clusters.md), lze přidat další funkce. 
>
> Funkci AKS pro [**rozsahy IP adres autorizovaných serverem API**](api-server-authorized-ip-ranges.md) lze přidat k omezení přístupu serveru rozhraní API pouze k veřejnému koncovému bodu brány firewall. Funkce povolených rozsahů IP adres je v diagramu označena jako volitelná. Když povolíte funkci schváleného rozsahu IP adres pro omezení přístupu k serveru rozhraní API, nástroje pro vývojáře musí použít JumpBox z virtuální sítě brány firewall nebo musíte přidat všechny koncové body pro vývojáře do povoleného rozsahu IP adres.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Povolení přístupu pro vývojáře k serveru rozhraní API

Pokud jste v předchozím kroku použili schválené rozsahy IP adres pro cluster, musíte přidat IP adresy vývojářských nástrojů do seznamu clusterů AKS schválených rozsahů IP adres, abyste z něj mohli získat přístup k serveru rozhraní API. Další možností je nakonfigurovat JumpBox s potřebnými nástroji v samostatné podsíti ve virtuální síti brány firewall.

Pomocí následujícího příkazu přidejte do schválených rozsahů další IP adresu.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 K nakonfigurování `kubectl` připojení k nově vytvořenému clusteru Kubernetes použijte příkaz [az AKS Get-Credentials] [az-AKS-Get-Credentials]. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Nasazení veřejné služby
Teď můžete začít vystavovat služby a nasazovat aplikace do tohoto clusteru. V tomto příkladu vystavíme veřejnou službu, ale můžete se také rozhodnout vystavit interní službu prostřednictvím [interního nástroje pro vyrovnávání zatížení](internal-lb.md).

![DNAT veřejné služby](media/limit-egress-traffic/aks-create-svc.png)

Nasaďte aplikaci hlasovací aplikace v Azure zkopírováním níže uvedeného YAML do souboru s názvem `example.yaml` .

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Nasaďte službu spuštěním:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Přidat pravidlo DNAT do Azure Firewall

> [!IMPORTANT]
> Když použijete Azure Firewall k omezení odchozího provozu a vytvoření trasy definované uživatelem (UDR) k vynucení veškerého odchozího provozu, ujistěte se, že jste v bráně firewall vytvořili příslušné pravidlo DNAT pro správné povolení provozu příchozího přenosu dat. Použití Azure Firewall s UDR v důsledku asymetrického směrování narušuje nastavení příchozího přenosu dat. (K tomuto problému dochází, pokud má podsíť AKS výchozí trasu, která přechází na soukromou IP adresu brány firewall, ale používáte veřejný Nástroj pro vyrovnávání zatížení – příchozí nebo Kubernetes služba typu: Vyrovnávání zatížení sítě). V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, dojde k vrácení vráceného paketu, protože brána firewall neví o zavedené relaci. Informace o tom, jak integrovat Azure Firewall s vaším nástrojem pro vyrovnávání zatížení pro příchozí přenosy nebo služby, najdete v tématu věnovaném [integraci Azure firewall s Azure Standard Load Balancer](../firewall/integrate-lb.md).


Aby bylo možné konfigurovat příchozí připojení, musí být do Azure Firewall zapsáno pravidlo DNAT. Pro otestování připojení ke clusteru je definováno pravidlo pro veřejnou IP adresu front-endu firewall pro směrování do interní IP adresy vystavené interní službou.

Cílovou adresu lze přizpůsobit, protože se jedná o port brány firewall, pro který má být k dispozici. Přeložená adresa musí být IP adresa interního nástroje pro vyrovnávání zatížení. Přeložený port musí být vystavený port pro vaši službu Kubernetes.

Budete muset zadat interní IP adresu přiřazenou k nástroji pro vyrovnávání zatížení, který vytvořila služba Kubernetes. Načtěte adresu spuštěním:

```bash
kubectl get services
```

Požadovaná IP adresa bude uvedena ve sloupci EXTERNAL-IP, podobně jako v následujícím příkladu.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Získejte IP adresu služby spuštěním:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Přidejte pravidlo překladu adres (NAT) tak, že spustíte:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Ověřit připojení

V prohlížeči přejděte na IP adresu front-endu Azure Firewall a ověřte připojení.

Měla by se zobrazit hlasovací aplikace AKS. V tomto příkladu byla veřejná IP adresa brány firewall `52.253.228.132` .


![Snímek obrazovky zobrazuje hlasovací aplikaci A K S s tlačítky pro kočky, psy a Reset a součty.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky Azure, odstraňte skupinu prostředků AKS.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jaké porty a adresy se mají povolit, pokud chcete omezit výstupní přenos pro cluster. Zjistili jste také, jak zabezpečit odchozí přenosy pomocí Azure Firewall. 

V případě potřeby můžete generalizovat výše uvedené kroky a přesměrovat provoz do preferovaného řešení pro odchozí přenosy, a to podle [ `userDefinedRoute` dokumentace typu odchozí](egress-outboundtype.md).

Pokud chcete omezit, jak lusky komunikují mezi sebou a East-Westm omezením provozu v rámci clusteru, podívejte [se na téma zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
