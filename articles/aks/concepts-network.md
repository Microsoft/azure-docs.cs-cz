---
title: Koncepty – sítě v Azure Kubernetes Services (AKS)
description: Přečtěte si o sítích ve službě Azure Kubernetes Service (AKS), včetně kubenet a Azure CNI, řadičů pro příjem dat, nástrojů pro vyrovnávání zatížení a statických IP adres.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105336"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)

V rámci přístupu ke službě mikroslužeb založených na kontejneru a při vývoji aplikací pracují komponenty aplikace společně a zpracovávají jejich úkoly. Kubernetes poskytuje různé prostředky, které umožňují tuto spolupráci:
* Můžete se připojit k aplikacím a vystavit je interně nebo externě. 
* Pomocí vyrovnávání zatížení aplikací můžete vytvářet vysoce dostupné aplikace. 
* U složitějších aplikací můžete nakonfigurovat příchozí přenos dat pro ukončení protokolu SSL/TLS nebo pro směrování více součástí. 
* Z bezpečnostních důvodů můžete omezit tok síťového provozu do nebo mezi lusky a uzly.

V tomto článku se seznámíte se základními koncepcemi, které poskytují sítě vašim aplikacím v AKS:

- [Služby](#services)
- [Virtuální sítě Azure](#azure-virtual-networks)
- [Kontrolery příchozích dat](#ingress-controllers)
- [Zásady sítě](#network-policies)

## <a name="kubernetes-basics"></a>Základy Kubernetes

Pro povolení přístupu k vašim aplikacím nebo mezi komponentami aplikace Kubernetes poskytuje abstrakci vrstvu pro virtuální sítě. Uzly Kubernetes se připojují k virtuální síti a poskytují příchozí a odchozí připojení pro lusky. Komponenta *Kube-proxy* se spouští na všech uzlech, aby poskytovala tyto síťové funkce.

V Kubernetes:
* *Služby* logicky seskupují, aby umožňovaly přímý přístup k určitému portu prostřednictvím IP adresy nebo názvu DNS. 
* Provoz můžete distribuovat pomocí *Nástroje pro vyrovnávání zatížení*. 
* Složitější směrování provozu s aplikacemi se dá dosáhnout i u *řadičů* příchozího přenosu dat. 
* Zabezpečení a filtrování síťového provozu pro lusky je možné u *zásad sítě* Kubernetes.

Platforma Azure taky zjednodušuje virtuální sítě pro clustery AKS. Když vytvoříte Nástroj pro vyrovnávání zatížení Kubernetes, vytvoříte a nakonfigurujete základní prostředek nástroje pro vyrovnávání zatížení Azure. Při otevírání síťových portů do lusků jsou nakonfigurovaná odpovídající pravidla skupiny zabezpečení sítě Azure. Pro směrování aplikací HTTP může Azure nakonfigurovat také *externí DNS* , protože jsou nakonfigurované nové trasy příchozího přenosu dat.

## <a name="services"></a>Služby

Pro zjednodušení konfigurace sítě pro úlohy aplikací Kubernetes používá *služby* k logickému seskupení sady lusků a zajištění připojení k síti. K dispozici jsou následující typy služeb:

- **IP adresa clusteru** 
  
  Vytvoří interní IP adresu pro použití v rámci clusteru AKS. Vhodné pro interní aplikace, které podporují jiné úlohy v rámci clusteru.

    ![Diagram znázorňující tok přenosů IP adres clusteru v clusteru AKS][aks-clusterip]

- **NodePort** 

  Vytvoří mapování portů na podkladovém uzlu, který umožňuje, aby aplikace byla k dispozici přímo s IP adresou a portem uzlu.

    ![Diagram znázorňující tok přenosů NodePort v clusteru AKS][aks-nodeport]

- **LoadBalancer** 

  Vytvoří prostředek nástroje pro vyrovnávání zatížení Azure, nakonfiguruje externí IP adresu a připojí požadované lusky k back-endu služby Vyrovnávání zatížení. Aby bylo možné v provozu zákazníků dosáhnout aplikace, budou pravidla vyrovnávání zatížení vytvořena na požadovaných portech. 

    ![Diagram znázorňující Load Balancer tok provozu v clusteru AKS][aks-loadbalancer]

    Pro další kontrolu a směrování příchozího provozu můžete místo toho použít [kontroler](#ingress-controllers)příchozího přenosu dat.

- **Extern** 

  Vytvoří specifickou položku DNS pro snazší přístup k aplikaci.

Je možné dynamicky přiřadit IP adresy a služby Vyrovnávání zatížení, nebo můžete zadat existující statickou IP adresu. Můžete přiřadit interní i externí statické IP adresy. Existující statické IP adresy jsou často vázané na záznam DNS.

Můžete vytvořit *interní* i *externí* nástroje pro vyrovnávání zatížení. Interním nástrojům pro vyrovnávání zatížení je přiřazena pouze privátní IP adresa, takže k nim nelze přicházet z Internetu.

## <a name="azure-virtual-networks"></a>Virtuální sítě Azure

V AKS můžete nasadit cluster, který používá jeden z následujících dvou síťových modelů:

- *Kubenet* sítě

  Síťové prostředky se obvykle vytváří a konfigurují při nasazení clusteru AKS.

- Sítě *Azure Container Networking Interface (CNI)*
 
  Cluster AKS je připojený k prostředkům a konfiguracím stávající virtuální sítě.

### <a name="kubenet-basic-networking"></a>Kubenet (základní) sítě

Možnost sítě *kubenet* je výchozí konfigurace pro vytváření clusteru AKS. S *kubenet*:
1. Uzly obdrží IP adresu z podsítě virtuální sítě Azure. 
1. Lusky obdrží IP adresu z logického jiného adresního prostoru než z podsítě virtuální sítě Azure. 
1. Překlad adres (NAT) se pak nakonfiguruje tak, aby pody mohly získat přístup k prostředkům ve virtuální síti Azure. 
1. Zdrojová IP adresa provozu je přeložena na primární IP adresu uzlu.

Uzly používají modul plug-in [kubenet][kubenet] Kubernetes. Další možnosti:
* Umožněte, aby platforma Azure vytvořila a nakonfigurovala virtuální sítě za vás. 
* Vyberte, chcete-li nasadit cluster AKS do existující podsítě virtuální sítě. 

Nezapomeňte, že pouze uzly obdrží IP adresu směrování. Lusky používají překlad adres (NAT) ke komunikaci s dalšími prostředky mimo cluster AKS. Tento přístup snižuje počet IP adres, které je třeba rezervovat v síťovém prostoru pro použití v luskech.

Další informace najdete v tématu [Konfigurace sítě kubenet pro cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sítě Azure CNI (rozšířené)

Při použití Azure CNI získá každý pod IP adresu z podsítě a dá se k němu přistupovat přímo. Tyto IP adresy musí být plánovány předem a jedinečné v rámci vašeho síťového prostoru. Každý uzel má parametr konfigurace maximálního počtu lusků, které podporuje. Současně se rezervuje ekvivalentní počet IP adres na uzel. Bez plánování tento přístup může vést k vyčerpání IP adres nebo nutnosti znovu sestavovat clustery ve větší podsíti, protože vaše aplikace vyžaduje růst.

Na rozdíl od kubenet provoz do koncových bodů ve stejné virtuální síti není NAT k primární IP adrese uzlu. Zdrojová adresa pro přenos dat ve virtuální síti je pod IP adresou pod. Přenosy, které jsou externí pro virtuální síť, jsou pořád NAT na primární IP adresu uzlu.

Uzly používají modul plug-in [Azure CNI][cni-networking] Kubernetes.

![Diagram znázorňující dva uzly s mostem připojujícím se každé virtuální síti Azure][advanced-networking-diagram]

Další informace najdete v tématu [konfigurace Azure CNI pro cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Porovnání síťových modelů

Kubenet i Azure CNI poskytují připojení k síti pro vaše clustery AKS. Existují však i výhody a nevýhody. Na nejvyšší úrovni platí následující požadavky:

* **kubenet**
    * Zachovává adresní prostor IP adres.
    * Používá interní nebo externí nástroj pro vyrovnávání zatížení k dosažení lusků mimo cluster.
    * Ručně můžete spravovat a udržovat trasy definované uživatelem (udr).
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

V souvislosti se službou DNS nabízí CoreDNS nasazení, které se spouští v AKS s vlastním nástrojem pro automatické škálování (kubenet) a služby Azure CNI plugins. Další informace o CoreDNS v Kubernetes najdete v tématu [přizpůsobení služby DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS ve výchozím nastavení je nakonfigurované pro přeposílání neznámých domén do funkce DNS Azure Virtual Network, kde je nasazený cluster AKS. Proto Azure DNS a soukromé zóny budou fungovat pro lusky běžící v AKS.

### <a name="support-scope-between-network-models"></a>Rozsah podpory mezi síťovými modely

Libovolný model sítě, který použijete, můžete nasadit jak kubenet, tak i Azure CNI, a to jedním z těchto způsobů:

* Platforma Azure může automaticky vytvářet a konfigurovat prostředky virtuální sítě při vytváření clusteru AKS.
* Při vytváření clusteru AKS můžete ručně vytvořit a nakonfigurovat prostředky virtuální sítě a připojit se k těmto prostředkům.

I když jsou podporované možnosti, jako jsou koncové body služby nebo udr, kubenet i Azure CNI, [zásady podpory pro AKS][support-policies] definují, jaké změny můžete provádět. Například:

* Pokud ručně vytvoříte prostředky virtuální sítě pro cluster AKS, budete podporováni při konfiguraci vlastních koncových bodů udr nebo služby.
* Pokud platforma Azure automaticky vytvoří prostředky virtuální sítě pro cluster AKS, nemůžete ručně změnit tyto prostředky spravované AKS a nakonfigurovat vlastní udr nebo koncové body služby.

## <a name="ingress-controllers"></a>Kontrolery příchozích dat

Když vytvoříte službu typu Vyrovnávání zatížení, vytvoříte také základní prostředek nástroje pro vyrovnávání zatížení Azure. Nástroj pro vyrovnávání zatížení je nakonfigurovaný k distribuci provozu do lusků ve vaší službě na daném portu. 

Nástroj pro vyrovnávání zatížení funguje pouze ve vrstvě 4. Ve vrstvě 4 služba neví o samotných aplikacích a nemůže dělat žádné další okolnosti směrování.

*Řadiče* příchozího přenosu dat pracují ve vrstvě 7 a můžou použít více inteligentních pravidel k distribuci provozu aplikací. Řadiče příchozího přenosu dat obvykle směrují provoz HTTP do různých aplikací na základě příchozí adresy URL.

![Diagram znázorňující tok příchozího provozu v clusteru AKS][aks-ingress]

### <a name="create-an-ingress-resource"></a>Vytvoření prostředku příchozího přenosu dat
V AKS můžete vytvořit prostředek příchozího přenosu dat pomocí NGINX, podobného nástroje nebo funkce směrování aplikace HTTP AKS. Když zapnete směrování aplikace HTTP pro cluster AKS, platforma Azure vytvoří kontroler příchozího přenosu dat a *externí řadič DNS* . Při vytváření nových prostředků příchozího přenosu v Kubernetes se požadované záznamy DNS vytvoří v zóně DNS specifické pro clustery. 

Další informace najdete v tématu [nasazení směrování aplikace http][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Kontroler příchozího přenosu Application Gateway (AGIC)

Díky doplňku AGIC (Application Gateway příchozího řadiče) využívají zákazníci AKS Application Gateway nativní Nástroj pro vyrovnávání zatížení Azure úrovně 7 a zveřejňují cloudový software na Internet. AGIC sleduje cluster hostitele Kubernetes a průběžně aktualizuje Application Gateway a zpřístupňuje vybrané služby na internetu. 

Další informace o doplňku AGIC pro AKS najdete v tématu [co je Application Gateway kontroler][agic-overview]příchozího přenosu dat?.

### <a name="ssltls-termination"></a>Ukončení protokolu SSL/TLS

Ukončení protokolu SSL/TLS je další běžná funkce příchozího přenosu dat. V případě rozsáhlých webových aplikací, které jsou k dispozici prostřednictvím protokolu HTTPS, zpracovává příchozí prostředek protokol TLS namísto samotné aplikace. Pokud chcete zajistit automatické generování a konfiguraci certifikace TLS, můžete nakonfigurovat prostředek příchozího přenosu dat tak, aby používal zprostředkovatele, jako je například "šifrovat". 

Další informace o konfiguraci kontroleru NGINX příchozího přenosu dat pomocí šifry najdete v tématu příchozí [a TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Zachování IP adresy zdrojového klienta

Nakonfigurujte kontroler příchozího přenosu dat, aby se zachovala zdrojová IP adresa klienta v požadavcích na kontejnery v clusteru AKS. Když váš kontroler příchozího přenosu směruje požadavek klienta na kontejner v clusteru AKS, původní zdrojová IP adresa této žádosti není k dispozici pro cílový kontejner. Když povolíte *zachování IP adresy zdrojového klienta*, je zdrojová IP adresa pro klienta k dispozici v hlavičce žádosti v části *X-předáno-pro*. 

Pokud používáte zachování IP adresy zdrojového klienta v řadiči příchozího přenosu dat, nemůžete použít průchozí protokol TLS. U jiných služeb, jako je třeba typ *Vyrovnávání zatížení* sítě, můžete použít předávání IP adres klienta a předávací protokol TLS.

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupina zabezpečení sítě filtruje provoz pro virtuální počítače, jako jsou AKS uzly. Při vytváření služeb, jako je třeba nástroj pro vyrovnávání zatížení, platforma Azure automaticky konfiguruje všechna nezbytná pravidla skupiny zabezpečení sítě. 

Nemusíte ručně konfigurovat pravidla skupiny zabezpečení sítě pro filtrování provozu pro lusky v clusteru AKS. Jednoduše definujte všechny požadované porty a předávání jako součást vašich manifestů služby Kubernetes. Umožněte, aby platforma Azure vytvořila nebo aktualizovala příslušná pravidla. 

Zásady sítě můžete použít také k automatickému použití pravidel filtru přenosu na lusky.

## <a name="network-policies"></a>Zásady sítě

Ve výchozím nastavení mohou všechny lusky v clusteru AKS odesílat a přijímat přenosy bez omezení. Pro lepší zabezpečení definujte pravidla, která řídí tok přenosů, například:
* Back-endové aplikace se zveřejňují jenom u požadovaných služeb front-endu. 
* Součásti databáze jsou dostupné jenom pro aplikační vrstvy, které k nim připojovat.

Zásada sítě je funkce Kubernetes, která je dostupná v AKS, která umožňuje řídit tok přenosů mezi lusky. V závislosti na nastaveních, jako jsou přiřazené popisky, obory názvů nebo přenosový port, povolíte nebo zakážete provoz. I když jsou skupiny zabezpečení sítě lepší pro uzly AKS, zásady sítě představují pružnější způsob, jak řídit tok provozu pro lusky. V případě, že se v clusteru AKS dynamicky vytvářejí lusky, je možné automaticky použít požadované zásady sítě.

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
