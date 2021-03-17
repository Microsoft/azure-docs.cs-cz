---
title: Koncepty – sítě v Azure Kubernetes Services (AKS)
description: Přečtěte si o sítích ve službě Azure Kubernetes Service (AKS), včetně kubenet a Azure CNI, řadičů pro příjem dat, nástrojů pro vyrovnávání zatížení a statických IP adres.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88243900"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)

V rámci přístupu mikroslužeb založených na kontejneru ke vývoji aplikací musí komponenty aplikace spolupracovat na zpracování svých úkolů. Kubernetes poskytuje různé prostředky, které umožňují komunikaci této aplikace. Můžete se připojit k aplikacím a vystavit je interně nebo externě. K vytváření aplikací s vysokou dostupností můžete vyrovnávat zatížení aplikací. Složitější aplikace můžou vyžadovat konfiguraci příchozího provozu pro ukončení protokolu SSL/TLS nebo pro směrování více součástí. Z bezpečnostních důvodů může být také nutné omezit tok síťového provozu do lusků a uzlů.

V tomto článku se seznámíte se základními koncepcemi, které poskytují sítě vašim aplikacím v AKS:

- [Služby](#services)
- [Virtuální sítě Azure](#azure-virtual-networks)
- [Kontrolery příchozích dat](#ingress-controllers)
- [Zásady sítě](#network-policies)

## <a name="kubernetes-basics"></a>Základy Kubernetes

Pro umožnění přístupu k aplikacím nebo pro komunikaci mezi komponentami aplikace Kubernetes poskytuje abstrakci vrstvu pro virtuální sítě. Uzly Kubernetes jsou připojené k virtuální síti a můžou poskytovat příchozí a odchozí připojení pro lusky. Komponenta *Kube-proxy* se spouští na všech uzlech, aby poskytovala tyto síťové funkce.

V Kubernetes *služby* logicky seskupují, aby umožňovaly přímý přístup prostřednictvím IP adresy nebo názvu DNS a na určitém portu. Provoz můžete také distribuovat pomocí *Nástroje pro vyrovnávání zatížení*. Složitější směrování provozu s aplikacemi se dá dosáhnout i u *řadičů*příchozího přenosu dat. Zabezpečení a filtrování síťového provozu pro lusky je možné u *zásad sítě*Kubernetes.

Platforma Azure také pomáhá zjednodušit virtuální sítě pro clustery AKS. Když vytvoříte Nástroj pro vyrovnávání zatížení Kubernetes, vytvoří se a nakonfiguruje příslušný prostředek nástroje pro vyrovnávání zatížení Azure. Při otevírání síťových portů do lusků jsou nakonfigurovaná odpovídající pravidla skupiny zabezpečení sítě Azure. Pro směrování aplikací HTTP může Azure nakonfigurovat také *externí DNS* , protože jsou nakonfigurované nové trasy příchozího přenosu dat.

## <a name="services"></a>Služby

Pro zjednodušení konfigurace sítě pro úlohy aplikací Kubernetes používá *služby* k logickému seskupení sady lusků a zajištění připojení k síti. K dispozici jsou následující typy služeb:

- **IP adresa clusteru** – vytvoří interní IP adresu pro použití v rámci clusteru AKS. Vhodné pro interní aplikace, které podporují jiné úlohy v rámci clusteru.

    ![Diagram znázorňující tok přenosů IP adres clusteru v clusteru AKS][aks-clusterip]

- **NodePort** – vytvoří mapování portů na podkladovém uzlu, který umožňuje, aby se aplikace získala přímo s IP adresou a portem uzlu.

    ![Diagram znázorňující tok přenosů NodePort v clusteru AKS][aks-nodeport]

- Nástroj pro vyrovnávání **zatížení – vytvoří** prostředek nástroje pro vyrovnávání zatížení Azure, nakonfiguruje externí IP adresu a připojí požadované lusky k back-endu služby Vyrovnávání zatížení. Aby bylo možné v provozu zákazníků dosáhnout aplikace, budou pravidla vyrovnávání zatížení vytvořena na požadovaných portech. 

    ![Diagram znázorňující Load Balancer tok provozu v clusteru AKS][aks-loadbalancer]

    Pro další kontrolu a směrování příchozího provozu můžete místo toho použít [kontroler](#ingress-controllers)příchozího přenosu dat.

- **External** -vytvoří specifickou položku DNS pro snazší přístup k aplikacím.

IP adresu pro nástroje pro vyrovnávání zatížení a služby lze dynamicky přiřadit nebo můžete zadat existující statickou IP adresu, kterou chcete použít. Je možné přiřadit interní i externí statické IP adresy. Tato existující statická IP adresa je často vázaná na položku DNS.

Je možné vytvořit *interní* i *externí* nástroje pro vyrovnávání zatížení. Interním nástrojům pro vyrovnávání zatížení je přiřazena pouze privátní IP adresa, takže k nim nelze přicházet z Internetu.

## <a name="azure-virtual-networks"></a>Virtuální sítě Azure

V AKS můžete nasadit cluster, který používá jeden z následujících dvou síťových modelů:

- *Kubenet* Networking – síťové prostředky se většinou vytvářejí a konfigurují, protože je nasazený cluster AKS.
- Síťové *rozhraní CNI (Azure Container Networking Interface)* – cluster AKS je připojený k existujícím prostředkům a konfiguracím virtuální sítě.

### <a name="kubenet-basic-networking"></a>Kubenet (základní) sítě

Možnost sítě *kubenet* je výchozí konfigurace pro vytváření clusteru AKS. Pomocí *kubenet*uzly získají IP adresu z podsítě virtuální sítě Azure. Pody získají IP adresu z logicky jiného adresního prostoru do podsítě virtuální sítě Azure uzlů. Překlad adres (NAT) se pak nakonfiguruje tak, aby pody mohly získat přístup k prostředkům ve virtuální síti Azure. Zdrojová IP adresa provozu je NAT k primární IP adrese uzlu.

Uzly používají modul plug-in [kubenet][kubenet] Kubernetes. Můžete nechat platformu Azure, která vám umožní vytvořit a nakonfigurovat virtuální sítě, nebo se rozhodnout nasadit cluster AKS do existující podsítě virtuální sítě. Znovu, pouze uzly obdrží IP adresu směrování a lusky používají ke komunikaci s dalšími prostředky mimo cluster AKS službu NAT. Tento přístup významně snižuje počet IP adres, které je třeba vyhradit v síťovém prostoru pro použití v luskech.

Další informace najdete v tématu [Konfigurace sítě kubenet pro cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sítě Azure CNI (rozšířené)

Při použití Azure CNI získá každý pod IP adresu z podsítě a dá se k němu přistupovat přímo. Tyto IP adresy musí být v rámci vašeho síťového prostoru jedinečné a musí být plánovány předem. Každý uzel má parametr konfigurace maximálního počtu lusků, které podporuje. Pro tento uzel je pak rezervovaný i ekvivalentní počet IP adres na uzel. Tento přístup vyžaduje více plánování, jinak může dojít k vyčerpání IP adres nebo potřeba znovu sestavit clustery ve větší podsíti, jak vaše aplikace požaduje růst.

Na rozdíl od kubenet provoz do koncových bodů ve stejné virtuální síti není NAT k primární IP adrese uzlu. Zdrojová adresa pro přenos dat ve virtuální síti je pod IP adresou pod. Přenosy, které jsou externí pro virtuální síť, jsou pořád NAT na primární IP adresu uzlu.

Uzly používají modul plug-in Kubernetes [CNI (Azure Container Networking Interface)][cni-networking] .

![Diagram znázorňující dva uzly s mostem připojujícím se každé virtuální síti Azure][advanced-networking-diagram]

Další informace najdete v tématu [konfigurace Azure CNI pro cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porovnání síťových modelů

Kubenet i Azure CNI poskytují připojení k síti pro vaše clustery AKS. Existují však i výhody a nevýhody. Na nejvyšší úrovni platí následující požadavky:

* **kubenet**
    * Zachovává adresní prostor IP adres.
    * Používá interní nebo externí nástroj pro vyrovnávání zatížení k dosažení lusků mimo cluster.
    * Je nutné ručně spravovat a udržovat trasy definované uživatelem (udr).
    * Maximálně 400 uzlů na cluster.
* **CNI Azure**
    * Lusky získají úplnou konektivitu virtuální sítě a dají se přímo kontaktovat prostřednictvím jejich privátních IP adres z propojených sítí.
    * Vyžaduje další adresní prostor IP adres.

Mezi kubenet a Azure CNI existují následující rozdíly v chování:

| Schopnost                                                                                   | Kubenet   | CNI Azure |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Nasadit cluster v existující nebo nové virtuální síti                                            | Podporováno – udr ručně použito | Podporováno |
| Připojení pod                                                                         | Podporováno | Podporováno |
| Připojení pod virtuálním počítačem; Virtuální počítač ve stejné virtuální síti                                          | Funguje při inicializaci pod | Funguje v obou směrech |
| Připojení pod virtuálním počítačem; Virtuální počítač ve virtuální síti s partnerským vztahem                                            | Funguje při inicializaci pod | Funguje v obou směrech |
| Místní přístup pomocí sítě VPN nebo Express Route                                                | Funguje při inicializaci pod | Funguje v obou směrech |
| Přístup k prostředkům zabezpečeným koncovými body služby                                             | Podporováno | Podporováno |
| Vystavení služeb Kubernetes pomocí služby Vyrovnávání zatížení, služby App Gateway nebo řadiče pro příchozí přenosy | Podporováno | Podporováno |
| Výchozí Azure DNS a soukromé zóny                                                          | Podporováno | Podporováno |

V souvislosti se službou DNS nabízí kubenet i služba Azure CNI plugins DNS, což je nasazení běžící v AKS s vlastním nástrojem pro horizontální navýšení kapacity. Další informace o CoreDNS v Kubernetes najdete v tématu [přizpůsobení služby DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). Služba CoreDNS je nakonfigurovaná na výchozí hodnotu pro přeposílání neznámých domén na servery DNS uzlů, jinými slovy, na funkce DNS Azure Virtual Network, kde je nasazený cluster AKS. Proto Azure DNS a soukromé zóny budou fungovat pro lusky běžící v AKS.

### <a name="support-scope-between-network-models"></a>Rozsah podpory mezi síťovými modely

Bez ohledu na použitý model sítě můžete nasadit jak kubenet, tak i Azure CNI, a to jedním z následujících způsobů:

* Platforma Azure může automaticky vytvářet a konfigurovat prostředky virtuální sítě při vytváření clusteru AKS.
* Při vytváření clusteru AKS můžete ručně vytvořit a nakonfigurovat prostředky virtuální sítě a připojit se k těmto prostředkům.

I když jsou podporované možnosti, jako jsou koncové body služby nebo udr, kubenet i Azure CNI, [zásady podpory pro AKS][support-policies] definují, jaké změny můžete provádět. Například:

* Pokud ručně vytvoříte prostředky virtuální sítě pro cluster AKS, budete podporováni při konfiguraci vlastních koncových bodů udr nebo služby.
* Pokud platforma Azure automaticky vytvoří prostředky virtuální sítě pro cluster AKS, není podporováno ruční změna těchto prostředků spravovaných AKS ke konfiguraci vlastních koncových bodů udr nebo služby.

## <a name="ingress-controllers"></a>Kontrolery příchozích dat

Když vytvoříte službu typu Vyrovnávání zatížení, vytvoří se základní prostředek nástroje pro vyrovnávání zatížení Azure. Nástroj pro vyrovnávání zatížení je nakonfigurovaný k distribuci provozu do lusků ve vaší službě na daném portu. Nástroj pro vyrovnávání zatížení funguje pouze ve vrstvě 4 – služba nezohledňuje skutečné aplikace a nemůže dělat žádné další informace o směrování.

*Řadiče* příchozího přenosu dat pracují ve vrstvě 7 a můžou použít více inteligentních pravidel k distribuci provozu aplikací. Běžným použitím kontroleru příchozího přenosu dat je směrování přenosů HTTP do různých aplikací na základě příchozí adresy URL.

![Diagram znázorňující tok příchozího provozu v clusteru AKS][aks-ingress]

V AKS můžete vytvořit prostředek příchozího přenosu dat pomocí nějakého typu NGINX nebo použít funkci směrování aplikace HTTP AKS. Když zapnete směrování aplikace HTTP pro cluster AKS, platforma Azure vytvoří kontroler příchozího přenosu dat a *externí řadič DNS* . Při vytváření nových prostředků příchozího přenosu v Kubernetes se požadované záznamy DNS vytvoří v zóně DNS specifické pro clustery. Další informace najdete v tématu [nasazení směrování aplikace http][aks-http-routing].

Doplněk Application Gateway AGIC () umožňuje zákazníkům AKS využít Application Gateway nativní Nástroj pro vyrovnávání zatížení na úrovni 7 Azure úrovně 7 k zveřejnění cloudového softwaru na Internet. AGIC monitoruje cluster Kubernetes, na kterém je hostovaný, a nepřetržitě aktualizuje Application Gateway, aby se vybrané služby zobrazovaly na internetu. Další informace o doplňku AGIC pro AKS najdete v tématu [co je Application Gateway řadič pro příchozí přenosy?][agic-overview]

Další běžnou funkcí příchozího přenosu dat je ukončení SSL/TLS. U rozsáhlých webových aplikací, které jsou k dispozici prostřednictvím protokolu HTTPS, může být ukončení protokolu TLS zpracováno prostředkem příchozího přenosu dat, nikoli v rámci samotné aplikace. Pokud chcete zajistit automatické generování a konfiguraci certifikace TLS, můžete nakonfigurovat prostředek příchozího přenosu dat tak, aby používal zprostředkovatele, jako je například zašifrování. Další informace o konfiguraci kontroleru NGINX příchozího přenosu dat pomocí šifry najdete v tématu příchozí [a TLS][aks-ingress-tls].

Můžete taky nakonfigurovat svůj kontroler příchozího přenosu dat, aby se v požadavcích na kontejnery v clusteru AKS zachovala zdrojová IP adresa klienta. Když je požadavek klienta směrován do kontejneru v clusteru AKS prostřednictvím vašeho kontroleru příchozího přenosu dat, původní zdrojová IP adresa této žádosti nebude k dispozici pro cílový kontejner. Když povolíte *zachování IP adresy zdrojového klienta*, je zdrojová IP adresa pro klienta k dispozici v hlavičce žádosti v části *X-předáno-pro*. Pokud používáte zachování IP adresy zdrojového klienta v řadiči příchozího přenosu dat, nemůžete použít průchozí protokol TLS. U jiných služeb, jako je třeba typ *Vyrovnávání zatížení* sítě, můžete použít předávání IP adres klienta a předávací protokol TLS.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupina zabezpečení sítě filtruje provoz virtuálních počítačů, například uzlů AKS. Při vytváření služeb, jako je například nástroj pro vyrovnávání zatížení, platforma Azure automaticky konfiguruje všechna potřebná pravidla skupiny zabezpečení sítě. Nekonfigurujte ručně pravidla skupiny zabezpečení sítě pro filtrování provozu pro lusky v clusteru AKS. Definujte všechny požadované porty a předávání jako součást svých manifestů služby Kubernetes a umožněte, aby platforma Azure vytvořila nebo aktualizovala příslušná pravidla. Můžete také použít zásady sítě, jak je popsáno v další části, a automaticky tak použít pravidla filtru přenosu na lusky.

## <a name="network-policies"></a>Zásady sítě

Ve výchozím nastavení mohou všechny lusky v clusteru AKS odesílat a přijímat přenosy bez omezení. Pro zvýšení zabezpečení můžete definovat pravidla, která řídí tok přenosů. Back-endové aplikace se často zveřejňují jenom pro požadované služby front-end, nebo součásti databáze jsou dostupné jenom pro aplikační vrstvy, které k nim připojovat.

Zásada sítě je funkce Kubernetes, která je dostupná v AKS, která umožňuje řídit tok přenosů mezi lusky. Můžete povolit nebo zamítnout provoz na základě nastavení, jako jsou například přiřazené popisky, obor názvů nebo přenosový port. Skupiny zabezpečení sítě jsou pro uzly AKS, ne lusky, další. Používání zásad sítě je vhodnějším způsobem nativního cloudu pro řízení toku provozu. V případě, že se v clusteru AKS dynamicky vytvářejí lusky, je možné automaticky použít požadované zásady sítě.

Další informace najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat AKS sítě, vytvořte a nakonfigurujte cluster AKS s vlastními rozsahy IP adres s využitím [kubenet][aks-configure-kubenet-networking] nebo [Azure CNI][aks-configure-advanced-networking].

Související osvědčené postupy najdete v tématu [osvědčené postupy pro připojení k síti a zabezpečení v AKS][operator-best-practices-network].

Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Přístup a identita Kubernetes/AKS][aks-concepts-identity]
- [Zabezpečení Kubernetes/AKS][aks-concepts-security]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
