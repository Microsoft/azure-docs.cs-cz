---
title: Principy – sítě do služby Azure Kubernetes (AKS)
description: Další informace o možnostech sítě v Azure Kubernetes Service (AKS), včetně kubenet a sítě Azure CNI, řadiče příchozího přenosu dat, nástroje pro vyrovnávání zatížení a statické IP adresy.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 6affa19c61ff4a824e390c42b7fd97554a30c9bb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176233"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)

V mikroslužeb založených na kontejnerech přístup k vývoji aplikací komponenty aplikace musí spolupracovat na zpracování jejich úloh. Kubernetes poskytuje různé prostředky, které umožňují komunikaci této aplikace. Můžete připojit k a vystavit aplikace interně nebo externě. Pokud chcete vytvořit vysoce dostupné aplikace, můžete načíst vyvážit vašich aplikací. Složitější aplikace můžou vyžadovat konfigurace příchozímu přenosu pro ukončení protokolu SSL/TLS, nebo směrují z několika součástí. Z bezpečnostních důvodů budete také muset omezení toku síťového provozu do nebo mezi uzly a podů.

Tento článek představuje základní koncepty, které poskytují sítě pro vaše aplikace ve službě AKS:

- [Služby](#services)
- [Virtuální sítě Azure](#azure-virtual-networks)
- [Kontrolery příchozího přenosu dat](#ingress-controllers)
- Zásady sítě

## <a name="kubernetes-basics"></a>Základy Kubernetes

Pokud chcete povolit přístup pro vaše aplikace nebo součásti aplikace komunikovat mezi sebou, Kubernetes poskytuje abstraktní vrstvu do virtuálních sítí. Uzly Kubernetes jsou připojené k virtuální síti a může poskytnout příchozí a odchozí připojení pro tyto pody. *Kube proxy* komponenty běží na každém uzlu poskytnout tyto funkce v síti.

V systému Kubernetes *služby* logicky seskupit podů umožňující přímý přístup přes IP adresu nebo název DNS a na určitém portu. Můžete také distribuovat provoz pomocí *nástroj pro vyrovnávání zatížení*. Složitější směrování provozu aplikace lze také nastavit pomocí *příchozího přenosu dat řadiče*. Zabezpečení a filtrování síťového provozu pro podů je možné s využitím Kubernetes *zásady sítě, které*.

Platforma Azure také pomáhá zjednodušit virtuální sítě pro clustery AKS. Když vytvoříte nástroj pro vyrovnávání zatížení Kubernetes, základní prostředek nástroje pro vyrovnávání zatížení Azure je vytvoření a konfiguraci. Při otevření síťových portů podů, jsou nakonfigurovány odpovídající pravidla skupiny zabezpečení sítě Azure. Směrování aplikace HTTP, můžete taky nakonfigurovat Azure *externí DNS* jako nové příchozího přenosu dat jsou nakonfigurované trasy.

## <a name="services"></a>Služby

Pro zjednodušení konfigurace sítě pro úlohy aplikací používá Kubernetes *služby* logicky seskupit sadu podů a poskytovat připojení k síti. K dispozici jsou následující typy služeb:

- **Adresa IP clusteru** -vytvoří interní IP adresa pro použití v rámci clusteru AKS. Vhodné pro pouze interní aplikace, které podporují jiné úlohy v rámci clusteru.

    ![Diagram znázorňující tok přenosů IP clusteru v clusteru AKS][aks-clusterip]

- **NodePort** -vytvoří mapování portů na základní uzel, který umožňuje aplikaci získat přístup přímo k uzlu IP adrese a portu.

    ![Diagram znázorňující tok provozu NodePort v clusteru AKS][aks-nodeport]

- **Nástroj pro vyrovnávání zatížení** – vytvoří prostředek nástroje pro vyrovnávání zatížení Azure, nakonfiguruje externí IP adresu a požadovaná podů se připojí k back-endového fondu nástroje pro vyrovnávání zatížení. Na požadované porty se vytvoří pro povolení provozu zákazníků na dosah aplikací, pravidla Vyrovnávání zatížení. 

    ![Diagram znázorňující tok provozu nástroje pro vyrovnávání zatížení v clusteru AKS][aks-loadbalancer]

    Pro větší kontrolu a směrování příchozího provozu, můžete místo toho použít [kontroler příchozího přenosu dat](#ingress-controllers).

- **ExternalName** -vytvoří konkrétní záznam DNS pro snadnější přístup k aplikaci.

Můžete dynamicky přidělit IP adresu nástroje pro vyrovnávání zatížení a služeb, nebo můžete zadat existující statické IP adresy používat. Interní a externí statické IP adresy můžete přiřadit. Tato existující statická IP adresa se často váže na položku DNS.

Obě *interní* a *externí* nástroje pro vyrovnávání zatížení je možné vytvořit. Interní služby load balancer mají přiřazenou pouze privátní IP adresu, takže není přístupný z Internetu.

## <a name="azure-virtual-networks"></a>Virtuální sítě Azure

Ve službě AKS můžete nasadit cluster, který používá jednu z následujících dvou síťových modely:

- *Kubenet* síťové služby – síťové prostředky jsou obvykle vytvoření a konfiguraci při nasazení clusteru AKS.
- *Azure Container síťové rozhraní (CNI)* sítě – clusteru AKS je připojený k existující prostředky virtuální sítě a konfigurace.

### <a name="kubenet-basic-networking"></a>Sítě Kubenet (basic)

*Kubenet* sítě možnost je výchozí konfigurace pro vytvoření clusteru AKS. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě Azure. Podů obdrží IP adresu z logicky jiným adresním prostorem podsítě virtuální sítě Azure z uzlů. Překlad síťových adres (NAT) je nakonfigurovaný tak, aby tyto pody může mít přístup k prostředkům ve službě Azure virtual network. Síťový provoz na IP adresu zdrojového je že NAT by uzlu primární IP adresa.

Použití uzlů [kubenet] [ kubenet] modulu plug-in Kubernetes. Můžete nechat na platformě Azure, vytvořit a konfigurovat virtuální sítě pro vás nebo zvolit pro nasazení clusteru AKS do existující podsítě virtuální sítě. Pouze uzly přijímají směrovatelné IP adresy a podů opět pomocí NAT komunikovat s ostatními prostředky mimo AKS cluster. Tento přístup výrazně snižuje počet IP adres, které je nutné rezervovat v prostoru podů použití vaší sítě.

Další informace najdete v tématu [konfigurace sítě pro AKS cluster kubenet][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Sítě Azure CNI (rozšířené)

S Azure CNI každý pod získá IP adresu z podsítě a je přístupný přímo. Tyto IP adresy musí být jedinečný v rámci prostor vaší sítě a je třeba se připravit předem. Každý uzel má parametr konfigurace pro maximální počet podů, které podporuje. Ekvivalentní počet IP adres na jedno uzlu jsou pak ještě před zahájením vyhrazené pro tento uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adresu nebo nutnosti znovu sestavovat clusterů ve větší podsíť, jak rostou vaše požadavky aplikace růst.

Použití uzlů [služby Azure Container síťové rozhraní (CNI)] [ cni-networking] modulu plug-in Kubernetes.

![Diagram znázorňující dva uzly s mostů propojení každý z nich jedné virtuální sítě Azure][advanced-networking-diagram]

Azure CNI poskytuje následující funkce nad kubenet sítě:

- Každý pod v clusteru je přiřazená IP adresa ve virtuální síti. Tyto pody může komunikovat přímo s dalších podů v clusteru a ostatní uzly ve virtuální síti.
- Podů v podsíti, které mají povolené koncové body služeb můžete bezpečně připojit ke službám Azure, jako je například Azure Storage a SQL DB.
- Nemůžete vytvářet trasy definované uživatelem (UDR) tak, aby směrovat provoz z podů na síťové virtuální zařízení.

Další informace najdete v tématu [konfigurovat Azure CNI pro AKS cluster][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Kontrolery příchozího přenosu dat

Když vytvoříte nástroj pro vyrovnávání zatížení typ služby, se vytvoří prostředek základní nástroje pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení je nakonfigurován za účelem distribuce provozu do podů ve službě na daném portu. Funguje pouze nástroj pro vyrovnávání zatížení vrstvy 4 – služba nebude vědět o vlastní aplikace a nemůže provádět žádné další aspekty směrování.

*Příchozí přenos dat řadiče* pracovat ve vrstvě 7 a využívat inteligentnější pravidla za účelem distribuce provozu aplikace. Je běžné použití kontroleru příchozího přenosu dat pro směrování provozu HTTP na jinou aplikací založených na příchozí adrese URL.

![Diagram znázorňující tok provozu příchozího přenosu dat v clusteru AKS][aks-ingress]

Ve službě AKS můžete vytvořit prostředek příchozího přenosu dat pomocí příkazu podobného tomuto serveru NGINX, nebo použít funkci směrování aplikace AKS HTTP. Když povolíte směrování aplikace HTTP pro AKS cluster, Platforma Azure vytvoří kontroler příchozího přenosu dat a *externí DNS* kontroleru. Při vytváření nových prostředků příchozího přenosu dat v Kubernetes se vyžaduje DNS A záznamy vytvořené v zóně DNS specifické pro cluster. Další informace najdete v tématu [nasazení směrování aplikace HTTP][aks-http-routing].

Další běžnou funkcí příchozího přenosu dat se ukončení protokolu SSL/TLS. Ukončení protokolu TLS na velké webové aplikace přistupuje přes protokol HTTPS, může být zpracována prostředků příchozího přenosu dat a nikoli v rámci vlastní aplikace. Poskytnout automatické generování certifikaci TLS a konfigurace, můžete nakonfigurovat prostředek příchozího přenosu dat, který má být použit poskytovatelů, jako je například umožňuje šifrovat. Další informace o konfiguraci řadič příchozího přenosu dat serveru NGINX se teď šifrování, najdete v části [Ingress a protokol TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupina zabezpečení sítě pro virtuální počítače, filtruje provoz, jako je například uzlů AKS. Při vytváření služby, jako je nástroji pro vyrovnávání zatížení, Platforma Azure automaticky nakonfiguruje všechny pravidla skupiny zabezpečení sítě, které jsou potřeba. Nekonfigurujte ručně pravidla skupiny zabezpečení sítě pro filtrování provozu pro podů v clusteru AKS. Definovat všechny požadované porty a předávání jako součást vaší služby Kubernetes manifestů a nechat na platformě Azure, vytvořit nebo aktualizovat pomocí příslušných pravidel. Můžete také použít zásady sítě, jak je popsáno v další části, automaticky použít pravidla filtrování přenosů pro pody.

Výchozí skupina zabezpečení sítě, které existují pravidla pro provoz jako je SSH. Tyto výchozí pravidla se pro správu clusteru a řešení potíží s přístupem. Odstranění těchto pravidel výchozí může způsobit problémy se správou AKS a přeruší cíl na úrovni služby (SLO).

## <a name="network-policies"></a>Zásady sítě

Ve výchozím nastavení můžete všechny podů v clusteru AKS odesílat a přijímat provoz bez omezení. Pro lepší zabezpečení můžete definovat pravidla, která řídí tok provozu. Back-endové aplikace jsou často dostupná jenom v případě do požadované front-endové služby nebo databáze součásti jsou pouze přístupné aplikačních vrstev, které k nim připojit.

Zásady sítě je funkce, Kubernetes, která umožňuje řídit tok přenosů mezi pody. Můžete povolit nebo zakázat provoz na základě nastavení, jako jsou přiřazená popisky, obor názvů nebo provoz portu. Skupiny zabezpečení sítě se více uzlů AKS, není pody. Použití zásady sítě je vhodnější, nativní pro cloud způsob, jak řídit tok provozu. Při vytváření podů v clusteru AKS se dynamicky, požadovaná šířka zásady je automaticky použít.

Další informace najdete v tématu [zabezpečení přenosu mezi pody pomocí zásady sítě ve službě Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Další postup

Začínáme se službou AKS sítí, vytvoření a konfigurace clusteru AKS pomocí vlastní rozsahy IP adres pomocí [kubenet] [ aks-configure-kubenet-networking] nebo [Azure CNI] [ aks-configure-advanced-networking].

Další informace o základní Kubernetes a AKS koncepty najdete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / zabezpečení AKS][aks-concepts-security]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes snížit nebo navýšit AKS][aks-concepts-scale]

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
