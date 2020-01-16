---
title: Konfigurace sítě pro Azure Dev Spaces v různých topologiích sítě
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Popisuje požadavky na síť pro provozování Azure Dev Spaces ve službě Azure Kubernetes.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers, CNI, kubenet, SDN, Network
ms.openlocfilehash: 9e32e3b65451dceefaeeaf7faed7c8337797e0b8
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044993"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurace sítě pro Azure Dev Spaces v různých topologiích sítě

Azure Dev Spaces běží na clusterech AKS (Azure Kubernetes Service) s výchozí konfigurací sítě. Pokud chcete změnit konfiguraci sítě pro cluster AKS, jako je například uvedení clusteru za bránou firewall, používání skupin zabezpečení sítě nebo použití zásad sítě, je nutné přidat další požadavky na spuštění Azure Dev Spaces.

![Konfigurace virtuální sítě](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfigurace virtuálních sítí nebo podsítí

Cluster AKS může mít jinou konfiguraci virtuální sítě nebo podsítě pro omezení příchozího nebo odchozího provozu clusteru AKS. Cluster může být například za bránou firewall, například Azure Firewall, nebo můžete použít skupiny zabezpečení sítě nebo vlastní role pro omezení síťového provozu.

Azure Dev Spaces má určité požadavky na příchozí *a odchozí* síťový provoz a také provoz *jenom* příchozí přenos dat. Pokud používáte Azure Dev Spaces v clusteru AKS s konfigurací virtuální sítě nebo podsítě, která omezuje provoz pro cluster AKS, musíte postupovat podle následujících požadavků na příchozí přenos dat, příchozí a odchozí přenos dat, aby bylo Azure Dev Spaces správně fungovat.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Požadavky na příchozí a odchozí provoz sítě

Azure Dev Spaces potřebuje příchozí a odchozí provoz pro tyto plně kvalifikované názvy domény:

| PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY                       | Port       | Použití      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Načtení imagí Docker pro Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Vyžádání imagí Helm pro Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Vyžádání imagí Helm pro Azure Dev Spaces |
| azds-*. azds. IO             | HTTPS: 443 | Pro komunikaci s Azure Dev Spaces back-end službami pro kontroler Azure Dev Spaces. Přesný plně kvalifikovaný název domény najdete v *dataplaneFqdn* v části `USERPROFILE\.azds\settings.json` |

Aktualizujte bránu firewall nebo konfiguraci zabezpečení tak, aby povolovaly síťový provoz do a ze všech výše uvedených plně kvalifikovaných názvů domén. Pokud například používáte bránu firewall k zabezpečení sítě, je třeba přidat výše uvedené plně kvalifikované názvy domény do aplikačního pravidla brány firewall, aby se povolil přenos do a z těchto domén.

### <a name="ingress-only-network-traffic-requirements"></a>Jenom příchozí požadavky na provoz v síti

Azure Dev Spaces poskytuje směrování na úrovni oboru názvů Kubernetes a také veřejný přístup ke službám pomocí vlastního plně kvalifikovaného názvu domény. Aby obě tyto funkce fungovaly, aktualizujte si bránu firewall nebo konfiguraci sítě, abyste umožnili veřejné příchozí přenosy na externí IP adresu řadiče pro Azure Dev Spaces příchozího přenosu v clusteru. Případně můžete vytvořit [interní nástroj pro vyrovnávání zatížení][aks-internal-lb] a přidat do brány firewall pravidlo překladu adres (NAT) k překladu veřejné IP adresy vaší brány firewall na IP adresu interního nástroje pro vyrovnávání zatížení. K vytvoření vlastního kontroleru příchozího přenosu dat můžete použít taky [traefik][traefik-ingress] nebo [Nginx][nginx-ingress] .

## <a name="aks-cluster-network-requirements"></a>AKS požadavky na síť clusteru

AKS umožňuje používat [zásady sítě][aks-network-policies] k řízení příchozího a odchozího provozu mezi lusky v clusteru a také přenosem dat z pod. Azure Dev Spaces má určité požadavky na příchozí *a odchozí* síťový provoz a také provoz *jenom* příchozí přenos dat. Pokud používáte Azure Dev Spaces v clusteru AKS se zásadami sítě AKS, musíte pro správné fungování Azure Dev Spaces postupovat podle následujících požadavků na příchozí a odchozí přenos dat.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Požadavky na příchozí a odchozí provoz sítě

Azure Dev Spaces vám umožní komunikovat přímo s podmnožinou v prostoru pro vývoj v clusteru pro účely ladění. Aby tato funkce fungovala, přidejte zásadu sítě, která umožňuje příchozí a odchozí komunikaci s IP adresami infrastruktury Azure Dev Spaces, která se [liší podle oblasti][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Jenom příchozí požadavky na provoz v síti

Azure Dev Spaces poskytuje směrování mezi lusky napříč obory názvů. Například obory názvů s povoleným Azure Dev Spaces mohou mít vztah nadřazenosti/podřízenosti, který umožňuje směrování síťového provozu mezi lusky napříč nadřazenými a podřízenými obory názvů. Aby tato funkce fungovala, přidejte zásadu sítě, která umožňuje provoz mezi obory názvů, do kterých se směruje síťový provoz, jako jsou například obory názvů nadřazených a podřízených objektů. Pokud je adaptér příchozího přenosu nasazený do oboru názvů *azds* , musí kontroler příchozích dat komunikovat s lusky, které jsou instrumentované v prostoru pro vývoj v Azure, v jiném oboru názvů. Aby kontroler příchozího provozu fungoval správně, musí být síťový provoz povolený z oboru názvů *azds* do oboru názvů, ve kterém se instrumentované lusky spouštějí.

## <a name="using-azure-cni"></a>Používání Azure CNI

Ve výchozím nastavení jsou clustery AKS nakonfigurované tak, aby používaly [kubenet][aks-kubenet] pro sítě, které fungují s Azure dev Spaces. Cluster AKS můžete také nakonfigurovat tak, aby používal [rozhraní CNI (Azure Container Networking Interface)][aks-cni]. Pokud chcete použít Azure Dev Spaces s Azure CNI v clusteru AKS, umožněte virtuální síti a adresní prostorům adresní prostory až 10 privátních IP adres pro lusky nasazené pomocí Azure Dev Spaces. Další podrobnosti o povolení privátních IP adres najdete v [dokumentaci k AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Použití rozsahů povolených IP adres serveru API

Clustery AKS umožňují nakonfigurovat další zabezpečení, které omezuje, která IP adresa může komunikovat s clustery, například pomocí vlastních virtuálních sítí nebo [zabezpečení přístupu k serveru rozhraní API pomocí autorizovaných rozsahů IP][aks-ip-auth-ranges]adres. Pokud chcete použít Azure Dev Spaces při použití tohoto dalšího zabezpečení při [vytváření][aks-ip-auth-range-create] clusteru, musíte [v závislosti na vaší oblasti zapnout další rozsah][dev-spaces-ip-auth-range-regions]. Můžete také [aktualizovat][aks-ip-auth-range-update] existující cluster, aby bylo možné tyto další rozsahy. Pro připojení k vašemu serveru API musíte taky u všech vývojových počítačů, které se připojují ke clusteru AKS, použít IP adresu pro účely ladění.

## <a name="using-aks-private-clusters"></a>Používání privátních clusterů AKS

V tuto chvíli se Azure Dev Spaces [privátním clusterům AKS][aks-private-clusters]nepodporuje.

## <a name="client-requirements"></a>Požadavky na klienty

Azure Dev Spaces používá klientské nástroje, jako je rozšíření CLI Azure Dev Spaces, Visual Studio Code rozšíření a rozšíření sady Visual Studio, ke komunikaci s clusterem AKS pro ladění. Chcete\*-li použít Azure Dev Spaces nástrojů na straně klienta, povolte provoz z vývojových počítačů do domény *azds.IO.* . Přesný plně kvalifikovaný název domény najdete v tématu *dataplaneFqdn* v `USERPROFILE\.azds\settings.json`. Pokud používáte [rozsahy IP adres autorizovaných serverem API][auth-range-section], musíte taky povolit IP adresu všech vývojových počítačů, které se připojují ke clusteru AKS, aby se mohly připojit k vašemu serveru API.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak zjednodušit vývoj díky práci s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj pro tým v Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md