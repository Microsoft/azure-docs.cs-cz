---
title: Doporučené postupy pro síťové prostředky
titleSuffix: Azure Kubernetes Service
description: Seznamte se s doporučenými postupy operátora clusteru pro prostředky virtuálních sítí a připojení ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: c8aee9967e09d2ae8bec3ee170756d8d22de0fe4
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668211"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro připojení k síti a zabezpečení ve službě Azure Kubernetes Service (AKS)

Při vytváření a správě clusterů ve službě Azure Kubernetes Service (AKS) poskytujete připojení k síti pro vaše uzly a aplikace. Tyto síťové prostředky zahrnují rozsahy IP adres, vyrovnávání zatížení a řadiče příchozích dat. Chcete-li zachovat vysokou kvalitu služeb pro vaše aplikace, je třeba naplánovat a nakonfigurovat tyto prostředky.

Tento článek osvědčených postupů se zaměřuje na připojení k síti a zabezpečení pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Porovnání síťových režimů kubenet a Azure CNI v AKS
> * Plán pro požadované IP adresování a připojení
> * Distribuce provozu pomocí vykladačů zatížení, řadičů příchozích dat nebo brány firewall webové aplikace (WAF)
> * Bezpečné připojení k uzlům clusteru

## <a name="choose-the-appropriate-network-model"></a>Zvolte vhodný síťový model

**Pokyny pro osvědčené postupy** – pro integraci s existujícími virtuálními sítěmi nebo místními sítěmi použijte azure cni sítě v AKS. Tento model sítě také umožňuje větší oddělení prostředků a ovládacích prvků v podnikovém prostředí.

Virtuální sítě poskytují základní připojení pro uzly AKS a zákazníky pro přístup k vašim aplikacím. Existují dva různé způsoby nasazení clusterů AKS do virtuálních sítí:

* **Kubenet networking** – Azure spravuje prostředky virtuální sítě při nasazení clusteru a používá plugin [kubenet][kubenet] Kubernetes.
* **Azure CNI networking** – nasazuje se do existující virtuální sítě a používá modul plug-in Kubernetes [(Azure Container Networking Interface).][cni-networking] Pody přijímají jednotlivé IP adresy, které mohou směrovat na jiné síťové služby nebo místní prostředky.

Rozhraní CNI (Container Networking Interface) je protokol neutrální pro dodavatele, který umožňuje modulu runtime kontejneru provádět požadavky poskytovateli sítě. Azure CNI přiřazuje IP adresy podům a uzlům a poskytuje funkce správy IP adres (IPAM) při připojení k existujícím virtuálním sítím Azure. Každý prostředek uzlu a podu obdrží IP adresu ve virtuální síti Azure a ke komunikaci s jinými prostředky nebo službami není potřeba žádné další směrování.

![Diagram znázorňující dva uzly s mosty, které se připojují k jedné virtuální síti Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Pro většinu produkčních nasazení byste měli používat azure cni sítě. Tento model sítě umožňuje oddělení řízení a správu prostředků. Z hlediska zabezpečení často chcete, aby různé týmy tyto prostředky spravovaly a zabezpečily. Azure CNI sítě umožňuje připojení k existující prostředky Azure, místní prostředky nebo jiné služby přímo prostřednictvím IP adres přiřazených ke každému podu.

Při použití sítě Azure CNI je prostředek virtuální sítě v samostatné skupině prostředků pro cluster AKS. Delegujte oprávnění pro instanční objekt Služby AKS pro přístup a správu těchto prostředků. Instanční objekt používaný clusterem AKS musí mít alespoň oprávnění [síťového přispěvatele](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti ve vaší virtuální síti. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) namísto použití předdefinované role přispěvatele sítě, jsou vyžadována následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Další informace o delegování hlavního serveru služeb AKS najdete v [tématu Delegování přístupu k jiným prostředkům Azure][sp-delegation].

Jako každý uzel a pod přijímat vlastní IP adresu, naplánovat rozsahy adres pro podsítě AKS. Podsíť musí být dostatečně velká, aby poskytovala adresy IP pro každý uzel, pody a síťové prostředky, které nasazujete. Každý cluster AKS musí být umístěn ve své vlastní podsíti. Chcete-li povolit připojení k místním nebo partnerským sítím v Azure, nepoužívejte rozsahy IP adres, které se překrývají s existujícími síťovými prostředky. Existují výchozí omezení počtu podů, které každý uzel běží s kubenet a Azure CNI sítě. Ke zpracování horizontálních navýšení kapacity událostí nebo upgradů clusteru potřebujete také další adresy IP, které jsou k dispozici pro použití v přiřazené podsíti. Tento další adresní prostor je obzvláště důležitý, pokud používáte kontejnery Windows Server (aktuálně ve verzi Preview v AKS), protože tyto fondy uzlů vyžadují upgrade pro použití nejnovějších oprav zabezpečení. Další informace o uzlech systému Windows Server naleznete [v tématu Upgrade fondu uzlů v systému AKS][nodepool-upgrade].

Pokud chcete vypočítat požadovanou IP adresu, [přečtěte si informace o konfiguraci sítí Azure CNI v AKS][advanced-networking].

### <a name="kubenet-networking"></a>Síť Kubenet

Přestože kubenet nevyžaduje nastavení virtuálních sítí před nasazením clusteru, existují nevýhody:

* Uzly a pody jsou umístěny v různých podsítích IP. Uživatelem definované směrování (UDR) a předávání IP se používá ke směrování provozu mezi pody a uzly. Toto další směrování může snížit výkon sítě.
* Připojení k existujícím místním sítím nebo partnerský vztah k jiným virtuálním sítím Azure mohou být složitá.

Kubenet je vhodný pro malé vývojové nebo testovací úlohy, protože není třeba vytvářet virtuální síť a podsítě odděleně od clusteru AKS. Jednoduché weby s nízkým provozem nebo pro zvedání a přesouvání úloh do kontejnerů mohou také těžit z jednoduchosti clusterů AKS nasazených v sítích kubenet. Pro většinu produkčních nasazení byste měli plánovat a používat sítě Azure CNI. Můžete také [nakonfigurovat vlastní rozsahy IP adres a virtuální sítě pomocí kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuce příchozího přenosu dat

**Pokyny pro osvědčené postupy** – k distribuci přenosů HTTP nebo HTTPS do vašich aplikací použijte prostředky příchozího přenosu dat a řadiče. Řadiče příchozích dat poskytují další funkce prostředpou běžného zařízení pro vyrovnávání zatížení Azure a lze je spravovat jako nativní prostředky Kubernetes.

Azure vyrovnávání zatížení můžete distribuovat provoz zákazníků do aplikací ve vašem clusteru AKS, ale je omezená v tom, co chápe o tomto provozu. Prostředek pro vyrovnávání zatížení pracuje ve vrstvě 4 a distribuuje přenosy na základě protokolu nebo portů. Většina webových aplikací, které používají HTTP nebo HTTPS by měl používat Prostředky příchozího přenosu dat Kuberenetes a řadiče, které pracují na vrstvě 7. Příchozí přenos může distribuovat provoz na základě adresy URL aplikace a zpracovávat ukončení TLS/SSL. Tato možnost také snižuje počet adres IP, které zveřejňujete a mapujete. Pomocí systému pro vyrovnávání zatížení každá aplikace obvykle potřebuje veřejnou IP adresu přiřazenou a mapovanou na službu v clusteru AKS. S ingress prostředku jedné adresy IP můžete distribuovat provoz do více aplikací.

![Diagram znázorňující tok přenosů příchozích dat v clusteru AKS](media/operator-best-practices-network/aks-ingress.png)

 Existují dvě součásti pro příchozí přenos:

 * *Zdroj*příchozího přenosu dat a
 * Řadič příchozího *přenosu* dat

Ingress prostředek je manifest `kind: Ingress` YAML, který definuje hostitele, certifikáty a pravidla pro směrování provozu do služeb, které běží v clusteru AKS. Následující příklad Manifest UAML by distribuovat provoz pro *myapp.com* do jedné ze dvou služeb, *blogservice* nebo *storeservice*. Zákazník je přesměrován na jednu nebo druhou službu na základě adresy URL, ke které přistupuje.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Řadič příchozího přenosu dat je daemon, který běží na uzlu AKS a sleduje příchozí požadavky. Provoz je pak distribuován na základě pravidel definovaných v prostředku příchozího přenosu dat. Nejběžnější řadič příchozího přenosu dat je založen na [NGINX]. Služba AKS vás neomezuje na konkrétní ovladač, takže můžete používat další ovladače, například [Contour][contour], [HAProxy][haproxy]nebo [Traefik][traefik].

Řadiče příchozího přenosu dat musí být naplánovány na uzlu Linux. Uzly systému Windows Server (aktuálně ve verzi preview v AKS) by neměly spouštět řadič příchozího přenosu dat. Pomocí voliče uzlů v manifestu YAML nebo helmgrafu nasazení označují, že prostředek by měl běžet na uzlu založeném na Linuxu. Další informace naleznete [v tématu Usenonoseseři k řízení, kde jsou pody naplánovány v AKS][concepts-node-selectors].

Existuje mnoho scénářů pro příchozí přenos dat, včetně následujících návodů:

* [Vytvoření základního řadiče příchozího přenosu dat s externím připojením k síti][aks-ingress-basic]
* [Vytvoření řadiče příchozího přenosu dat, který používá interní, privátní síť a adresu IP][aks-ingress-internal]
* [Vytvoření řadiče příchozího přenosu dat, který používá vlastní certifikáty TLS][aks-ingress-own-tls]
* Vytvoření řadiče příchozího přenosu dat, který používá let's Encrypt k automatickému generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Bezpečný provoz pomocí brány firewall webové aplikace (WAF)

**Pokyny pro osvědčené postupy** – Chcete-li vyhledat příchozí provoz pro potenciální útoky, použijte bránu firewall webové aplikace (WAF), jako je [Barracuda WAF pro Azure][barracuda-waf] nebo Azure Application Gateway. Tyto pokročilejší síťové prostředky mohou také směrovat provoz za připojení http a https nebo základní ukončení Protokolu SSL.

Řadič příchozího přenosu dat, který distribuuje provoz do služeb a aplikací, je obvykle prostředek Kubernetes v clusteru AKS. Řadič běží jako daemon na uzlu AKS a spotřebovává některé prostředky uzlu, jako je procesor, paměť a šířku pásma sítě. Ve větších prostředích často chcete převést část tohoto směrování provozu nebo ukončení TLS na síťový prostředek mimo cluster AKS. Chcete také skenovat příchozí provoz pro potenciální útoky.

![Brána firewall webových aplikací (WAF), jako je Azure App Gateway, může chránit a distribuovat provoz pro váš cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Brána firewall webové aplikace (WAF) poskytuje další vrstvu zabezpečení filtrováním příchozích přenosů. Open Web Application Security Project (OWASP) poskytuje sadu pravidel pro sledování útoků, jako je skriptování mezi weby nebo otrava souborů cookie. [Azure Application Gateway][app-gateway] (aktuálně ve verzi preview v AKS) je WAF, který lze integrovat s clustery AKS poskytovat tyto funkce zabezpečení, než provoz dosáhne clusteru AKS a aplikací. Tyto funkce také vykonávají i jiná řešení jiných výrobců, takže můžete i nadále používat stávající investice nebo odborné znalosti v daném produktu.

Vyrovnávání zatížení nebo ingress prostředky nadále spuštěny v clusteru AKS dále upřesnit rozložení provozu. Brána aplikací může být centrálně spravována jako řadič příchozího přenosu dat s definicí prostředků. Chcete-li začít, [vytvořte řadič příchozího přenosu dat brány aplikace][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Řízení toku provozu pomocí zásad sítě

**Pokyny pro osvědčené postupy** – pomocí zásad sítě povolte nebo odepřete provoz podům. Ve výchozím nastavení je veškerý provoz mezi pody v rámci clusteru povolen. Pro lepší zabezpečení definujte pravidla, která omezují komunikaci podů.

Zásady sítě je funkce Kubernetes, která umožňuje řídit tok provozu mezi pody. Můžete povolit nebo odepřít provoz na základě nastavení, jako jsou přiřazené popisky, obor názvů nebo port provozu. Použití zásad sítě poskytuje cloudově nativní způsob řízení toku provozu. Jako pody jsou dynamicky vytvářeny v clusteru AKS, požadované zásady sítě lze automaticky použít. Nepoužívejte skupiny zabezpečení sítě Azure k řízení provozu pod-pod, použijte zásady sítě.

Chcete-li použít zásady sítě, musí být tato funkce povolena při vytváření clusteru AKS. Nelze povolit zásady sítě v existujícím clusteru AKS. Naplánujte si dopředu, abyste se ujistili, že povolíte zásady sítě v clusterech a můžete je použít podle potřeby. Zásady sítě by se měly používat pouze pro uzly a pody založené na Linuxu v AKS.

Zásady sítě se vytvoří jako prostředek Kubernetes pomocí manifestu YAML. Zásady jsou použity pro definované pody, pak příchozí nebo odchozí pravidla definovat, jak může tok provozu. Následující příklad použije zásady sítě pro pody s *aplikací: back-endový* popisek, který se na ně vztahuje. Pravidlo příchozího přenosu dat pak umožňuje pouze provoz z podů s *aplikací: front-end* popisek:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Pokud chcete začít se zásadami, přečtěte si informace [o zabezpečeném provozu mezi pody pomocí síťových zásad ve službě Azure Kubernetes Service (AKS).][use-network-policies]

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bezpečné připojení k uzlům prostřednictvím hostitele bašty

**Pokyny pro osvědčené postupy** – nezveřejňujte vzdálené připojení k uzlům AKS. Vytvořte hostitele bastionu nebo pole skoku ve virtuální síti pro správu. Pomocí hostitele bastionu můžete bezpečně směrovat provoz do clusteru AKS na úlohy vzdálené správy.

Většinu operací v AKS lze provést pomocí nástrojů pro správu Azure nebo prostřednictvím serveru Rozhraní API Kubernetes. Uzly AKS nejsou připojeny k veřejnému internetu a jsou k dispozici pouze v privátní síti. Chcete-li se připojit k uzlům a provádět údržbu nebo řešení problémů, směrovat připojení přes hostitele bastionu nebo skokbox. Tento hostitel by měl být v samostatné virtuální síti pro správu, která je bezpečně propojena s virtuální sítí clusteru AKS.

![Připojení k uzlům AKS pomocí hostitele bašty nebo skokové pole](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Síť pro správu hostitele bašty by měla být také zabezpečena. Pomocí brány [Azure ExpressRoute][expressroute] nebo [VPN][vpn-gateway] se můžete připojit k místní síti a řídit přístup pomocí skupin zabezpečení sítě.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na připojení k síti a zabezpečení. Další informace o základech sítě v Kubernetes najdete v [tématu Síťové koncepty pro aplikace ve službě Azure Kubernetes Service (AKS).][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool