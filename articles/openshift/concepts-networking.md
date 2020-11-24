---
title: Koncepty – diagram sítě pro Azure Red Hat na OpenShift 4
description: Diagram sítě a přehled pro sítě Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 06/22/2020
ms.openlocfilehash: 3417b59d0be9e285f8793ef598abb7f98bda7549
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95527985"
---
# <a name="networking-in-azure-red-hat-on-openshift-4"></a>Sítě v Azure Red Hat na OpenShift 4

Tato příručka obsahuje přehled o sítích v clusterech Azure Red Hat na OpenShift 4 společně s diagramem a seznamem důležitých koncových bodů.

Další podrobnosti o základních konceptech OpenShift sítě najdete v [dokumentaci k síti Azure Red Hat OpenShift 4](https://docs.openshift.com/aro/4/networking/understanding-networking.html).

## <a name="networking-concepts-in-azure-red-hat-openshift"></a>Koncepty sítě v Azure Red Hat OpenShift

![Diagram sítě Azure Red Hat OpenShift 4](./media/concepts-networking/aro4-networking-diagram.png)

Když nasadíte Azure Red Hat na OpenShift 4, celý cluster je obsažený ve virtuální síti. V této virtuální síti jsou hlavní uzly a pracovní uzly každé živé v vlastní podsíti. Každá podsíť používá veřejný a interní nástroj pro vyrovnávání zatížení.

## <a name="explanation-of-endpoints"></a>Vysvětlení koncových bodů

Následující seznam obsahuje důležité koncové body v clusteru Azure Red Hat OpenShift.

* **ARO – jiných pracovních prostorů**
    * Toto je koncový bod privátního propojení Azure, který používají technici pro spolehlivost Microsoftu a Red Hat k tomu, aby se mohl cluster spravovat.
* **ARO – interní – 9,1**
    * Tento koncový bod vyrovnává provoz se serverem rozhraní API. Pro tento nástroj pro vyrovnávání zatížení jsou hlavní uzly ve fondu back-endu.
* **ARO – veřejná – 9,1**
    * Pokud je rozhraní API veřejné, směruje tento koncový bod a vyrovnává provoz na server rozhraní API. Tento koncový bod přiřadí veřejnou odchozí IP adresu, aby mohly hlavní servery získat přístup k Azure Resource Manager a nahlásit se zpátky na stav clusteru.
* **ARO – interní**
    * Tento koncový bod vyvažuje za interní provoz služby. Pro tento nástroj pro vyrovnávání zatížení jsou pracovní uzly ve fondu back-endu.
    * Tento nástroj pro vyrovnávání zatížení není ve výchozím nastavení vytvořen. Tento nástroj pro vyrovnávání zatížení se vytvoří po vytvoření služby typu Vyrovnávání zatížení se správnými poznámkami. Příklad: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **Zásady sítě (příchozí)**
    * Podporováno v rámci OpenShift SDN
    * Ve výchozím nastavení povoleno, vynucení prováděné zákazníky
    * V1 NetworkPolicy kompatibilní, ale typy "odchozí a IPBlock" ještě nejsou podporované.
    * **společnosti**
    * Tento koncový bod vyrovnává provoz se serverem rozhraní API. Pro tento nástroj pro vyrovnávání zatížení jsou hlavní uzly ve fondu back-endu.
  * **ARO – interní – 9,1**
    * Tento koncový bod se používá pro všechny veřejné přenosy. Když vytváříte aplikaci a trasu, jedná se o cestu k provozu příchozího přenosu dat.
    * Tento nástroj pro vyrovnávání zatížení zahrnuje také odchozí připojení k Internetu z libovolného uzlu běžícího v pracovních uzlech prostřednictvím odchozích pravidel Azure Load Balancer.
        * Aktuálně odchozí pravidla se nedají konfigurovat. Každý uzel přiděluje 1 024 portů TCP.
        * DisableOutboundSnat není v pravidlech pro odchodu nakonfigurovaná, takže lusky by mohly mít jako výstupní IP adresu jakoukoli veřejnou IP adresu nakonfigurovanou v této ALB.
        * V důsledku obou předchozích bodů je jediným způsobem, jak přidat dočasné porty SNAT, přidávat do společnosti ARO veřejné služby typu Vyrovnávání zatížení.
* **Zásady sítě (odchozí data)**
    * Zásady odchozího přenosu dat se podporují pomocí funkce odchozího firewallu v OpenShift.
    * Pouze jeden pro každý obor názvů nebo projekt.
    * V oboru názvů default se nepodporují zásady odchozího přenosu dat.
    * Pravidla zásad odchozího přenosu se vyhodnocují v pořadí (první až poslední).
    * **ARO – odchozí – PIP**
        * Tento koncový bod slouží jako veřejná IP adresa (PIP) pro pracovní uzly.
        * Tento koncový bod umožňuje službám přidat konkrétní IP adresu z clusteru Azure Red Hat OpenShift do seznamu povolených serverů.
* **ARO – Node-NSG**
    * Když vystavíte službu, rozhraní API vytvoří v této skupině zabezpečení sítě pravidlo, které umožní tok provozu a dosáhnou uzlů.
    * Ve výchozím nastavení je tato skupina zabezpečení sítě povolena u všech odchozích přenosů. V současné době je možné odchozí provoz omezit jenom na rovinu ovládacího prvku Azure Red Hat OpenShift.
* **ARO – controlplane-NSG**
    * Tento koncový bod povoluje provoz pouze do zadání přes port 6443 pro hlavní uzly.
* **Azure Container Registry**
    * Toto je registr kontejneru, který poskytuje a interně používá společnost Microsoft.
        * V tomto registru jsou k dispozici image platformy hostitele a součásti clusteru. Například kontejnery monitorování nebo protokolování.
        * Neurčeno pro použití zákazníky Azure Red Hat OpenShift.  
        * Jen pro čtení.
        * K tomuto registru dojde prostřednictvím koncového bodu služby (interní připojení mezi službami Azure).
        * Tento interní registr není ve výchozím nastavení k dispozici mimo cluster.
* **Privátní propojení**
    * Umožňuje síťové připojení z roviny správy do clusteru pro správu clusteru.
    * Technici pro spolehlivost Microsoftu a Red Hat vám pomůžou se správou clusteru.

## <a name="networking-basics-in-openshift"></a>Základy sítě v OpenShift

OpenShift software definované sítě (SDN) se používá ke konfiguraci překryté sítě pomocí Open virtuálního přepínače (OVS), implementace technologii OpenFlow založeného na specifikaci CNI (Container Network Interface). SDN podporuje různé moduly plug-in a síťové zásady jsou moduly plug-in používané v Azure Red Hat na OpenShift 4. Veškerá síťová komunikace je spravovaná pomocí SDN, takže ve vašich virtuálních sítích nejsou potřeba žádné další trasy, aby bylo možné využít komunikaci pod technologií pod.

## <a name="azure-red-hat-openshift-networking-specifics"></a>Specifikovaná síť Azure Red Hat OpenShift

Následující funkce jsou specifické pro Azure Red Hat OpenShift:
* Je podporována vaše vlastní virtuální síť.
* CIDRs síť pod a služba se dají konfigurovat.
* Uzly a hlavní servery jsou v různých podsítích.
* Podsítě uzlů a virtuálních sítí virtuálních sítí by měly být minimálně/27.
* Hodnota pod směrováním CIDR by měla být minimálně/18 (síť pod Nesměrovatelné IP adresy a používá se jenom uvnitř OpenShift SDN).
* Každému uzlu je přiděleno/23 podsítě (512 IP adres) pro své lusky. Tato hodnota se nedá změnit.
* Nemůžete připojit objekt pod k více sítím.
* Nemůžete nakonfigurovat statickou statickou IP adresu. (Jedná se o funkci OpenShift. Informace najdete v tématu [Konfigurace odchozích IP adres](https://docs.openshift.com/aro/4/networking/openshift_sdn/assigning-egress-ips.html).

## <a name="network-settings"></a>Nastavení sítě

V Azure Red Hat OpenShift 4 jsou k dispozici následující nastavení sítě:

* **Viditelnost rozhraní API** – při spuštění [příkazu AZ ARO Create](tutorial-create-cluster.md#create-the-cluster)se nastaví viditelnost rozhraní API.
    * Veřejné sítě mají přístup k serveru rozhraní API Public.
    * "Privátní" Server rozhraní API přiřadil privátní IP adresu z podsítě hlavních serverů, k dispozici pouze pomocí propojených sítí (s partnerskými virtuálními sítěmi, jinými podsítěmi v clusteru). V zastoupení zákazníka se vytvoří privátní zóna DNS.
* **Viditelnost** příchozího přenosu – při spuštění [příkazu AZ ARO Create](tutorial-create-cluster.md#create-the-cluster)se nastaví viditelnost rozhraní API.
    * "Veřejné" trasy budou ve výchozím nastavení veřejné Standard Load Balancer (může být změněno).
    * "Soukromé" trasy budou výchozím nastavením interního nástroje pro vyrovnávání zatížení (Tento stav může být změněn).

## <a name="network-security-groups"></a>Skupiny zabezpečení sítě
Skupiny zabezpečení sítě se vytvoří ve skupině prostředků uzlu, která je uzamčená. Skupiny zabezpečení sítě se přiřazují přímo k podsítím, nikoli na síťových kartách uzlu. Skupiny zabezpečení sítě jsou neměnné, což znamená, že nemáte oprávnění je měnit. 

S veřejně viditelným serverem API ale nemůžete vytvářet skupiny zabezpečení sítě a přiřazovat je k síťovým kartám.

## <a name="domain-forwarding"></a>Předávání domény
Azure Red Hat OpenShift používá CoreDNS. Je možné nakonfigurovat předávání domény (Další informace najdete v dokumentaci o [používání předávání DNS](https://docs.openshift.com/aro/4/networking/dns-operator.html#nw-dns-forward_dns-operator) ).

V současné době nemůžete do svých virtuálních sítí přenášet vlastní DNS.


Další informace o odchozích přenosech a o tom, co Azure Red Hat OpenShift podporuje pro výstup, najdete v dokumentaci k [zásadám podpory](support-policies-v4.md) .
