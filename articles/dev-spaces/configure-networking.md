---
title: Konfigurace sítě pro Azure Dev Spaces v různých síťových topologii
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Popisuje požadavky na sítě pro spuštění Azure Dev Spaces ve službách Azure Kubernetes Services
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery, CNI, kubenet, SDN, síť
ms.openlocfilehash: 82d046aa36fe9caf6337aa7f58ca0db525062283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240571"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurace sítě pro Azure Dev Spaces v různých síťových topologii

Azure Dev Spaces běží na clusterech Služby Azure Kubernetes (AKS) s výchozí konfigurací sítě. Pokud chcete změnit konfiguraci sítě clusteru AKS, jako je například umístění clusteru za bránu firewall, použití skupin zabezpečení sítě nebo použití zásad sítě, je třeba zahrnout další důležité informace pro spuštění Azure Dev Spaces.

![Konfigurace virtuální sítě](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfigurace virtuální sítě nebo podsítě

Cluster AKS může mít jinou konfiguraci virtuální sítě nebo podsítě, která omezuje příchozí nebo odchozí přenosy pro cluster AKS. Cluster může být například za bránou firewall, jako je například Brána Azure Firewall, nebo můžete použít skupiny zabezpečení sítě nebo vlastní role pro omezení síťového provozu.

Azure Dev Spaces má určité požadavky pro *příchozí a odchozí síťový* provoz, jakož i příchozí přenos y *pouze* přenosy dat. Pokud používáte Azure Dev Spaces v clusteru AKS s virtuální sítí nebo konfigurací podsítě, která omezuje provoz pro váš cluster AKS, musíte postupovat pouze podle následujících požadavků na příchozí přenosy a odchozí přenosy, aby se azure dev spaces správně fungovat.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Požadavky na přenos v síti příchozích a odchozích přenosů

Azure Dev Spaces potřebuje příchozí a odchozí provoz pro následující sítě souborů FQDN:

| FQDN                       | Port       | Použití      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Vyžádat impulzy dockeru pro Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Stažení imitace kormidel pro Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Stažení imitace kormidel pro Azure Dev Spaces |
| azds-*.azds.io             | HTTPS: 443 | Pokud můžete komunikovat s back-endovými službami Azure Dev Spaces pro řadič Azure Dev Spaces. Přesný úplně kvalifikovaný kvalifikovaný přístup k datům lze nalézt v *datovém letadleFqdn*`USERPROFILE\.azds\settings.json` |

Aktualizujte bránu firewall nebo konfiguraci zabezpečení tak, aby síťový provoz umožňoval do a ze všech výše uvedených skupinových skupin ových kvantiten. Pokud například k zabezpečení sítě používáte bránu firewall, měly by být výše uvedené názvy domén přidány do pravidla aplikace brány firewall, aby byl povolen provoz do a z těchto domén.

### <a name="ingress-only-network-traffic-requirements"></a>Příchozí přenos pouze požadavky na provoz v síti

Azure Dev Spaces poskytuje směrování na úrovni oboru názvů Kubernetes a také veřejný přístup ke službám pomocí vlastního plně přístupného osobního názvu. Aby obě tyto funkce fungovaly, aktualizujte bránu firewall nebo konfiguraci sítě, abyste povolili veřejný příchozí přenos dat na externí IP adresu řadiče příchozího přenosu dat Azure Dev Spaces ve vašem clusteru. Případně můžete vytvořit [interní systém vyrovnávání zatížení][aks-internal-lb] a přidat pravidlo NAT do brány firewall a přeložit veřejnou IP adresu brány firewall do IP adresy interního systému vyrovnávání zatížení. Můžete také použít [traefik][traefik-ingress] nebo [NGINX][nginx-ingress] k vytvoření vlastního řadiče příchozího přenosu dat.

## <a name="aks-cluster-network-requirements"></a>Požadavky na síť clusteru AKS

AKS umožňuje používat [zásady sítě][aks-network-policies] k řízení příchozího přenosu dat a odchozího přenosu mezi pody v clusteru, jakož i odchozí provoz z podu. Azure Dev Spaces má určité požadavky pro *příchozí a odchozí síťový* provoz, jakož i příchozí přenos y *pouze* přenosy dat. Pokud používáte Azure Dev Spaces v clusteru AKS se zásadami sítě AKS, musíte postupovat pouze podle následujících požadavků na příchozí přenosy a odchozí přenosy, aby azure dev spaces fungovalsprávně.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Požadavky na přenos v síti příchozích a odchozích přenosů

Azure Dev Spaces umožňuje komunikovat přímo s podv dev prostoru ve vašem clusteru pro ladění. Aby tato funkce fungovala, přidejte zásady sítě, které umožňují příchozí a odchozí komunikaci s IP adresami infrastruktury Azure Dev Spaces, které [se liší podle oblasti][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Příchozí přenos pouze požadavky na provoz v síti

Azure Dev Spaces poskytuje směrování mezi pody napříč obory názvů. Například obory názvů s povolenou funkcí Azure Dev Spaces mohou mít vztah nadřazený/podřízený, který umožňuje směrování síťového provozu mezi pody mezi nadřazenými a podřízenými obory názvů. Azure Dev Spaces také zveřejňuje koncové body služby pomocí vlastního vícenežového přístupu k souborům. Chcete-li nakonfigurovat různé způsoby vystavení služeb a vliv na směrování na úrovni oboru názvů, viz [Použití různých možností koncového bodu][endpoint-options].

## <a name="using-azure-cni"></a>Použití Azure CNI

Ve výchozím nastavení jsou clustery AKS nakonfigurovány tak, aby používaly [kubenet][aks-kubenet] pro sítě, které fungují s Azure Dev Spaces. Cluster AKS můžete také nakonfigurovat tak, aby používal [rozhraní Azure Container Networking Interface (CNI).][aks-cni] Pokud chcete v clusteru AKS používat Azure Dev Spaces s Azure CNI, povolte virtuální síti a adresním prostorům podsítě až 10 privátních IP adres pro pody nasazené azure dev spaces. Další podrobnosti o povolení privátních IP adres jsou k dispozici v [dokumentaci AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Použití oblastí IP autorizovaných serveru API

Clustery AKS umožňují konfigurovat další zabezpečení, které omezuje, která adresa IP může s vašimi clustery pracovat, například pomocí vlastních virtuálních sítí nebo [zabezpečení mnoství přístupk a zabezpečení serveru API pomocí autorizovaných rozsahů IP adres][aks-ip-auth-ranges]. Chcete-li při vytváření clusteru používat Azure Dev Spaces při [použití][aks-ip-auth-range-create] tohoto dalšího zabezpečení, musíte [povolit další rozsahy založené na vaší oblasti][dev-spaces-ip-auth-range-regions]. Můžete také [aktualizovat][aks-ip-auth-range-update] existující cluster, který umožní tyto další rozsahy. Je také nutné povolit IP adresu všech vývojových počítačů, které se připojují k clusteru AKS pro ladění pro připojení k serveru rozhraní API.

## <a name="using-aks-private-clusters"></a>Použití privátních clusterů AKS

V současné době Azure Dev Spaces není podporována s [AKS privátní clustery][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Použití různých možností koncového bodu

Azure Dev Spaces má možnost zpřístupnit koncové body pro vaše služby spuštěné na AKS. Při povolení Azure Dev Spaces v clusteru máte následující možnosti konfigurace typu koncového bodu pro váš cluster:

* *Veřejný* koncový bod, který je výchozí, nasazuje řadič příchozího přenosu dat s veřejnou IP adresou. Veřejná IP adresa je registrována ve službě DNS clusteru, což umožňuje veřejný přístup k vašim službám pomocí adresy URL. Tuto adresu URL `azds list-uris`můžete zobrazit pomocí aplikace .
* *Soukromý* koncový bod nasazuje řadič příchozího přenosu dat s privátní IP adresou. S privátní IP adresou je nástroj pro vyrovnávání zatížení vašeho clusteru přístupný pouze z virtuální sítě clusteru. Privátní IP adresa nástroje pro vyrovnávání zatížení je registrována na dns clusteru, takže služby uvnitř virtuální sítě clusteru jsou přístupné pomocí adresy URL. Tuto adresu URL `azds list-uris`můžete zobrazit pomocí aplikace .
* Nastavení *žádné* pro možnost koncového bodu způsobí, že žádný řadič příchozího přenosu dat, které mají být nasazeny. Bez nasazeného řadiče příchozího přenosu dat nebudou [fungovat možnosti směrování Azure Dev Spaces.][dev-spaces-routing] Volitelně můžete implementovat vlastní řešení řadiče příchozího přenosu dat pomocí [traefik][traefik-ingress] nebo [NGINX][nginx-ingress], které umožní možnosti směrování znovu pracovat.

Chcete-li nakonfigurovat možnost koncového bodu, použijte *-e* nebo *--endpoint* při povolení Azure Dev Spaces ve vašem clusteru. Například:

> [!NOTE]
> Možnost koncového bodu vyžaduje, abyste spouštěli Azure CLI verze 2.2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Požadavky na klienty

Azure Dev Spaces používá nástroje na straně klienta, jako je například rozšíření Azure Dev Spaces CLI, rozšíření kódu Visual Studio a rozšíření Visual Studio, ke komunikaci s clusterem AKS pro ladění. Chcete-li použít nástroje Azure Dev Spaces na straně klienta, povolte provoz z vývojových počítačů do domény *AZDs-\*.azds.io.* Přesný hlavní přístupkovou `USERPROFILE\.azds\settings.json` dobu naleznete v *tématu dataplaneFqdn* in. Pokud používáte [rozsahy IP adres autorizované ho serveru API][auth-range-section], musíte také povolit IP adresu všech vývojových počítačů, které se připojují k vašemu clusteru AKS, pro ladění pro připojení k serveru rozhraní API.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak Azure Dev Spaces pomáhá vyvíjet složitější aplikace napříč více kontejnery a jak můžete zjednodušit vývoj spolupráce pomocí práce s různými verzemi nebo větvemi kódu v různých prostorech.

> [!div class="nextstepaction"]
> [Vývoj týmu v Azure Dev Spaces][team-quickstart]

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
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md