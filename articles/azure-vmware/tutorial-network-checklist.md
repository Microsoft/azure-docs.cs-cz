---
title: Kurz – kontrolní seznam pro plánování sítě
description: Seznamte se s požadavky na požadavky na síť a podrobnostmi o připojení k síti a síťových portech pro řešení Azure VMware.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5538f9c5d6543ca312835f4ef6437e413dea231b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576673"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Kontrolní seznam pro plánování sítě pro řešení Azure VMware 

Řešení Azure VMware nabízí prostředí privátního cloudu VMware, které je přístupné pro uživatele a aplikace z místních prostředí a prostředí a prostředků založených na platformě Azure. Připojení se doručuje prostřednictvím síťových služeb, jako je Azure ExpressRoute a připojení VPN, a vyžaduje pro povolení služeb určité konkrétní rozsahy síťových adres a porty brány firewall. Tento článek poskytuje informace, které potřebujete ke správné konfiguraci sítě pro práci s řešením Azure VMware.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Požadavky na virtuální síť a okruh ExpressRoute
> * Požadavky na směrování a podsíť
> * Požadované síťové porty pro komunikaci se službami
> * Hlediska DHCP a DNS v řešení Azure VMware

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Požadavky na virtuální síť a okruh ExpressRoute
Když ve svém předplatném vytvoříte připojení k virtuální síti, okruh ExpressRoute se naváže prostřednictvím partnerského vztahu, použije autorizační klíč a ID partnerského vztahu, které požadujete v Azure Portal. Partnerský vztah je privátní a jedno připojení mezi Vaším privátním cloudem a virtuální sítí.

> [!NOTE] 
> Okruh ExpressRoute není součástí nasazení privátního cloudu. Místní okruh ExpressRoute překračuje rozsah tohoto dokumentu. Pokud vyžadujete místní připojení k privátnímu cloudu, můžete použít některý ze stávajících okruhů ExpressRoute nebo si ho koupit v Azure Portal.

Při nasazení privátního cloudu obdržíte IP adresy pro vCenter a správce NSX-T. Pro přístup k těmto rozhraním pro správu budete muset ve virtuální síti vašeho předplatného vytvořit další prostředky. Můžete najít postupy pro vytváření těchto prostředků a vytváření [privátních partnerských vztahů ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) v kurzech.

Logické sítě privátního cloudu jsou dodávány s předem zřízeným NSX-T. Pro vás předběžně zřídí bránu 0 a bránu 1. Můžete vytvořit segment a připojit ho k existující bráně 1-1 nebo ji připojit k nové bráně, kterou definujete. Logické síťové komponenty NSX-T poskytují konektivitu mezi úlohami v severozápadním a v západním rozsahu a umožňují připojení k Internetu a službám Azure v oblasti USA – jih.

## <a name="routing-and-subnet-considerations"></a>Posouzení směrování a podsítí
Privátní cloud služby AVS je připojený k vaší službě Azure Virtual Network pomocí připojení Azure ExpressRoute. Tato velká šířka pásma, připojení s nízkou latencí umožňuje přístup ke službám běžícím v předplatném Azure z vašeho privátního cloudového prostředí. Směrování je založené na protokolu BGP (Routing) Border Gateway Protocol (BGP), automaticky zřízené a povolené ve výchozím nastavení pro každé nasazení privátního cloudu. 

Privátní cloudy služby AVS vyžadují minimálně `/22` blok síťových adres CIDR pro podsítě, které jsou uvedené níže. Tato síť doplňuje vaše místní sítě. Blok adres by se neměl překrývat s bloky adres používanými v jiných virtuálních sítích v rámci vašeho předplatného a místních sítí. V rámci tohoto bloku adres se automaticky zřídí Správa, zřizování a vMotion sítě.

Příklad `/22` bloku síťových adres CIDR:  `10.10.0.0/22`

Podsítě:

| Využití sítě             | Podsíť | Příklad          |
| ------------------------- | ------ | ---------------- |
| Správa privátního cloudu  | `/26`  | `10.10.0.0/26`   |
| vMotion síť           | `/25`  | `10.10.1.128/25` |
| Úlohy virtuálních počítačů              | `/24`  | `10.10.2.0/24`   |
| Partnerský vztah ExpressRoute      | `/29`  | `10.10.3.8/29`   |


## <a name="required-network-ports"></a>Požadované síťové porty

| Zdroj | Cíl | Protokol | Port | Description  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| Server DNS privátního cloudu | Místní server DNS | UDP | 53 | Klient DNS – požadavky na přeposílání z PC vCenter pro všechny místní dotazy DNS (podívejte se na část DNS níže) |  
| Místní server DNS   | Server DNS privátního cloudu | UDP | 53 | Klient DNS – požadavky na přeposílání z místních služeb na servery DNS privátního cloudu (podívejte se na část DNS níže) |  
| Místní síť  | Privátní cloud vCenter Server  | TCP (HTTP)  | 80 | vCenter Server vyžaduje port 80 pro přímá připojení HTTP.Port 80 přesměrovává požadavky na port HTTPS 443. Toto přesměrování pomáhá při použití  `http://server`   namísto  `https://server` .  <br><br>WS-Management (vyžaduje se taky otevření portu 443) <br><br>Pokud používáte vlastní databázi Microsoft SQL a nikoli sadu vCenter Server databáze SQL Server 2008, služba SQL Reporting Services používá port 80. Při instalaci vCenter Server instalační program vyzve, abyste změnili port HTTP pro vCenter Server. Změňte vCenter Server port HTTP na vlastní hodnotu, aby se zajistila úspěšná instalace.Služba Microsoft Internetová informační služba (IIS) také používá port 80. Podívejte se na téma konflikt mezi vCenter Server a IIS pro port 80. |  
| Síť pro správu privátního cloudu | Místní služby Active Directory  | TCP  | 389 | Tento port musí být otevřen v místním počítači a všech vzdálených instancích vCenter Server. Tento port je číslo portu LDAP pro adresářové služby pro skupinu vCenter Server. VCenter Server systém musí vytvořit vazbu na port 389, i když se nepřipojujete k této instanci vCenter Server do skupiny propojených režimů. Pokud je na tomto portu spuštěna jiná služba, může být vhodnější ji odebrat nebo změnit její port na jiný port. Službu LDAP můžete spustit na jakémkoli portu od 1025 do 65535.Pokud tato instance slouží jako služba Microsoft Windows Active Directory, změňte číslo portu z 389 na dostupný port z 1025 až 65535. Tento port je volitelný – pro konfiguraci místní služby AD jako zdroje identity v privátním cloudu vCenter. |  
| Místní síť  | Privátní cloud vCenter Server  | TCP (HTTPS)  | 443 | Tento port umožňuje přístup k serveru vCenter z místní sítě.Výchozí port, který vCenter Server systém používá k naslouchání připojením od klienta vSphere. Pokud chcete vCenter Server systému povolit příjem dat z klienta vSphere, otevřete v bráně firewall port 443. Systém vCenter Server také používá port 443 k monitorování přenosu dat z klientů sady SDK.Tento port se používá taky pro následující služby: WS-Management (vyžaduje se taky otevření portu 80). vSphere klientský přístup ke Správci aktualizací vSphere. Připojení klienta správy sítě od jiných výrobců k vCenter Server. Klienti správy sítě třetích stran mají přístup k hostitelům. |  
| Webový prohlížeč  | Správce hybridního cloudu  | TCP (HTTPS) | 9443 | Rozhraní pro správu virtuálních zařízení hybridního cloudového správce pro konfiguraci systému hybridního cloudového správce. |
| Síť pro správu  | Správce hybridního cloudu | SSH | 22 | Správce přístupu SSH k hybridnímu Cloud Manageru. |
| HCM | Cloudová brána | TCP (HTTPS) | 8123 | Odeslání pokynů pro replikaci na základě hostitelů do brány hybridního cloudu. |
| HCM | Cloudová brána | HTTP TCP (HTTPS) | 9443 | Pošlete pokyny pro správu do místní hybridní cloudové brány pomocí REST API. |
| Cloudová brána | L2C | TCP (HTTPS) | 443 | Poslat pokyny pro správu ze služby Cloud Gateway do L2C, když L2C používá stejnou cestu jako brána hybridního cloudu. |
| Cloudová brána | Hostitelé ESXi | TCP | 80 902 | Nasazení správy a OVF. |
| Cloudová brána (místní)| Cloudová brána (vzdálená) | UDP | 4500 | Vyžadováno pro protokol IPSEC<br>   Protokol IKEv2 (Internet Key Exchange) pro zapouzdření úloh pro obousměrný tunel. Překládání adres (NAT-T) je podporované i pro procházení síťových adres. |
| Cloudová brána (místní) | Cloudová brána (vzdálená)  | UDP | 500 | Vyžadováno pro protokol IPSEC<br> Protokol ISAKMP (Internet Key Exchange) pro obousměrný tunel. |
| Místní síť vCenter | Síť pro správu privátního cloudu | TCP | 8000 |  vMotion virtuálních počítačů z místního serveru vCenter do privátního cloudu vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Hlediska překladu DHCP a DNS
Aplikace a úlohy, které běží v prostředí privátního cloudu, vyžadují překlad IP adres a služby DHCP pro vyhledávání a přiřazování IP adres. K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS. Virtuální počítač můžete nakonfigurovat tak, aby poskytoval tyto služby v prostředí vašeho privátního cloudu.  

Použijte službu DHCP integrovanou do NSX nebo použijte místní server DHCP v privátním cloudu namísto směrování provozu DHCP přes síť WAN zpátky do místního prostředí.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s důležitými informacemi a požadavky pro nasazení privátního cloudu řešení Azure VMware. 


Jakmile budete mít správné sítě, přejděte k dalšímu kurzu, kde můžete vytvořit privátní cloud pro řešení Azure VMware.

> [!div class="nextstepaction"]
> [Vytvoření privátního cloudu řešení Azure VMware](tutorial-create-private-cloud.md)
