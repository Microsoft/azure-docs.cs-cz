---
title: Operátor osvědčené postupy – připojení k síti ve službě Azure Kubernetes služby (AKS)
description: Podívejte se na clusteru – operátor osvědčené postupy pro prostředky virtuální sítě a připojení ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: iainfou
ms.openlocfilehash: b1d4a3bde7852cb4eb9192ed1eab80c20613510a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263836"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro připojení k síti a zabezpečení ve službě Azure Kubernetes Service (AKS)

Jak vytvořit a spravovat clustery ve službě Azure Kubernetes Service (AKS), poskytovat připojení k síti pro uzly a aplikací. Tyto síťové prostředky patří rozsahy adres IP, nástroje pro vyrovnávání zatížení a příchozího přenosu dat řadiče. Udržujte vysokou kvalitu služby pro vaše aplikace, musíte naplánovat a potom nakonfigurujte tyto prostředky.

Tento článek o osvědčených postupech se zaměřuje na připojení k síti a zabezpečení pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Porovnání základních a pokročilých síťových režimy v AKS
> * Plánování pro požadované IP adresy a možnosti připojení
> * Distribuce provozu pomocí nástroje pro vyrovnávání zatížení, řadiče příchozího přenosu dat nebo firewallů webových aplikací (WAF)
> * Zabezpečené připojení k uzlům clusteru

## <a name="choose-the-appropriate-network-model"></a>Zvolte model příslušné sítě

**Osvědčené postupy pro moduly** – integrace s existující virtuální sítě nebo sítích na pracovišti, použijte rozšířeného sítě ve službě AKS. Tento model síťového umožňuje také větší oddělení prostředků a ovládací prvky v podnikovém prostředí.

Virtuální sítě poskytují základní připojení pro uzly AKS a zákazníky pro přístup k vaší aplikace. Existují dva různé způsoby nasazování clusteru AKS do virtuální sítě:

* **Základní síť** -Azure spravuje prostředky virtuální sítě jako cluster nasazuje a používá [kubenet] [ kubenet] modulu plug-in Kubernetes.
* **Rozšířeného sítě** – nasadí do existující virtuální sítě a použije [Azure Container síťové rozhraní (CNI)] [ cni-networking] modulu plug-in Kubernetes. Podů přijímat jednotlivých IP adres, který může směrovat do jiných síťových služeb nebo místní prostředky.

Kontejner síťové rozhraní (CNI) je protokol dodavatelích, který umožňuje provádět požadavky poskytovatele síťových prostředí runtime kontejneru. Azure CNI přiřadí IP adresy k podů a uzly a poskytuje funkce pro správu (IPAM) IP adresy připojit k existující virtuální sítě Azure. Každý uzel a pod prostředků obdrží IP adresu ve virtuální síti Azure a žádné další směrování je potřeba ke komunikaci s ostatními prostředky nebo služby.

![Diagram znázorňující dva uzly s mostů propojení každý z nich jedné virtuální sítě Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Pro většinu nasazení v produkčním prostředí měli byste použít rozšířeného sítě. Tento model sítě umožňuje oddělení ovládacího prvku a správu prostředků. Z hlediska zabezpečení často mají různými týmy ke správě a zabezpečení těchto prostředků. Rozšířeného sítě umožňuje, aby připojení k existující prostředky Azure, místním prostředkům nebo jiné služby přímo prostřednictvím IP adresy přiřazené k každý pod.

Při použití rozšířeného sítě je prostředek virtuální sítě v samostatné skupiny prostředků do clusteru AKS. Delegování oprávnění pro instanční objekt služby AKS k přístupu a správě těchto prostředků. Instanční objekt používané clusterem AKS musí mít minimálně [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) oprávnění na podsítě v rámci vaší virtuální sítě. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) nemusíte používat předdefinovaná role Přispěvatel sítě, se vyžadují následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Další informace o delegování instančního objektu služby AKS najdete v tématu [delegovat přístup k dalším prostředkům Azure][sp-delegation].

Jak každým uzlem a pod zobrazí jeho vlastní IP adresu, naplánujte si rozsahy adres podsítě AKS. Podsíť musí být dostatečně velký, k poskytování IP adres pro každý uzel, podů a síťové prostředky, které nasadíte. Každý cluster AKS, musí být umístěné ve vlastní podsíti. Povolení připojení k místní nebo partnerské sítě v Azure, nepoužívejte rozsahy IP adres, které se překrývají s existující síťové prostředky. Zde jsou nastavená výchozí omezení počtu podů, které se spouští každý uzel se síťovými základních a pokročilých. Pro zpracování vertikálního navýšení události nebo upgrady clusteru, musíte také dalších IP adres k dispozici pro použití v přiřazené podsítě.

Pro výpočet IP adresy potřeba, najdete v článku [pokročilé konfigurace sítě ve službě AKS][advanced-networking].

### <a name="basic-networking-with-kubenet"></a>Základní práce se sítěmi pomocí Kubenet

I když základní sítě nevyžaduje nastavení virtuální sítě předtím, než se cluster nasazuje, existují nevýhody:

* Uzly a podů jsou umístěny v různých podsítích protokolu IP. Předávání uživatelem definovanou směrování (UDR) a IP adresa se používá pro směrování provozu mezi uzly a podů. Další směrování snižuje výkon sítě.
* Připojení k existující místní sítě nebo partnerského vztahu k jiným virtuálním sítím Azure je složitá.

Základní sítě je vhodná pro malé úlohy vývoje a testování, protože není nutné vytvořit virtuální síť a podsítě odděleně od clusteru AKS. Jednoduché weby s nízkým provozem, nebo chcete navýšit a přesunout úlohy do kontejnerů, mohou také těžit z jednoduchost AKS clusterech nasazených s základní sítě. Pro většinu nasazení v produkčním prostředí plánování a použití rozšířeného sítě.

## <a name="distribute-ingress-traffic"></a>Distribuce příchozího provozu

**Osvědčené postupy pro moduly** – Chcete-li distribuovat provoz protokolu HTTP nebo HTTPS k aplikacím, použijte prostředky příchozího přenosu dat a řadičů. Příchozí přenos dat řadiče poskytují další funkce nad regulární nástroji Azure load balancer a můžete spravovat jako nativní prostředky Kubernetesu.

Azure load balancer můžete distribuovat provoz zákazníka k aplikacím ve vašem clusteru AKS, ale co to rozumí o tento přenos je omezený. Prostředek nástroje pro vyrovnávání zatížení funguje ve vrstvě 4 a distribuuje provoz na základě protokolu nebo porty. Většina webových aplikací, které používají protokol HTTP nebo HTTPS používali prostředky Kuberenetes příchozího přenosu dat a řadičů, které fungují ve vrstvě 7. Příchozí přenos dat můžete distribuci provozu na základě adresy URL aplikace a popisovač ukončení protokolu TLS/SSL. Tato možnost také snižuje počet IP adres vystavení a mapování. Pomocí služby load balancer každá aplikace obvykle musí veřejnou IP adresu, přiřadit a namapované na služby v clusteru AKS. Jednu IP adresu můžete s prostředek příchozí přenos dat distribuce provozu do více aplikací.

![Diagram znázorňující tok provozu příchozího přenosu dat v clusteru AKS](media/operator-best-practices-network/aks-ingress.png)

 Existují dvě komponenty pro příjem příchozích dat:

 * Příchozí přenos dat *prostředků*, a
 * Příchozí přenos dat *kontroleru*

Prostředek příchozího přenosu dat je YAML manifestu `kind: Ingress` , který definuje hostitele, certifikáty a pravidla pro směrování provozu do služby, které běží ve vašem clusteru AKS. Následující příklad YAML manifestu distribuovat provoz pro *myapp.com* na jednu ze dvou služeb *blogservice* nebo *storeservice*. Zákazník se přesměruje do jedné služby nebo druhý podle zadané adresy URL přistupují.

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

Řadič služby příchozího přenosu dat je proces démon, který běží na uzlu AKS a sleduje pro příchozí požadavky. Provoz je poté distribuován na základě pravidel definovaných v příchozího přenosu dat prostředku. Nejběžnější kontroler příchozího přenosu dat se odvíjí [Server Nginx]. AKS nebrání můžete k určitému kontroleru, abyste mohli používat ostatní řadiče, jako [rozvrh][contour], [HAProxy][haproxy], nebo [ Traefik][traefik].

Existuje mnoho scénářů pro příchozí přenos dat, včetně následujících návody:

* [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
* [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
* [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
* Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpečení provozu pomocí firewallu webových aplikací (WAF)

**Osvědčené postupy pro moduly** – Chcete-li kontrolovat příchozí provoz na potenciální útoky, pomocí firewallu webových aplikací (WAF), jako [Barracuda WAF pro Azure] [ barracuda-waf] nebo služby Azure Application Gateway. Tyto pokročilejší síťové prostředky můžete také směrovat provoz nad rámec pouze připojení HTTP a HTTPS nebo základní ukončení protokolu SSL.

Řadič služby příchozího přenosu dat, který distribuuje provoz do služeb a aplikací je obvykle prostředek Kubernetes v clusteru AKS. Kontroler spouští se jako démon na uzlu AKS a využívá některé z uzlu prostředků, jako jsou procesor, paměť a šířka pásma sítě. V prostředích s větší často chcete přesměrovat některé této směrování provozu nebo ukončení protokolu TLS k síťovému prostředku mimo AKS cluster. Chcete také zkontrolovat příchozího provozu pro potenciální útoky.

![Firewall webových aplikací (WAF) jako je například Azure App Gateway můžete chránit a distribuovat provoz pro váš cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Firewall webových aplikací (WAF) poskytuje další vrstvu zabezpečení pomocí filtrování příchozích přenosů. V otevřené webové aplikace zabezpečení projektu (OWASP) poskytuje sadu pravidel a sledujte útoky jako různé skriptování, nebo soubor cookie (Cache poisoning). [Azure Application Gateway] [ app-gateway] je WAF, která lze integrovat s AKS clusterů a zajistit tak tyto funkce zabezpečení před přenosy přicházejí clusteru AKS a aplikace. Jiná řešení třetích stran také provádění těchto funkcí, proto můžete nadále používat stávající investice nebo odbornými znalostmi v daném produktu.

Nástroje pro vyrovnávání nebo příchozího přenosu dat prostředků zatížení nadále spuštěna v clusteru AKS pro další upřesnění distribuce provozu. App Gateway můžete centrálně spravovat jako řadič příchozího přenosu dat s definicí prostředků. Abyste mohli začít, [vytvořit řadič služby Application Gateway příchozího přenosu dat][app-gateway-ingress].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bezpečné připojení k uzlům prostřednictvím hostitel typu bašta

**Osvědčené postupy pro moduly** -nezveřejňují vzdáleného připojení k vaší uzlů AKS. Vytvoření hostitel typu bašta, nebo přejít do virtuální síť pro správu pole. Použijte hostitele bastionu bezpečně směrovat provoz do clusteru AKS na vzdálených úkolů správy.

Většinu operací ve službě AKS je možné provést pomocí nástroje pro správu Azure nebo na serveru Kubernetes API. Uzlů AKS nejste připojeni k veřejnému Internetu a k dispozici pouze v privátní síti. Připojení k uzlům a provést údržbu nebo řešení potíží, směrovat připojení prostřednictvím hostitel typu bašta nebo jump box. Tento hostitel musí být v samostatné správu virtuální síť, bezpečně partnerský vztah virtuální sítě clusteru AKS.

![Připojte se k uzlů AKS pomocí hostitel typu bašta nebo jump box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

By měla být zabezpečená síť pro správu pro hostitele bastionu, příliš. Použití [Azure ExpressRoute] [ expressroute] nebo [VPN gateway] [ vpn-gateway] pro připojení k místní síti a řízení přístupu pomocí zabezpečení sítě skupiny.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na připojení k síti a zabezpečení. Další informace o základní informace o síti v Kubernetes najdete v tématu [sítě koncepty pro aplikace ve službě Azure Kubernetes Service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Server Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: http://www.haproxy.org
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
[advanced-networking]: configure-advanced-networking.md
