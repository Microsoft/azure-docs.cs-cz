---
title: Koncepty – diagram sítě pro Azure Red Hat na OpenShift 4
description: Diagram sítě a přehled pro sítě Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: 5d69aacb6e3f25e3414aa446c4c5ae7852cabdfc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720898"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Koncepty sítě pro Azure Red Hat OpenShift (ARO)

Tato příručka obsahuje přehled sítí v Azure Red Hat OpenShift na clusterech OpenShift 4 společně s diagramem a seznamem důležitých koncových bodů. Další informace o základních konceptech OpenShift sítě najdete v [dokumentaci k síti Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Diagram sítě Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Když nasadíte Azure Red Hat na OpenShift 4, celý cluster je obsažený ve virtuální síti. V této virtuální síti jsou hlavní uzly a pracovní uzly každé živé v vlastní podsíti. Každá podsíť používá interní nástroj pro vyrovnávání zatížení a veřejný Nástroj pro vyrovnávání zatížení.

## <a name="networking-components"></a>Síťové komponenty

Následující seznam obsahuje důležité síťové součásti v clusteru Azure Red Hat OpenShift.

* **ARO – jiných pracovních prostorů**
    * Toto je koncový bod privátního propojení Azure, který používají technici pro spolehlivost Microsoftu a Red Hat ke správě clusteru.
* **ARO – interní – 9,1**
    * Tento koncový bod vyrovnává provoz se serverem rozhraní API. Pro tento nástroj pro vyrovnávání zatížení jsou hlavní uzly ve fondu back-endu.
* **ARO – veřejná – 9,1**
    * Pokud je rozhraní API veřejné, směruje tento koncový bod a vyrovnává provoz na server rozhraní API. Tento koncový bod přiřadí veřejnou odchozí IP adresu, aby mohly hlavní servery získat přístup k Azure Resource Manager a nahlásit se zpátky na stav clusteru.
* **ARO – interní**
    * Tento koncový bod vyvažuje za interní provoz služby. Pro tento nástroj pro vyrovnávání zatížení jsou pracovní uzly ve fondu back-endu.
    * Tento nástroj pro vyrovnávání zatížení není ve výchozím nastavení vytvořen. Tento nástroj pro vyrovnávání zatížení se vytvoří po vytvoření služby typu Vyrovnávání zatížení se správnými poznámkami. Příklad: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **ARO – interní – 9,1**
    * Tento koncový bod se používá pro všechny veřejné přenosy. Když vytváříte aplikaci a trasu, jedná se o cestu k provozu příchozího přenosu dat.
    * Tento nástroj pro vyrovnávání zatížení zahrnuje také odchozí připojení k Internetu z libovolného uzlu běžícího v pracovních uzlech prostřednictvím odchozích pravidel Azure Load Balancer.
        * V současné době se odchozí pravidla nedají konfigurovat. Každý uzel přiděluje 1 024 portů TCP.
        * DisableOutboundSnat není v pravidlech pro odchodu nakonfigurovaná, takže lusky by mohly mít jako výstupní IP adresu jakoukoli veřejnou IP adresu nakonfigurovanou v této ALB.
        * V důsledku obou předchozích bodů je jediným způsobem, jak přidat dočasné porty SNAT, přidávat do společnosti ARO veřejné služby typu Vyrovnávání zatížení.
* **ARO – odchozí – PIP**
    * Tento koncový bod slouží jako veřejná IP adresa (PIP) pro pracovní uzly.
    * Tento koncový bod umožňuje službám přidat konkrétní IP adresu z clusteru Azure Red Hat OpenShift do seznamu povolených serverů.
* **ARO – NSG**
    * Když vystavíte službu, rozhraní API vytvoří v této skupině zabezpečení sítě pravidlo, které umožní tok a přístup k rovině a uzlům ovládacího prvku.
    * Ve výchozím nastavení je tato skupina zabezpečení sítě povolena u všech odchozích přenosů. V současné době je možné odchozí provoz omezit jenom na rovinu ovládacího prvku Azure Red Hat OpenShift.
* **ARO – controlplane-NSG**
  * Tento koncový bod povoluje provoz pouze do zadání přes port 6443 pro hlavní uzly.
* **Azure Container Registry**
    * Toto je registr kontejneru, který poskytuje a interně používá společnost Microsoft. Tento registr je jen pro čtení a není určený pro použití uživateli Azure Red Hat OpenShift.
        * V tomto registru jsou k dispozici image platformy hostitele a součásti clusteru. Například kontejnery monitorování nebo protokolování.
        * K tomuto registru dojde prostřednictvím koncového bodu služby (interní připojení mezi službami Azure).
        * Tento interní registr není ve výchozím nastavení k dispozici mimo cluster.
* **Privátní propojení**
    * Umožňuje připojení k síti z roviny pro správu do clusteru pro spolehlivost specialistů na Microsoft a Red Hat, které vám pomůžou se správou clusteru.

## <a name="networking-policies"></a>Zásady sítě

* Příchozí **: zásada** příchozího připojení k síti je podporovaná jako součást [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html). Tato zásada sítě je ve výchozím nastavení povolená a jejich vynucení provádí uživatelé. I když je zásada příchozí sítě v režimu v1 kompatibilní s NetworkPolicy, nepodporují se typy výstupů a IPBlock.

* **Odchozí**: odchozí zásady sítě se podporují pomocí funkce [odchozího firewallu](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) v OpenShift. Pro každý obor názvů nebo projekt existuje jenom jedna zásada pro výstup. Zásady odchozího přenosu dat nejsou v oboru názvů default podporovány a jsou vyhodnocovány v pořadí (první až poslední).

## <a name="networking-basics-in-openshift"></a>Základy sítě v OpenShift

OpenShift software definované sítě [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) se používá ke konfiguraci překryté sítě pomocí Open virtuálního přepínače [(OVS)](https://www.openvswitch.org/), implementace technologii OpenFlow založeného na specifikaci CNI (Container Network Interface). SDN podporuje různé moduly plug-in – zásada sítě je modul plug-in používaný v Azure Red Hat na OpenShift 4. Veškerá síťová komunikace je spravovaná pomocí SDN, takže ve vašich virtuálních sítích nejsou potřeba žádné další trasy, aby bylo možné využít komunikaci pod technologií pod.

## <a name="networking--for-azure-red-hat-openshift"></a>Sítě pro Azure Red Hat OpenShift

Následující síťové funkce jsou specifické pro Azure Red Hat OpenShift:  
* Uživatelé můžou vytvořit svůj cluster ARO v existující virtuální síti nebo vytvořit virtuální síť při vytváření clusteru ARO.
* CIDRs síť pod a služba se dají konfigurovat.
* Uzly a hlavní servery jsou v různých podsítích.
* Podsítě uzlů a virtuálních sítí virtuálních sítí by měly být minimálně/27.
* Výchozí hodnota pod směrováním CIDR je 10.128.0.0/14.
* Výchozí CIDR služby je 172.30.0.0/16.
* CIDRs a síť služby by se neměly překrývat s ostatními rozsahy adres, které se ve vaší síti používají, a nesmí být v rozsahu IP adres virtuálních sítí vašeho clusteru.
* V případě CIDR by měla být velikost minimálně/18. (Síť pod Nesměrovatelné IP adresy se používá jenom uvnitř OpenShift SDN.)
* Každému uzlu je přiděleno/23 podsítě (512 IP adres) pro své lusky. Tato hodnota se nedá změnit.
* Nemůžete připojit objekt pod k více sítím.
* Nemůžete nakonfigurovat statickou statickou IP adresu. (Jedná se o funkci OpenShift. Informace najdete v tématu [Konfigurace odchozích IP adres](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html).

## <a name="network-settings"></a>Nastavení sítě

Pro clustery Azure Red Hat OpenShift 4 jsou k dispozici následující nastavení sítě:

* **Viditelnost rozhraní API** – při spuštění [příkazu AZ ARO Create](tutorial-create-cluster.md#create-the-cluster)se nastaví viditelnost rozhraní API.
    * Veřejné sítě mají přístup k serveru rozhraní API Public.
    * "Privátní" Server rozhraní API přiřadil privátní IP adresu z podsítě hlavních serverů, k dispozici pouze pomocí propojených sítí (s partnerskými virtuálními sítěmi, jinými podsítěmi v clusteru). V zastoupení zákazníka se vytvoří privátní zóna DNS.
* **Viditelnost** příchozího přenosu – při spuštění [příkazu AZ ARO Create](tutorial-create-cluster.md#create-the-cluster)se nastaví viditelnost rozhraní API.
    * "Veřejné" trasy budou ve výchozím nastavení veřejné Standard Load Balancer (může být změněno).
    * "Soukromé" trasy budou výchozím nastavením interního nástroje pro vyrovnávání zatížení (Tento stav může být změněn).

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Skupiny zabezpečení sítě se vytvoří v rámci skupiny prostředků uzlu, která je pro uživatele zamčená. Skupiny zabezpečení sítě se přiřazují přímo k podsítím, nikoli na síťových kartách uzlu. Skupiny zabezpečení sítě jsou neměnné a uživatelé k jejich změně nemají oprávnění.

S veřejně viditelným serverem API nemůžete vytvářet skupiny zabezpečení sítě a přiřazovat je k síťovým kartám.

## <a name="domain-forwarding"></a>Předávání domény
Azure Red Hat OpenShift používá CoreDNS. Je možné nakonfigurovat předávání domény. Do virtuálních sítí nemůžete přenášet vlastní DNS. Další informace najdete v dokumentaci k [používání předávání DNS](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Co je nového v OpenShift 4,5

Díky podpoře OpenShift 4,5 představila Azure Red Hat OpenShift několik významných změn architektury. Tyto změny se vztahují jenom na nově vytvořené clustery s OpenShift 4,5. Stávající clustery, které byly upgradovány na OpenShift 4,5, nebudou mít v procesu upgradu změněny své síťové architektury. Uživatelé budou muset znovu vytvořit své clustery, aby mohli používat tuto novou architekturu.

![Diagram sítě Azure Red Hat OpenShift 4,5](./media/concepts-networking/aro-4-5-networking-diagram.png)

Jak je uvedeno výše v diagramu výše, všimnete si několika změn:
* Dříve používaly ARO dvě veřejné LoadBalancers: jeden pro Server rozhraní API a jeden pro fond pracovních uzlů. S touto aktualizací architektury se tato aktualizace konsoliduje v rámci jediného vyrovnávání zatížení. 
* Z důvodu omezení složitosti byly odebrány vyhrazené prostředky IP adresy.
* Řídicí rovina ARO teď sdílí stejnou skupinu zabezpečení sítě jako pracovní uzly ARO.

Další informace o OpenShift 4,5 najdete v [poznámkách k verzi OpenShift 4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Další kroky
Další informace o odchozích přenosech a o tom, co Azure Red Hat OpenShift podporuje pro výstup, najdete v dokumentaci k [zásadám podpory](support-policies-v4.md) .
