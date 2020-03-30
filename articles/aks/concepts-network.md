---
title: Koncepty – vytváření sítí ve službách Azure Kubernetes Services (AKS)
description: Další informace o sítích ve službě Azure Kubernetes Service (AKS), včetně sítí kubenet a Azure CNI, řadičů příchozích dat, nástrojů pro vyrovnávání zatížení a statických IP adres.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253933"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Síťové koncepty pro aplikace ve službě Azure Kubernetes Service (AKS)

V přístupu mikroslužeb založených na kontejnerech k vývoji aplikací musí součásti aplikací spolupracovat na zpracování svých úkolů. Kubernetes poskytuje různé prostředky, které umožňují komunikaci této aplikace. Můžete se připojit k aplikacím a vystavit je interně nebo externě. Chcete-li vytvářet vysoce dostupné aplikace, můžete vyrovnávání zatížení aplikací. Složitější aplikace mohou vyžadovat konfiguraci příchozího přenosu pro ukončení ssl/tls nebo směrování více součástí. Z bezpečnostních důvodů může být také nutné omezit tok síťového provozu do podů a uzlů nebo mezi nimi.

Tento článek představuje základní koncepty, které poskytují vytváření sítí pro vaše aplikace v AKS:

- [Služby](#services)
- [Virtuální sítě Azure](#azure-virtual-networks)
- [Řadiče příchozího přenosu dat](#ingress-controllers)
- [Zásady sítě](#network-policies)

## <a name="kubernetes-basics"></a>Základy Kubernetes

Chcete-li povolit přístup k aplikacím nebo pro součásti aplikace komunikovat mezi sebou, Kubernetes poskytuje abstrakce vrstvy virtuální sítě. Uzly Kubernetes jsou připojené k virtuální síti a mohou poskytovat příchozí a odchozí připojení pro pody. Komponenta *kube-proxy* je spuštěna na každém uzlu a poskytuje tyto síťové funkce.

V Kubernetes, *Služby* logicky seskupit pods umožnit přímý přístup přes IP adresu nebo název DNS a na konkrétním portu. Provoz můžete také distribuovat pomocí *systému vyrovnávání zatížení*. Složitější směrování aplikačního provozu lze také dosáhnout pomocí *řadičů příchozího přenosu dat*. Zabezpečení a filtrování síťového provozu pro lusky je možné s kubernetes *síťové politiky*.

Platforma Azure také pomáhá zjednodušit virtuální sítě pro clustery AKS. Když vytvoříte nástroj pro vyrovnávání zatížení Kubernetes, vytvoří se a nakonfiguruje se základní prostředek nástroj pro vyrovnávání zatížení Azure. Při otevírání síťových portů do podů jsou nakonfigurovaná odpovídající pravidla skupiny zabezpečení sítě Azure. Pro směrování aplikací HTTP azure můžete také nakonfigurovat *externí DNS* jako nové příchozí ho trasy jsou nakonfigurovány.

## <a name="services"></a>Služby

Chcete-li zjednodušit konfiguraci sítě pro úlohy aplikací, Kubernetes používá *služby* logicky seskupit sadu podů dohromady a poskytnout připojení k síti. K dispozici jsou následující typy služeb:

- **IP clusteru** - Vytvoří interní ADRESU IP pro použití v clusteru AKS. Vhodné pro interní aplikace, které podporují další úlohy v rámci clusteru.

    ![Diagram znázorňující tok přenosů IP clusteru v clusteru AKS][aks-clusterip]

- **NodePort** - Vytvoří mapování portů na podkladovém uzlu, který umožňuje aplikaci přístup přímo s IP adresu uzlu a port.

    ![Diagram znázorňující tok provozu NodePort v clusteru AKS][aks-nodeport]

- **LoadBalancer** – vytvoří prostředek pro vyrovnávání zatížení Azure, nakonfiguruje externí IP adresu a připojí požadované pody k back-endovému fondu vykladače vyrovnávání zatížení. Chcete-li umožnit zákazníkům provoz k dosažení aplikace, pravidla vyrovnávání zatížení jsou vytvořeny na požadovaných portech. 

    ![Diagram znázorňující tok provozu vykladačů zatížení v clusteru AKS][aks-loadbalancer]

    Pro další řízení a směrování příchozího provozu můžete místo toho použít [řadič příchozího přenosu dat](#ingress-controllers).

- **ExternalName** - Vytvoří určitou položku DNS pro snadnější přístup k aplikacím.

Ip adresu pro vykladače zatížení a služby lze dynamicky přiřadit nebo můžete zadat existující statickou adresu IP, která se má použít. Lze přiřadit interní i externí statické IP adresy. Tato existující statická adresa IP je často vázána na položku DNS.

Lze vytvořit *interní* i *externí* vykladače zatížení. Interním vykladačům zatížení je přiřazena pouze soukromá IP adresa, takže k nim nelze získat přístup z Internetu.

## <a name="azure-virtual-networks"></a>Virtuální sítě Azure

V AKS můžete nasadit cluster, který používá jeden z následujících dvou síťových modelů:

- *Kubenet* networking – síťové prostředky jsou obvykle vytvářeny a konfigurovány při nasazení clusteru AKS.
- *Azure Container Networking Interface (CNI)* networking – cluster AKS je připojený k existujícím prostředkům a konfiguracím virtuální sítě.

### <a name="kubenet-basic-networking"></a>Kubenet (základní) networking

Možnost *kubenet* sítě je výchozí konfigurace pro vytvoření clusteru AKS. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě Azure. Pody získají IP adresu z logicky jiného adresního prostoru do podsítě virtuální sítě Azure uzlů. Překlad adres (NAT) se pak nakonfiguruje tak, aby pody mohly získat přístup k prostředkům ve virtuální síti Azure. Zdrojová IP adresa provozu je NAT'd na primární IP adresu uzlu.

Uzly používají plugin [kubenet][kubenet] Kubernetes. Platformě Azure můžete nechat vytvářet a konfigurovat virtuální sítě, nebo se rozhodnout nasadit cluster AKS do existující podsítě virtuální sítě. Opět platí, že pouze uzly přijímat směrovatelné IP adresy a pody používají NAT ke komunikaci s jinými prostředky mimo cluster AKS. Tento přístup výrazně snižuje počet adres IP, které je třeba rezervovat v síťovém prostoru pro pody k použití.

Další informace naleznete v [tématu Konfigurace sítě kubenet pro cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (pokročilé) sítě

Při použití Azure CNI získá každý pod IP adresu z podsítě a dá se k němu přistupovat přímo. Tyto adresy IP musí být jedinečné v celém síťovém prostoru a musí být naplánovány předem. Každý uzel má konfigurační parametr pro maximální počet podů, které podporuje. Ekvivalentní počet ADRES IP na uzel je pak rezervován předem pro tento uzel. Tento přístup vyžaduje další plánování, protože jinak může vést k vyčerpání IP adresy nebo k nutnosti znovu sestavit clustery ve větší podsíti, jak vaše aplikace požadavky rostou.

Uzly používají modul plug-in Kubernetes [(Azure Container Networking Interface) (Kubernetes).][cni-networking]

![Diagram znázorňující dva uzly s mosty, které se připojují k jedné virtuální síti Azure][advanced-networking-diagram]

Další informace najdete [v tématu Konfigurace Azure CNI pro cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porovnání síťových modelů

Kubenet i Azure CNI poskytují připojení k síti pro clustery AKS. Nicméně, tam jsou výhody a nevýhody pro každého. Na vysoké úrovni platí následující úvahy:

* **kubenet řekl:**
    * Šetří adresní prostor IP.
    * Používá Kubernetes interní nebo externí vyrovnávání zatížení k dosažení pody z vnějšku clusteru.
    * Je nutné ručně spravovat a udržovat uživatelem definované trasy (UDR).
    * Maximálně 400 uzlů na cluster.
* **Azure CNI**
    * Pody získají plné připojení k virtuální síti a lze je přímo dosáhnout prostřednictvím své privátní IP adresy z připojených sítí.
    * Vyžaduje více adresního prostoru IP.

Mezi kubenet a Azure CNI existují následující rozdíly chování:

| Schopnost                                                                                   | Kubenet (Kubenet)   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Nasazení clusteru v existující nebo nové virtuální síti                                            | Podporováno – ručně aplikovaná udr. | Podporuje se |
| Připojení pod-pod                                                                         | Podporuje se | Podporuje se |
| Připojení pod-vm; Virtuální počítač ve stejné virtuální síti                                          | Funguje při iniciaci pod | Funguje oběma směry |
| Připojení pod-vm; Virtuální počítač ve virtuální síti partnerské sítě                                            | Funguje při iniciaci pod | Funguje oběma směry |
| Místní přístup pomocí sítě VPN nebo expresní trasy                                                | Funguje při iniciaci pod | Funguje oběma směry |
| Přístup k prostředkům zabezpečeným koncovými body služby                                             | Podporuje se | Podporuje se |
| Vystavit služby Kubernetes pomocí služby vyrovnávání zatížení, brány aplikací nebo řadiče příchozího přenosu dat | Podporuje se | Podporuje se |
| Výchozí Azure DNS a privátní zóny                                                          | Podporuje se | Podporuje se |

Pokud jde o DNS, s kubenet a Azure CNI pluginy DNS je nabízen CoreDNS, daemon sada běží v AKS. Další informace o službě CoreDNS o kubernetes najdete [v tématu Přizpůsobení služby DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). Služba CoreDNS je nakonfigurována jako výchozí pro předávání neznámých domén serverům DNS uzlu, jinými slovy na funkce DNS virtuální sítě Azure, kde se nasadí cluster AKS. Proto Azure DNS a privátní zóny bude fungovat pro pody spuštěné v AKS.

### <a name="support-scope-between-network-models"></a>Rozsah podpory mezi modely sítě

Bez ohledu na síťový model, který používáte, lze kubenet i Azure CNI nasadit jedním z následujících způsobů:

* Platforma Azure můžete automaticky vytvořit a nakonfigurovat prostředky virtuální sítě při vytváření clusteru AKS.
* Prostředky virtuální sítě můžete ručně vytvořit a nakonfigurovat a připojit k těmto prostředkům při vytváření clusteru AKS.

I když možnosti, jako jsou koncové body služby nebo UDR jsou podporovány s kubenet a Azure CNI, [zásady podpory pro AKS][support-policies] definovat, jaké změny můžete provést. Například:

* Pokud ručně vytvoříte prostředky virtuální sítě pro cluster AKS, jste podporováni při konfiguraci vlastních udr nebo koncových bodů služby.
* Pokud platforma Azure automaticky vytvoří prostředky virtuální sítě pro váš cluster AKS, není podporována ručně změnit tyto prostředky spravované AKS a nakonfigurovat vlastní UDR nebo koncové body služby.

## <a name="ingress-controllers"></a>Kontrolery příchozího přenosu dat

Když vytvoříte službu typu LoadBalancer, vytvoří se základní prostředek nástrojpro vyrovnávání zatížení Azure. Systém vyrovnávání zatížení je nakonfigurován tak, aby distribuoval provoz do podů ve službě na daném portu. LoadBalancer funguje pouze na vrstvě 4 – služba neví o skutečné aplikace a nemůže provádět žádné další směrování úvahy.

*Řadiče příchozích dat* pracují ve vrstvě 7 a mohou používat inteligentnější pravidla k distribuci provozu aplikací. Běžné použití řadiče příchozího přenosu dat je směrovat přenosy HTTP do různých aplikací na základě příchozí adresy URL.

![Diagram znázorňující tok přenosů příchozích dat v clusteru AKS][aks-ingress]

V AKS můžete vytvořit prostředek příchozího přenosu dat pomocí něco jako NGINX nebo použít funkci směrování aplikací AKS HTTP. Když povolíte směrování aplikací HTTP pro cluster AKS, platforma Azure vytvoří řadič příchozího přenosu dat a řadič *externího DNS.* Při vytváření nových prostředků příchozího přenosu dat v Kubernetes jsou požadované záznamy DNS A vytvořeny v zóně DNS specifické pro cluster. Další informace naleznete v [tématu nasazení směrování aplikací HTTP][aks-http-routing].

Dalším běžným rysem příchozího přenosu dat je ukončení SSL/TLS. Ve velkých webových aplikacích přístupných přes protokol HTTPS může být ukončení TLS zpracováno prostředkem příchozího přenosu dat, nikoli v rámci samotné aplikace. Chcete-li zajistit automatické generování a konfiguraci certifikace TLS, můžete nakonfigurovat prostředek příchozího přenosu dat tak, aby používal zprostředkovatele, jako je Let's Encrypt. Další informace o konfiguraci řadiče příchozího přenosu dat NGINX pomocí funkce Let's Encrypt naleznete [v tématech Ingress a TLS][aks-ingress-tls].

Můžete také nakonfigurovat řadič příchozího přenosu dat tak, aby zachoval zdrojovou IP adresu klienta při požadavcích na kontejnery v clusteru AKS. Pokud je požadavek klienta směrován do kontejneru v clusteru AKS prostřednictvím řadiče příchozího přenosu dat, původní zdrojová IP adresa tohoto požadavku nebude k dispozici pro cílový kontejner. Pokud povolíte *zachování ip adresy zdroje klienta*, zdrojová IP adresa pro klienta je k dispozici v hlavičce požadavku v části *X-Forwarded-For*. Pokud používáte zachování IP ip zdroje klienta na řadiči příchozího přenosu dat, nelze použít ssl předávací. Zachování IP zdroje klienta a SSL předávací lze použít s jinými službami, jako je například *loadbalancer* typu.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupina zabezpečení sítě filtruje provoz pro virtuální společnosti, jako jsou uzly AKS. Při vytváření služeb, jako je například LoadBalancer, platforma Azure automaticky konfiguruje všechna pravidla skupiny zabezpečení sítě, která jsou potřeba. Nekonfigurujte ručně pravidla skupiny zabezpečení sítě pro filtrování provozu pro pody v clusteru AKS. Definujte všechny požadované porty a předávání jako součást manifestů služby Kubernetes a nechte platformu Azure vytvořit nebo aktualizovat příslušná pravidla. Můžete také použít zásady sítě, jak je popsáno v další části, automaticky použít pravidla filtru provozu podů.

## <a name="network-policies"></a>Zásady sítě

Ve výchozím nastavení mohou všechny pody v clusteru AKS odesílat a přijímat přenosy bez omezení. Pro lepší zabezpečení můžete definovat pravidla, která řídí tok provozu. Back-endové aplikace jsou často vystaveny pouze požadované front-endové služby nebo databázové součásti jsou přístupné pouze pro aplikační vrstvy, které se k nim připojují.

Zásady sítě je funkce Kubernetes dostupná v AKS, která umožňuje řídit tok provozu mezi pody. Můžete povolit nebo odepřít provoz na základě nastavení, jako jsou přiřazené popisky, obor názvů nebo port provozu. Skupiny zabezpečení sítě jsou spíše pro uzly AKS, nikoli pro pody. Použití zásad sítě je vhodnější, cloud-nativní způsob, jak řídit tok provozu. Jako pody jsou dynamicky vytvářeny v clusteru AKS, požadované zásady sítě lze automaticky použít.

Další informace najdete [v tématu Zabezpečené přenosy mezi pody pomocí zásad sítě ve službě Azure Kubernetes Service (AKS).][use-network-policies]

## <a name="next-steps"></a>Další kroky

Chcete-li začít pracovat se sítí AKS, vytvořte a nakonfigurujte cluster AKS s vlastními rozsahy IP adres pomocí [kubenet][aks-configure-kubenet-networking] nebo [Azure CNI][aks-configure-advanced-networking].

Doporučené postupy najdete v [tématu Doporučené postupy pro připojení k síti a zabezpečení v AKS][operator-best-practices-network].

Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / AKS zabezpečení][aks-concepts-security]
- [Kubernetes / AKS úložiště][aks-concepts-storage]
- [Kubernetes / AKS váha][aks-concepts-scale]

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
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
