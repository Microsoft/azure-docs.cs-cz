---
title: Omezení odchozího přenosu ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jaké porty a adresy jsou potřeba k řízení odchozího provozu ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 2cd7aeea272d22615d3ba3d3db6acc2c84d22cca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080175"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Řízení odchozího přenosu dat pro uzly clusteru ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení mají clustery AKS neomezený odchozí (odchozí) přístup k Internetu. Tato úroveň přístupu k síti umožňuje uzlům a službám, které spouštěte, přístup k externím prostředkům podle potřeby. Pokud chcete omezit přenos y odchozího přenosu, musí být omezený počet portů a adres přístupný pro údržbu clusteru. Cluster je ve výchozím nastavení nakonfigurován tak, aby používal pouze image kontejnerů základního systému z registru kontejnerů Microsoft (MCR) nebo registru kontejnerů Azure (ACR). Nakonfigurujte upřednostňovanou bránu firewall a pravidla zabezpečení tak, aby umožňovaly tyto požadované porty a adresy.

Tento článek podrobně popisuje, jaké síťové porty a plně kvalifikované názvy domén (FQDN) jsou povinné a volitelné, pokud omezíte odchozí přenosy v clusteru AKS.

> [!IMPORTANT]
> Tento dokument popisuje pouze způsob, jak uzamknout provoz opouštějící podsíť AKS. AKS nemá žádné požadavky na příchozí přenos dat.  Blokování provozu interních podsítí pomocí skupin zabezpečení sítě (NSG) a bran firewall není podporováno. Chcete-li řídit a blokovat provoz v rámci clusteru, použijte [zásady sítě][network-policy].

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalované a nakonfigurované azure CLI verze 2.0.66 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Přehled odchozího provozu

Pro účely správy a provozu musí uzly v clusteru AKS přistupovat k určitým portům a plně kvalifikovaným názvům domén (FQDNs). Tyto akce může být komunikovat se serverem rozhraní API nebo stáhnout a potom nainstalovat základní součásti clusteru Kubernetes a aktualizace zabezpečení uzlu. Ve výchozím nastavení není odchozí (odchozí) internetový provoz pro uzly v clusteru AKS omezen. Cluster může vytáhnout image kontejneru základního systému z externích úložišť.

Chcete-li zvýšit zabezpečení clusteru AKS, můžete omezit odchozí přenosy. Cluster je nakonfigurován tak, aby vytahoval image kontejnerů základního systému z mcr nebo ACR. Pokud tímto způsobem uzamknete odchozí provoz, definujte konkrétní porty a hlavní název, aby uzly AKS mohly správně komunikovat s požadovanými externími službami. Bez těchto autorizovaných portů a souhrnných virtuálních dna nemohou uzly AKS komunikovat se serverem rozhraní API ani instalovat základní součásti.

[Pomocí brány Azure Firewall][azure-firewall] nebo zařízení brány firewall třetí strany můžete zabezpečit přenos odchozího přenosu a definovat tyto požadované porty a adresy. AKS nevytváří automaticky tato pravidla pro vás. Následující porty a adresy jsou vhodné při vytváření příslušných pravidel v síťové bráně firewall.

> [!IMPORTANT]
> Pokud pomocí brány Azure Firewall omezíte přenosy odchozích přenosů a vytvoříte uživatelem definovanou trasu (UDR) k vynucení všech odchozích přenosů, ujistěte se, že jste v bráně firewall vytvořili příslušné pravidlo DNAT, které správně povolí přenos příchozího přenosu. Použití brány Azure Firewall s UDR přeruší nastavení příchozího přenosu dat z důvodu asymetrického směrování. (K problému dochází, pokud podsíť AKS má výchozí trasu, která přejde na privátní IP adresu brány firewall, ale používáte veřejný vyrovnávání zatížení – příchozí nebo Kubernetes služby typu: LoadBalancer). V tomto případě je příchozí provoz vykladače zatížení přijat prostřednictvím veřejné IP adresy, ale zpáteční cesta prochází privátní IP adresou brány firewall. Vzhledem k tomu, že brána firewall je stavová, zahodí vracející se paket, protože brána firewall si není vědoma zavedené relace. Informace o integraci azure firewall u příchozího přenosu dat nebo vyrovnávání zatížení služby najdete v [tématu Integrace azure firewallu se standardním službou Provynamání zatížení Azure](https://docs.microsoft.com/azure/firewall/integrate-lb).
> Přenosy pro port TCP 9000, port TCP 22 a port UDP 1194 můžete uzamknout pomocí síťového pravidla mezi ip adresami pracovního uzlu e-výstupního pracovního procesu a IP adresou serveru ROZHRANÍ API.

V AKS existují dvě sady portů a adres:

* [Požadované porty a adresa pro clustery AKS](#required-ports-and-addresses-for-aks-clusters) podrobně popisuje minimální požadavky na povolený odchozí provoz.
* [Volitelné doporučené adresy a porty pro clustery AKS](#optional-recommended-addresses-and-ports-for-aks-clusters) nejsou vyžadovány pro všechny scénáře, ale integrace s jinými službami, jako je Azure Monitor, nebude fungovat správně. Projděte si tento seznam volitelných portů a hlavních kvalifikovaných názevů nápravných spojů a autorizujte všechny služby a součásti používané v clusteru AKS.

> [!NOTE]
> Omezení odchozího přenosu funguje pouze v nových clusterech AKS. U existujících clusterů [proveďte operaci upgradu clusteru][aks-upgrade] pomocí příkazu `az aks upgrade` před omezením odchozího přenosu.

## <a name="required-ports-and-addresses-for-aks-clusters"></a>Požadované porty a adresy pro clustery AKS

Pro cluster AKS jsou vyžadována následující odchozí porty / pravidla sítě:

* Port TCP *443*
* TCP [IPAddrOfYourAPIServer]:443 je vyžadováno, pokud máte aplikaci, která potřebuje mluvit se serverem API.  Tuto změnu lze nastavit po vytvoření clusteru.
* TCP port *9000*, TCP port *22* a UDP port *1194* pro přední pod tunelu pro komunikaci s koncem tunelu na serveru API.
    * Chcete-li získat konkrétnější, naleznete **.hcp.\< umístění\>.azmk8s.io* a **.tun.\< umístění\>.azmk8s.io* adresy v následující tabulce.
* UDP port *123* pro synchronizaci času protokolu NTP (Network Time Protocol) (linuxové uzly).
* Port UDP *53* pro DNS je také vyžadován, pokud máte pody, které mají přímý přístup k serveru API.

Jsou vyžadována následující pravidla fqdn / aplikace:

> [!IMPORTANT]
> ***.blob.core.windows.net a aksrepos.azurecr.io** již nejsou vyžadována pravidla vícenežové číslo nkupy pro uzamčení odchozího přenosu.  U existujících clusterů [proveďte operaci upgradu clusteru][aks-upgrade] pomocí příkazu `az aks upgrade` k odebrání těchto pravidel.

> [!IMPORTANT]
> *.cdn.mscr.io byl nahrazen *.data.mcr.microsoft.com pro oblasti veřejného cloudu Azure. Změny se projeví až po inovovat existujícími pravidly brány firewall.

- Azure Globální

| FQDN                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *.hcp. \<umístění\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| *.tun. \<umístění\>.azmk8s.io | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| *.cdn.mscr.io       | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí Azure Content Delivery Network (CDN). |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je vyžadována pro přístup k bitovým kopiím v registru Microsoft Container Registry (MCR). Tento registr obsahuje obrázky/grafy první strany (například moby atd.) potřebné pro fungování clusteru během upgradu a škálování clusteru |
| *.data.mcr.microsoft.com             | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí pro doručování obsahu Azure (CDN). |
| management.azure.com       | HTTPS:443 | Tato adresa je vyžadována pro operace Kubernetes GET/PUT. |
| login.microsoftonline.com  | HTTPS:443 | Tato adresa je vyžadována pro ověřování služby Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa je vyžadována pro synchronizaci času NTP na uzlech Linuxu. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků společnosti Microsoft používané pro operace *apt-get* uložené v mezipaměti.  Příkladbalíčky patří Moby, PowerShell a Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Tato adresa je pro úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

- Azure China 21Vianet

| FQDN                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *.hcp. \<umístění\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| *.tun. \<umístění\>.cx.prod.service.azk8s.cn | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| *.azk8s.cn        | HTTPS:443 | Tato adresa je nutná ke stažení požadovaných binárních souborů a obrázků|
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je vyžadována pro přístup k bitovým kopiím v registru Microsoft Container Registry (MCR). Tento registr obsahuje obrázky/grafy první strany (například moby atd.) potřebné pro fungování clusteru během upgradu a škálování clusteru |
| *.cdn.mscr.io       | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí Azure Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí pro doručování obsahu Azure (CDN). |
| management.chinacloudapi.cn       | HTTPS:443 | Tato adresa je vyžadována pro operace Kubernetes GET/PUT. |
| login.chinacloudapi.cn  | HTTPS:443 | Tato adresa je vyžadována pro ověřování služby Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa je vyžadována pro synchronizaci času NTP na uzlech Linuxu. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků společnosti Microsoft používané pro operace *apt-get* uložené v mezipaměti.  Příkladbalíčky patří Moby, PowerShell a Azure CLI. |

- Azure Government

| FQDN                       | Port      | Použití      |
|----------------------------|-----------|----------|
| *.hcp. \<umístění\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| *.tun. \<umístění\>.cx.aks.containerservice.azure.us | HTTPS:443, TCP:22, TCP:9000, UDP:1194 | Tato adresa je vyžadována pro komunikaci serveru < > rozhraní API. Nahraďte * \<umístění\> * oblastí, kde je nasazen cluster AKS. |
| mcr.microsoft.com          | HTTPS:443 | Tato adresa je vyžadována pro přístup k bitovým kopiím v registru Microsoft Container Registry (MCR). Tento registr obsahuje obrázky/grafy první strany (například moby atd.) potřebné pro fungování clusteru během upgradu a škálování clusteru |
|*.cdn.mscr.io              | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí Azure Content Delivery Network (CDN). |
| *.data.mcr.microsoft.com             | HTTPS:443 | Tato adresa je vyžadována pro úložiště MCR podporované sítí pro doručování obsahu Azure (CDN). |
| management.usgovcloudapi.net       | HTTPS:443 | Tato adresa je vyžadována pro operace Kubernetes GET/PUT. |
| login.microsoftonline.us  | HTTPS:443 | Tato adresa je vyžadována pro ověřování služby Azure Active Directory. |
| ntp.ubuntu.com             | UDP:123   | Tato adresa je vyžadována pro synchronizaci času NTP na uzlech Linuxu. |
| packages.microsoft.com     | HTTPS:443 | Tato adresa je úložiště balíčků společnosti Microsoft používané pro operace *apt-get* uložené v mezipaměti.  Příkladbalíčky patří Moby, PowerShell a Azure CLI. |
| acs-mirror.azureedge.net      | HTTPS:443 | Tato adresa je pro úložiště potřebné k instalaci požadovaných binárních souborů, jako je kubenet a Azure CNI. |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>Volitelné doporučené adresy a porty pro clustery AKS

Následující odchozí porty / síťová pravidla jsou pro cluster AKS volitelná:

Pro správné fungování clusterů AKS se doporučují následující pravidla fqdn / aplikace:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| security.ubuntu.com, azure.archive.ubuntu.com, changelogs.ubuntu.com | HTTP:80   | Tato adresa umožňuje uzlům clusteru Linux stahovat požadované opravy zabezpečení a aktualizace. |

## <a name="required-addresses-and-ports-for-gpu-enabled-aks-clusters"></a>Požadované adresy a porty pro clustery AKS s povoleným grafickým procesorem

Pro clustery AKS, které mají povolenou grafickou procesorovou skupinu, jsou vyžadována následující pravidla pro kvalifikovaný název/ zápis/do povanci / pravidla aplikací:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| nvidia.github.io | HTTPS:443 | Tato adresa se používá pro správnou instalaci a provoz ovladače na uzlech založených na GPU. |
| us.download.nvidia.com | HTTPS:443 | Tato adresa se používá pro správnou instalaci a provoz ovladače na uzlech založených na GPU. |
| apt.dockerproject.org | HTTPS:443 | Tato adresa se používá pro správnou instalaci a provoz ovladače na uzlech založených na GPU. |

## <a name="required-addresses-and-ports-with-azure-monitor-for-containers-enabled"></a>Požadované adresy a porty s povoleným Azure Monitorem pro kontejnery

Následující pravidla plně kvalifikovaných názevů nebo aplikací jsou vyžadována pro clustery AKS, které mají povolený Azure Monitor pro kontejnery:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | HTTPS:443    | Toto je pro správné metriky a monitorování telemetrie pomocí Azure Monitor. |
| *.ods.opinsights.azure.com    | HTTPS:443    | To se používá Azure Monitor pro ingestování dat analýzy protokolů. |
| *.oms.opinsights.azure.com | HTTPS:443 | Tato adresa se používá omsagent, který se používá k ověření služby log analytics. |
|*.microsoftonline.com | HTTPS:443 | Používá se pro ověřování a odesílání metrik azure monitoru. |
|*.monitoring.azure.com | HTTPS:443 | Používá se k odesílání dat metriky azure monitoru. |

## <a name="required-addresses-and-ports-with-azure-dev-spaces-enabled"></a>Požadované adresy a porty s povoleným Azure Dev Spaces

Následující pravidla fQDN / aplikace jsou vyžadována pro clustery AKS, které mají povolené Azure Dev Spaces:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Tato adresa se používá k vytažení linuxových alpine a dalších bitových kopií Azure Dev Spaces. |
| gcr.io | HTTP:443 | Tato adresa slouží k tažení snímků kormidla/kultivátoru |
| storage.googleapis.com | HTTP:443 | Tato adresa slouží k tažení snímků kormidla/kultivátoru |
| azds-\<guid\>. \<umístění\>.azds.io | HTTPS:443 | Komunikace s back-endovými službami Azure Dev Spaces pro váš řadič. Přesný hlavní název sítě souborů lze nalézt v poli "dataplaneFqdn" v %USERPROFILE%\.azds\settings.json |

## <a name="required-addresses-and-ports-for-aks-clusters-with-azure-policy-in-public-preview-enabled"></a>Požadované adresy a porty pro clustery AKS s povolenou zásadou Azure (ve verzi Public Preview)

> [!CAUTION]
> Některé z níže uvedených funkcí jsou ve verzi Preview.  Návrhy v tomto článku se mohou změnit, protože funkce se přesune do fáze public preview a budoucí verze.

Následující pravidla fQDN / aplikace jsou vyžadována pro clustery AKS, které mají povolené zásady Azure.

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| gov-prod-policy-data.trafficmanager.net | HTTPS:443 | Tato adresa se používá pro správné fungování zásad Azure. (v současné době ve verzi preview v AKS) |
| raw.githubusercontent.com | HTTPS:443 | Tato adresa se používá k vyžádání předdefinovaných zásad z GitHubu, aby bylo zajištěno správné fungování zásad Azure. (v současné době ve verzi preview v AKS) |
| *.gk. \<umístění\>.azmk8s.io | HTTPS:443    | Doplněk zásad Azure, který bude mluvit s koncovým bodem auditu Gatekeeperu spuštěným na hlavním serveru, aby získal výsledky auditu. |
| dc.services.visualstudio.com | HTTPS:443 | Doplněk zásad Azure, který odesílá telemetrická data do koncového bodu přehledů aplikací. |

## <a name="required-by-windows-server-based-nodes-in-public-preview-enabled"></a>Vyžadované uzly založenými na systému Windows Server (ve verzi Public Preview) jsou povoleny

> [!CAUTION]
> Některé z níže uvedených funkcí jsou ve verzi Preview.  Návrhy v tomto článku se mohou změnit, protože funkce se přesune do fáze public preview a budoucí verze.

Pro clustery AKS založené na systému Windows Server jsou vyžadována následující pravidla pro vytvoření kvalifikovaných názevů FQDN / pravidel aplikací:

| FQDN                                    | Port      | Použití      |
|-----------------------------------------|-----------|----------|
| onegetcdn.azureedge.net, winlayers.blob.core.windows.net, winlayers.cdn.mscr.io, go.microsoft.com | HTTPS:443 | Instalace binárních souborů souvisejících se systémem Windows |
| mp.microsoft.com,<span></span>www.msftconnecttest.com, ctldl.windowsupdate.com | HTTP:80 | Instalace binárních souborů souvisejících se systémem Windows |
| kms.core.windows.net | TCP:1688 | Instalace binárních souborů souvisejících se systémem Windows |


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jaké porty a adresy povolit, pokud omezíte odchozí provoz pro cluster. Můžete také definovat, jak pody samy o sobě mohou komunikovat a jaká omezení mají v rámci clusteru. Další informace naleznete v [tématu Zabezpečení provozu mezi pody pomocí zásad sítě v AKS][network-policy].

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
