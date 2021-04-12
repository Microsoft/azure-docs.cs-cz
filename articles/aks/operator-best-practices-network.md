---
title: Osvědčené postupy pro síťové prostředky
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro používání virtuálních síťových prostředků a připojení ve službě Azure Kubernetes (AKS) pro provozovatele clusteru.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104945"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro připojení k síti a zabezpečení ve službě Azure Kubernetes Service (AKS)

Když vytváříte a spravujete clustery ve službě Azure Kubernetes Service (AKS), zadáváte pro své uzly a aplikace síťové připojení. Mezi tyto síťové prostředky patří rozsahy IP adres, nástroje pro vyrovnávání zatížení a řadiče pro příchozí přenosy dat. Aby bylo možné zajistit vysokou kvalitu služeb pro vaše aplikace, musíte tyto prostředky strategize a nakonfigurovat.

Tento článek o osvědčených postupech se zaměřuje na připojení k síti a zabezpečení pro operátory clusterů. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Porovnejte kubenet a síťové režimy CNI (Azure Container Networking Interface) v AKS.
> * Naplánujte požadované přidělování IP adres a připojení.
> * Distribuce provozu pomocí nástrojů pro vyrovnávání zatížení, řadičů příchozího přenosu dat nebo brány firewall webových aplikací (WAF).
> * Připojte se bezpečně k uzlům clusteru.

## <a name="choose-the-appropriate-network-model"></a>Zvolit příslušný model sítě

> **Osvědčené postupy** 
> 
> Využijte Azure CNI Networking v AKS pro integraci se stávajícími virtuálními sítěmi nebo místními sítěmi. Tento model sítě umožňuje větší oddělení prostředků a ovládacích prvků v podnikovém prostředí.

Virtuální sítě poskytují základní konektivitu pro uzly AKS a zákazníkům, kteří budou mít přístup k vašim aplikacím. Existují dva různé způsoby, jak nasadit clustery AKS do virtuálních sítí:

* **Sítě Azure CNI**

    Nasadí se do virtuální sítě a použije modul plug-in [Azure CNI][cni-networking] Kubernetes. Lusky přijímají jednotlivé IP adresy, které se můžou směrovat na jiné síťové služby nebo místní prostředky.
* **Kubenet sítě**

    Azure spravuje prostředky virtuální sítě při nasazení clusteru a používá modul plug-in [kubenet][kubenet] Kubernetes.


V produkčních nasazeních jsou platné možnosti kubenet i Azure CNI.

### <a name="cni-networking"></a>CNI sítě

Azure CNI je protokol neutrální od dodavatele, který umožňuje modulu runtime kontejneru vydávat požadavky poskytovateli sítě. Přiřadí IP adresy v luskech a uzlech a poskytuje funkce správy IP adres (IPAM) jako připojení ke stávajícím virtuálním sítím Azure. Každý uzel a prostředek pod obdrží IP adresu ve službě Azure Virtual Network – pro komunikaci s dalšími prostředky a službami není potřeba žádné dodatečné směrování.

![Diagram znázorňující dva uzly s mostem připojujícím se každé virtuální síti Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Zejména síť Azure CNI pro produkční prostředí umožňuje oddělení řízení a správy prostředků. Z hlediska zabezpečení často budete chtít, aby tyto prostředky spravovali a zabezpečili různí týmy. Pomocí sítě Azure CNI se můžete připojit k existujícím prostředkům Azure, místním prostředkům nebo jiným službám přímo prostřednictvím IP adres přiřazených ke každému pod.

Pokud používáte síť Azure CNI, je prostředek virtuální sítě v samostatné skupině prostředků do clusteru AKS. Delegovat oprávnění pro identitu clusteru AKS pro přístup k těmto prostředkům a jejich správu. Identita clusteru používaná clusterem AKS musí mít alespoň oprávnění [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti v rámci vaší virtuální sítě. 

Pokud chcete místo používání předdefinované role přispěvatele sítě definovat [vlastní roli](../role-based-access-control/custom-roles.md) , vyžadují se následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Ve výchozím nastavení používá AKS ke své identitě clusteru spravovanou identitu. Místo toho ale můžete použít instanční objekt. Další informace:
* Delegování instančního objektu služby AKS najdete v tématu delegování [přístupu k jiným prostředkům Azure][sp-delegation]. 
* Spravované identity najdete v tématu [použití spravovaných identit](use-managed-identity.md).

Jak každý uzel a pod obdrží svou vlastní IP adresu, naplánujte rozsahy adres pro podsítě AKS. Mějte na paměti, že:
* Podsíť musí být dostatečně velká, aby poskytovala IP adresy pro každý uzel, lusky a síťový prostředek, který nasadíte. 
    * Pro kubenet i síť Azure CNI má každý uzel, na kterém běží, výchozí omezení počtu lusků.
* Každý cluster AKS musí být umístěný ve vlastní podsíti. 
* Nepoužívejte rozsahy IP adres, které se překrývají se stávajícími síťovými prostředky. 
    * Je nutné, abyste umožnili připojení k místním nebo partnerským sítím v Azure.
* Aby bylo možné zpracovávat události horizontálního navýšení kapacity nebo upgrady clusterů, potřebujete další IP adresy dostupné v přiřazené podsíti. 
    * Tento dodatečný adresní prostor je obzvláště důležitý, pokud používáte kontejnery Windows serveru, protože tyto fondy uzlů vyžadují upgrade na použití nejnovějších oprav zabezpečení. Další informace o uzlech Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

Pokud chcete vypočítat požadovanou IP adresu, přečtěte si téma [Konfigurace sítě Azure CNI v AKS][advanced-networking].

Při vytváření clusteru pomocí sítě Azure CNI zadáte pro cluster jiné rozsahy adres, jako je adresa mostu Docker, IP adresa služby DNS a rozsah adres služby. Obecně se ujistěte, že tyto rozsahy adres:
* Nemusíte se vzájemně překrývat.
* Nemusíte překrývat s žádnou sítí přidruženou ke clusteru, včetně všech virtuálních sítí, podsítí, místních a partnerských sítí. 

Konkrétní podrobnosti o omezeních a velikosti pro tyto rozsahy adres najdete v tématu [Konfigurace sítě Azure CNI v AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet sítě

I když kubenet nevyžaduje, abyste nastavili virtuální sítě před nasazením clusteru, existují nevýhody pro čekání:

* Vzhledem k tomu, že se uzly a lusky nacházejí v různých podsítích IP adres, směrování definované uživatelem (UDR) a předávání IP trasuje provoz mezi lusky a uzly. Toto dodatečné směrování může snížit výkon sítě.
* Připojení k existujícím místním sítím nebo partnerským vztahům k jiným virtuálním sítím Azure můžou být složitá.

Vzhledem k tomu, že nevytvoříte virtuální síť a podsítě odděleně od clusteru AKS, Kubenet je ideální pro:
* Malé vývojové nebo testovací úlohy. 
* Jednoduché weby s nízkým provozem.
* Zdvihání a přesouvá úlohy do kontejnerů.

U většiny nasazení v produkčním prostředí byste měli naplánovat a použít službu Azure CNI Networking.

[Pomocí kubenet můžete také nakonfigurovat vlastní rozsahy IP adres a virtuální sítě][aks-configure-kubenet-networking]. Podobně jako u sítě Azure CNI se tyto rozsahy adres nepřesahují navzájem a neměly by se překrývat se všemi sítěmi, které jsou přidružené ke clusteru (virtuální sítě, podsítě, místní a partnerské sítě). 

Konkrétní informace o omezeních a velikosti pro tyto rozsahy adres najdete v tématu [použití sítě kubenet s vlastními rozsahy IP adres v AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuce příchozího provozu

> **Osvědčené postupy** 
> 
> K distribuci přenosů HTTP nebo HTTPS do aplikací použijte prostředky a řadiče příchozího přenosu dat. V porovnání s nástrojem pro vyrovnávání zatížení Azure poskytují řadiče příchozího přenosu dalších funkcí a dají se spravovat jako nativní prostředky Kubernetes.

I když nástroj pro vyrovnávání zatížení Azure může distribuovat provoz zákazníků do aplikací v clusteru AKS, je tento provoz omezený. Prostředek nástroje pro vyrovnávání zatížení pracuje ve vrstvě 4 a distribuuje provoz na základě protokolu nebo portů. 

Většina webových aplikací pomocí protokolu HTTP nebo HTTPS by měla používat Kubernetes prostředky a řadiče příchozího přenosu dat, které fungují ve vrstvě 7. Příchozí přenos dat může distribuovat provoz na základě adresy URL aplikace a zpracovat ukončení protokolu TLS/SSL. Příchozí přenos dat také snižuje počet IP adres, které zveřejňujete a namapujete. 

U nástroje pro vyrovnávání zatížení každá aplikace obvykle potřebuje veřejnou IP adresu přiřazenou a namapovanou na službu v clusteru AKS. Pomocí prostředku příchozího přenosu dat může jedna IP adresa distribuovat provoz do více aplikací.

![Diagram znázorňující tok příchozího provozu v clusteru AKS](media/operator-best-practices-network/aks-ingress.png)

 Pro příchozí přenos dat jsou k dispozici dvě komponenty:

 * *Prostředek* příchozího přenosu dat
 * *Kontroler* příchozího přenosu dat

### <a name="ingress-resource"></a>Prostředek příchozího přenosu dat

*Prostředek* příchozího přenosu dat je YAML manifest `kind: Ingress` . Definuje hostitele, certifikáty a pravidla pro směrování provozu do služeb spuštěných v clusteru AKS. 

Následující ukázkový manifest YAML by distribuuje provoz pro *MyApp.com* do jedné ze dvou služeb, *blogservice* nebo *StoreService*. Zákazník je směrován na jednu službu nebo druhý na základě adresy URL, které má přístup.

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

### <a name="ingress-controller"></a>Kontroler příchozího přenosu dat

*Kontroler* příchozího přenosu dat je démon, který běží na uzlu AKS a sleduje příchozí požadavky. Provoz se pak distribuuje na základě pravidel definovaných v prostředku příchozího přenosu dat. I když je nejběžnější kontroler příchozího přenosu dat založen na [Nginx], AKS vás neomezuje na konkrétní kontroler. Můžete použít [rozvrh][contour], [HAProxy][haproxy], [Traefik][traefik]atd.

Řadiče příchozího přenosu dat musí být naplánované na uzlu Linux. Označuje, že prostředek by měl běžet v uzlu se systémem Linux pomocí voliče uzlů ve vašem YAML manifestu nebo nasazení grafu Helm. Další informace najdete v tématu [použití selektorů uzlů k řízení, kde se v AKS naplánovala lusky][concepts-node-selectors].

> [!NOTE]
> V uzlech Windows Serveru by se kontroler příchozího přenosu dat neměl spouštět.

Pro příchozí přenos dat existuje mnoho scénářů, včetně následujících průvodců:

* [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
* [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
* [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
* Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpečení provozu pomocí firewallu webových aplikací (WAF)

> **Osvědčené postupy**
> 
> Pokud chcete vyhledat potenciální útoky, použijte Firewall webových aplikací (WAF), jako je například [Barracuda WAF pro Azure][barracuda-waf] nebo Azure Application Gateway. Tyto pokročilejší síťové prostředky můžou směrovat provoz i přes připojení HTTP a HTTPS nebo základní ukončení protokolu TLS.

Řadič příchozího přenosu dat je obvykle prostředek Kubernetes v clusteru AKS, který distribuuje provoz do služeb a aplikací. Kontroler se spouští jako démon na uzlu AKS a spotřebovává některé prostředky uzlu, jako je procesor, paměť a šířka pásma sítě. Ve větších prostředích budete chtít:
* Přesměruje některé z těchto směrování provozu nebo ukončení protokolu TLS do síťového prostředku mimo cluster AKS.
* Vyhledá v příchozím provozu potenciální útoky.

![Firewall webových aplikací (WAF), například Azure App Gateway, může chránit a distribuovat provoz pro cluster AKS.](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Pro tuto dodatečnou vrstvu zabezpečení, Firewall webových aplikací (WAF) filtruje příchozí provoz. V případě sady pravidel se v otevřeném projektu OWASP (Web Application Security) sleduje útoky, jako je například skriptování mezi weby nebo poškození souborů cookie. [Azure Application Gateway][app-gateway] (v současnosti ve verzi Preview v AKS) je WAF, který se integruje s CLUSTERy AKS a předá přenosy do vašeho clusteru a aplikací AKS. 

Vzhledem k tomu, že další řešení jiných výrobců tyto funkce používají, můžete i nadále používat stávající investice nebo odbornost v preferovaném produktu.

Nástroj pro vyrovnávání zatížení nebo prostředky příchozího přenosu dat nepřetržitě běží ve vašem clusteru AKS a upřesněte distribuci provozu. Aplikační brána se dá centrálně spravovat jako kontroler příchozího přenosu s definicí prostředků. Začněte tím, [že vytvoříte řadič Application Gateway příchozího][app-gateway-ingress]přenosu dat.

## <a name="control-traffic-flow-with-network-policies"></a>Řízení toku provozu pomocí zásad sítě

> **Osvědčené postupy** 
>
> K povolení nebo zamítnutí provozu v luskech použijte zásady sítě. Ve výchozím nastavení je povolen veškerý provoz mezi lusky v rámci clusteru. Pro lepší zabezpečení definujte pravidla, která omezují komunikaci pod.

Zásada sítě je funkce Kubernetes, která je dostupná v AKS, která umožňuje řídit tok přenosů mezi lusky. V závislosti na nastaveních, jako jsou přiřazené popisky, obory názvů nebo přenosový port, povolíte nebo zakážete provoz. Zásady sítě představují způsob, jak řídit tok provozu pro lusky. V případě, že se v clusteru AKS dynamicky vytvářejí lusky, je možné automaticky použít požadované zásady sítě.

Pokud chcete použít zásady sítě, povolte tuto funkci při vytváření nového clusteru AKS. V existujícím clusteru AKS nemůžete povolit síťové zásady. Předem naplánujte povolení zásad sítě v nezbytných clusterech. 

>[!NOTE]
>Zásady sítě by se měly používat jenom pro uzly se systémem Linux a lusky v AKS.

Zásady sítě můžete vytvořit jako prostředek Kubernetes pomocí manifestu YAML. Zásady se aplikují na definovaná lusky s pravidly příchozí nebo odchozí komunikace, která definují tok přenosů. 

Následující příklad aplikuje zásady sítě na lusky s *aplikací: back-end –* na ně se aplikuje jmenovka. Pravidlo příchozího přenosu dat povoluje pouze provoz z lusků s *aplikací:* popisek s front-endu:

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

Informace o tom, jak začít se zásadami, najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Zabezpečené připojení k uzlům prostřednictvím hostitele bastionu

> **Osvědčené postupy** 
>
> Nezveřejňujte vzdálené připojení k uzlům AKS. Ve virtuální síti pro správu vytvořte hostitele bastionu nebo pole s odkazem. Použijte hostitele bastionu k bezpečnému směrování provozu do vašeho clusteru AKS na úlohy vzdálené správy.

Většinu operací v AKS můžete dokončit pomocí nástrojů pro správu Azure nebo prostřednictvím serveru Kubernetes API. Uzly AKS jsou dostupné jenom v privátní síti a nepřipojují se k veřejnému Internetu. Pokud se chcete připojit k uzlům a zajišťovat údržbu a podporu, směrujte připojení prostřednictvím hostitele bastionu nebo pole s odkazem. Ověřte tohoto hostitele v samostatné, zabezpečené virtuální síti pro správu s partnerským vztahem k virtuální síti clusteru AKS.

![Připojení k uzlům AKS pomocí hostitele bastionu nebo pole s odkazem](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Síť pro správu pro hostitele bastionu by měla být zabezpečená. Použijte [Azure ExpressRoute][expressroute] nebo [VPN Gateway][vpn-gateway] pro připojení k místní síti a řízení přístupu pomocí skupin zabezpečení sítě.

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na připojení a zabezpečení sítě. Další informace o základech sítě v Kubernetes najdete v tématu [Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)][aks-concepts-network] .

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