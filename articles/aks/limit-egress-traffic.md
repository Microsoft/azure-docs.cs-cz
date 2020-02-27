---
title: Omezení odchozího provozu ve službě Azure Kubernetes (AKS)
description: Informace o tom, jaké porty a adresy se vyžadují k řízení odchozího provozu ve službě Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: a76f4eb8680d07193feb29450fdba7bb2a710a68
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595004"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Řízení přenosů dat pro uzly clusteru ve službě Azure Kubernetes (AKS)

Ve výchozím nastavení mají clustery AKS neomezený odchozí (výstupní) přístup k Internetu. Tato úroveň přístupu k síti umožňuje uzlům a službám, které spouštíte pro přístup k externím prostředkům, podle potřeby. Pokud chcete omezit výstupní přenos dat, musí být k dispozici omezený počet portů a adres, aby bylo možné udržovat v pořádku úlohy údržby clusteru. Cluster je ve výchozím nastavení nakonfigurovaný tak, aby používal image kontejnerů základního systému jenom z Microsoft Container Registry (MCR) nebo Azure Container Registry (ACR). Nakonfigurujte upřednostňovanou bránu firewall a pravidla zabezpečení, aby bylo možné tyto požadované porty a adresy.

Tento článek podrobně popisuje, které síťové porty a plně kvalifikované názvy domény (FQDN) jsou povinné a volitelné, pokud omezíte odchozí přenosy v clusteru AKS.

> [!IMPORTANT]
> Tento dokument popisuje pouze to, jak uzamknout provoz opustí AKS podsíť. AKS nemá žádné požadavky na příchozí přenosy.  Blokování provozu interní podsítě pomocí skupin zabezpečení sítě (skupin zabezpečení sítě) a brány firewall se nepodporuje. K řízení a blokování provozu v rámci clusteru použijte [zásady sítě][network-policy].

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.66 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Přehled odchozího provozu

Pro účely správy a provozu musí uzly v clusteru AKS přistupovat k určitým portům a plně kvalifikovaným názvům domén (FQDN). Tyto akce můžou být pro komunikaci se serverem rozhraní API nebo ke stažení a instalaci základních součástí clusteru Kubernetes a aktualizací zabezpečení uzlů. Ve výchozím nastavení není odchozí (odchozí) internetový provoz omezený pro uzly v clusteru AKS. Cluster může z externích úložišť získat základní image kontejneru systému.

Chcete-li zvýšit zabezpečení clusteru AKS, můžete chtít omezit odchozí přenosy. Cluster je nakonfigurovaný tak, aby vyčetl základní image kontejneru systému z MCR nebo ACR. Pokud tímto způsobem zamknete výstupní přenos, definujte konkrétní porty a plně kvalifikované názvy domény, aby uzly AKS správně komunikovaly s požadovanými externími službami. Bez těchto autorizovaných portů a plně kvalifikovaných názvů domény nemůžou uzly AKS komunikovat se serverem rozhraní API nebo instalovat součásti jádra.

K zabezpečení odchozího provozu a definování potřebných portů a adres můžete použít [Azure firewall][azure-firewall] nebo zařízení brány firewall jiného výrobce. AKS automaticky nevytváří tato pravidla. Při vytváření vhodných pravidel v bráně firewall sítě jsou k disadrese následující porty a adresy.

> [!IMPORTANT]
> Když použijete Azure Firewall k omezení odchozího provozu a vytvoření trasy definované uživatelem (UDR) k vynucení veškerého odchozího provozu, ujistěte se, že jste v bráně firewall vytvořili příslušné pravidlo DNAT pro správné povolení provozu příchozího přenosu dat. Použití Azure Firewall s UDR v důsledku asymetrického směrování narušuje nastavení příchozího přenosu dat. (K tomuto problému dochází, pokud má podsíť AKS výchozí trasu, která přechází na soukromou IP adresu brány firewall, ale používáte veřejný Nástroj pro vyrovnávání zatížení – příchozí nebo Kubernetes služba typu: Vyrovnávání zatížení sítě). V tomto případě se příchozí provoz nástroje pro vyrovnávání zatížení přijímá prostřednictvím veřejné IP adresy, ale návratová cesta prochází přes privátní IP adresu brány firewall. Vzhledem k tomu, že brána firewall je stavová, dojde k vrácení vráceného paketu, protože brána firewall neví o zavedené relaci. Informace o tom, jak integrovat Azure Firewall s vaším nástrojem pro vyrovnávání zatížení pro příchozí přenosy nebo služby, najdete v tématu věnovaném [integraci Azure firewall s Azure Standard Load Balancer](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Provoz pro port TCP 9000 a port TCP 22 můžete uzamknout pomocí síťového pravidla mezi IP adresami odchozího pracovního uzlu a IP adresou serveru rozhraní API.

V AKS existují dvě sady portů a adres:

* [Požadované porty a adresy pro clustery AKS podrobně vyžadují](#required-ports-and-addresses-for-aks-clusters) minimální požadavky pro ověřený odchozí přenos.
* [Volitelné Doporučené adresy a porty pro clustery AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) se nevyžadují pro všechny scénáře, ale integrace s jinými službami, jako je Azure monitor, nebudou správně fungovat. Zkontrolujte tento seznam volitelných portů a plně kvalifikovaných názvů domény a autorizujte všechny služby a součásti používané v clusteru AKS.

> [!NOTE]
> Omezení odchozího provozu funguje jenom na nových clusterech AKS. U existujících clusterů [proveďte operaci upgradu clusteru][aks-upgrade] pomocí příkazu `az aks upgrade` před omezením odchozího provozu.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Požadované porty a adresy pro clustery AKS

Pro cluster AKS se vyžadují následující Odchozí porty nebo Síťová pravidla:

* Port TCP *443*
* TCP [IPAddrOfYourAPIServer]: 443 se vyžaduje, pokud máte aplikaci, která potřebuje komunikovat se serverem rozhraní API.  Tuto změnu lze nastavit po vytvoření clusteru.
* Port TCP *9000* a TCP port *22* pro front-endu tunelu pro komunikaci s koncovým tunelem na serveru rozhraní API.
    * Pokud chcete získat konkrétnější informace, podívejte se na*umístění *.HCP.\<\>.azmk8s.IO* a * *.tun.\<umístění\> adresy azmk8s.IO* v následující tabulce.
* Port UDP *123* pro synchronizaci času NTP (Network Time Protocol) (uzly Linux).
* Port UDP *53* pro DNS se vyžaduje i v případě, že máte k dispozici přímý přístup k serveru rozhraní API.

Vyžaduje se následující plně kvalifikovaný název domény nebo pravidla použití:

> [!IMPORTANT]
> * **. blob.Core.Windows.NET a aksrepos.azurecr.IO** již nevyžadují pravidla plně kvalifikovaného názvu domény pro funkci pro přenos odchozích dat.  U existujících clusterů [proveďte operaci upgradu clusteru][aks-upgrade] pomocí příkazu `az aks upgrade`, abyste tato pravidla odebrali.

- Globální Azure

| Plně kvalifikovaný název domény                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *. HCP.\<umístění\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| *.tun.\<umístění\>. azmk8s.io | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je nutná pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje soubory a grafy první strany (například Moby atd.), které se vyžadují pro fungování clusteru během upgradu a škálování clusteru. |
| *.cdn.mscr.io              | HTTPS:443 | Tato adresa je vyžadována pro MCR úložiště zajištěné službou Azure Content Delivery Network (CDN). |
| management.azure.com       | HTTPS:443 | Tato adresa je vyžadována pro Kubernetes operace GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Tato adresa je vyžadována pro Azure Active Directory ověřování. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa se vyžaduje pro synchronizaci času NTP na uzlech se systémem Linux. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |
| acs-mirror.azureedge.net   | HTTPS:443 | Tato adresa je určena pro úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

- Azure China 21Vianet

| Plně kvalifikovaný název domény                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *. HCP.\<umístění\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| *.tun.\<umístění\>. cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| *. azk8s.cn        | HTTPS:443 | Tato adresa se vyžaduje ke stažení požadovaných binárních souborů a imagí.|
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je nutná pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje soubory a grafy první strany (například Moby atd.), které se vyžadují pro fungování clusteru během upgradu a škálování clusteru. |
| *.cdn.mscr.io              | HTTPS:443 | Tato adresa je vyžadována pro MCR úložiště zajištěné službou Azure Content Delivery Network (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Tato adresa je vyžadována pro Kubernetes operace GET/PUT. |
| login.chinacloudapi.cn  | HTTPS:443 | Tato adresa je vyžadována pro Azure Active Directory ověřování. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa se vyžaduje pro synchronizaci času NTP na uzlech se systémem Linux. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |

- Azure Government

| Plně kvalifikovaný název domény                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *. HCP.\<umístění\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| *.tun.\<umístění\>. cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000 | Tato adresa je koncový bod serveru rozhraní API. *\>umístění\<* nahraďte oblastí, ve které je nasazený cluster AKS. |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je nutná pro přístup k obrázkům v Microsoft Container Registry (MCR). Tento registr obsahuje soubory a grafy první strany (například Moby atd.), které se vyžadují pro fungování clusteru během upgradu a škálování clusteru. |
| *.cdn.mscr.io              | HTTPS:443 | Tato adresa je vyžadována pro MCR úložiště zajištěné službou Azure Content Delivery Network (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Tato adresa je vyžadována pro Kubernetes operace GET/PUT. |
| login.microsoftonline.us  | HTTPS:443 | Tato adresa je vyžadována pro Azure Active Directory ověřování. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa se vyžaduje pro synchronizaci času NTP na uzlech se systémem Linux. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků Microsoftu používané pro operace *apt-get* uložené v mezipaměti.  Příklady balíčků zahrnují Moby, PowerShell a Azure CLI. |
| acs-mirror.azureedge.net   | HTTPS:443 | Tato adresa je určena pro úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Volitelné Doporučené adresy a porty pro clustery AKS

Následující Odchozí porty nebo Síťová pravidla jsou pro cluster AKS volitelné:

Pro správné fungování clusterů AKS se doporučuje následující plně kvalifikovaný název domény nebo pravidla použití:

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Tato adresa umožní uzlům clusteru se systémem Linux stáhnout požadované opravy a aktualizace zabezpečení. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Požadované adresy a porty pro AKS clustery s podporou GPU

Pro clustery AKS s povoleným grafickým procesorem jsou vyžadovány následující plně kvalifikovaný název domény nebo pravidla použití:

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |
| us.download.nvidia.com | HTTPS:443 | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |
| apt.dockerproject.org | HTTPS:443 | Tato adresa se používá pro správnou instalaci ovladače a operaci na uzlech založených na GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Požadované adresy a porty s Azure Monitor pro kontejnery povoleny

Pro clustery AKS s povoleným Azure Monitor pro kontejnery jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití:

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443  | Jedná se o správné metriky a monitorování telemetrie pomocí Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443 | Tuto hodnotu používá Azure Monitor k ingestování dat Log Analytics. |
| *.oms.opinsights.azure.com | HTTPS:443 | Tuto adresu používá omsagent, která se používá k ověření služby Log Analytics. |
|*.microsoftonline.com | HTTPS:443 | Slouží k ověřování a posílání metrik pro Azure Monitor. |
|*.monitoring.azure.com | HTTPS:443 | Slouží k odesílání dat metrik do Azure Monitor. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Požadované adresy a porty s povoleným Azure Dev Spaces

Pro clustery AKS s povoleným Azure Dev Spaces jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití:

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Tato adresa se používá pro získání systému Linux Alpine a dalších Azure Dev Spacesch imagí. |
| gcr.io | HTTP: 443 | Tato adresa se používá k získání imagí Helm/do pokladny. |
| storage.googleapis.com | HTTP: 443 | Tato adresa se používá k získání imagí Helm/do pokladny. |
| azds –<guid>.<location>. azds.io | HTTPS:443 | Pro komunikaci se službou Azure Dev Spaces back-end pro váš kontroler. Přesný plně kvalifikovaný název domény najdete v části "dataplaneFqdn" v% USERPROFILE%\.azds\settings.JSON |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Požadované adresy a porty pro clustery AKS s povoleným Azure Policy (ve verzi Public Preview)

> [!CAUTION]
> Některé z následujících funkcí jsou ve verzi Preview.  Návrhy v tomto článku se můžou změnit, protože funkce se přesouvá do fází Public Preview a budoucích verzí.

Pro clustery AKS s povoleným Azure Policy jsou vyžadována následující plně kvalifikovaný název domény nebo pravidla použití.

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Tato adresa se používá pro správnou operaci Azure Policy. (aktuálně ve verzi Preview v AKS) |
| RAW.githubusercontent.com | HTTPS:443 | Tato adresa se používá k vyžádání předdefinovaných zásad z GitHubu, aby bylo zajištěno správné fungování Azure Policy. (aktuálně ve verzi Preview v AKS) |
| *. GK.<location>. azmk8s.io | HTTPS:443 | Doplněk Azure Policy, který se domluví na koncový bod auditu serveru gatekeeper běžící na hlavním serveru, aby získal výsledky auditu. |
| dc.services.visualstudio.com | HTTPS:443 | Doplněk Azure Policy, který odesílá data telemetrie do koncového bodu Application Insights. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Vyžadované uzly na bázi Windows serveru (ve verzi Public Preview) povolené

> [!CAUTION]
> Některé z následujících funkcí jsou ve verzi Preview.  Návrhy v tomto článku se můžou změnit, protože funkce se přesouvá do fází Public Preview a budoucích verzí.

Pro clustery AKS založené na Windows serveru jsou potřeba následující plně kvalifikovaný název domény nebo pravidla použití:

| Plně kvalifikovaný název domény                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Instalace binárních souborů souvisejících s Windows |
| mp.microsoft.com, www<span></span>. msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Instalace binárních souborů souvisejících s Windows |
| kms.core.windows.net | TCP: 1688 | Instalace binárních souborů souvisejících s Windows |


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jaké porty a adresy se mají povolit, pokud omezíte odchozí přenosy clusteru. Můžete také definovat, jak můžou částice a jaká omezení v clusteru komunikovat. Další informace najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
