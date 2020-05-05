---
title: 'Kurz: kontrolní seznam sítě'
description: Požadavky na požadavky na síť a podrobnosti o připojení k síti a síťových portech
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 42bd579a455c7efe3b8f6e4d4154e687726adb1d
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740147"
---
# <a name="networking-checklist-for-azure-vmware-solution-avs"></a>Kontrolní seznam sítí pro řešení Azure VMware (AVS)

Řešení Azure VMware nabízí prostředí privátního cloudu VMware, které je přístupné pro uživatele a aplikace z místních prostředí a prostředí a prostředků založených na platformě Azure. Připojení se doručuje prostřednictvím síťových služeb, jako je Azure ExpressRoute a připojení VPN, a pro povolení služeb bude vyžadovat určité rozsahy síťových adres a porty brány firewall. Tento článek poskytuje informace, které potřebujete ke správné konfiguraci sítě pro práci s prostředím AVS.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Požadavky na připojení k síti
> * Protokol DHCP v AVS

## <a name="network-connectivity"></a>Připojení k síti

Privátní cloud služby AVS je připojený k vaší službě Azure Virtual Network pomocí připojení Azure ExpressRoute. Tato velká šířka pásma, připojení s nízkou latencí umožňuje přístup ke službám běžícím v předplatném Azure z vašeho privátního cloudového prostředí.

Privátní cloudy služby AVS vyžadují minimálně blok `/22` síťových adres CIDR pro podsítě, které jsou uvedené níže. Tato síť doplňuje vaše místní sítě. Aby bylo možné se připojit k místním prostředím a virtuálním sítím, musí se jednat o blok síťových adres, který se nepřekrývá.

Příklad `/22` bloku síťových adres CIDR:`10.10.0.0/22`

Podsítě:

| Využití sítě             | Podsíť | Příklad        |
| ------------------------- | ------ | -------------- |
| Správa privátního cloudu            | `/24`    | `10.10.0.0/24`   |
| vMotion síť       | `/24`    | `10.10.1.0/24`   |
| Úlohy virtuálních počítačů | `/24`   | `10.10.2.0/24`   |
| Partnerský vztah ExpressRoute | `/24`    | `10.10.3.8/30`   |

### <a name="network-ports-required-to-communicate-with-the-service"></a>Síťové porty vyžadované ke komunikaci se službou

Zdroj|Cíl|Protocol (Protokol) |Port |Popis  |Poznámka
---|-----|:-----:|:-----:|-----|-----
Server DNS privátního cloudu  |Místní server DNS  |UDP |53|Klient DNS – požadavky na přeposílání z PC vCenter pro všechny místní dotazy DNS (podívejte se na část DNS níže) |
Místní server DNS  |Server DNS privátního cloudu  |UDP |53|Klient DNS – požadavky na přeposílání z místních služeb na servery DNS privátního cloudu (podívejte se na část DNS níže)
Místní síť  |Privátní cloud vCenter Server  |TCP (HTTP)  |80|vCenter Server vyžaduje port 80 pro přímá připojení HTTP.Port 80 přesměrovává požadavky na port HTTPS 443. Toto přesměrování pomáhá při použití `http://server` namísto `https://server`.  <br><br>WS-Management (vyžaduje se taky otevření portu 443) <br><br>Pokud používáte vlastní databázi Microsoft SQL a nikoli sadu vCenter Server databáze SQL Server 2008, služba SQL Reporting Services používá port 80. Při instalaci vCenter Server instalační program vyzve, abyste změnili port HTTP pro vCenter Server. Změňte vCenter Server port HTTP na vlastní hodnotu, aby se zajistila úspěšná instalace.Služba Microsoft Internetová informační služba (IIS) také používá port 80. Podívejte se na téma konflikt mezi vCenter Server a IIS pro port 80.
Síť pro správu privátního cloudu |Místní služby Active Directory  |TCP  |389|Tento port musí být otevřen v místním počítači a všech vzdálených instancích vCenter Server. Tento port je číslo portu LDAP pro adresářové služby pro skupinu vCenter Server. VCenter Server systém musí vytvořit vazbu na port 389, i když se nepřipojujete k této instanci vCenter Server do skupiny propojených režimů. Pokud je na tomto portu spuštěna jiná služba, může být vhodnější ji odebrat nebo změnit její port na jiný port. Službu LDAP můžete spustit na jakémkoli portu od 1025 do 65535.Pokud tato instance slouží jako služba Microsoft Windows Active Directory, změňte číslo portu z 389 na dostupný port z 1025 až 65535. Tento port je volitelný – pro konfiguraci místní služby AD jako zdroje identity v privátním cloudu vCenter.
Místní síť  |Privátní cloud vCenter Server  |TCP (HTTPS)  |443|Tento port umožňuje přístup k serveru vCenter z místní sítě.Výchozí port, který vCenter Server systém používá k naslouchání připojením od klienta vSphere. Pokud chcete vCenter Server systému povolit příjem dat z klienta vSphere, otevřete v bráně firewall port 443. Systém vCenter Server také používá port 443 k monitorování přenosu dat z klientů sady SDK.Tento port se používá taky pro následující služby: WS-Management (vyžaduje se taky otevření portu 80). vSphere klientský přístup ke Správci aktualizací vSphere. Připojení klienta správy sítě od jiných výrobců k vCenter Server. Klienti správy sítě třetích stran mají přístup k hostitelům. 
Webový prohlížeč  | Správce hybridního cloudu  | TCP (https) | 9443 | Rozhraní pro správu virtuálních zařízení hybridního cloudového správce pro konfiguraci systému hybridního cloudového správce.
Síť pro správu  | Správce hybridního cloudu |protokoly |22| Správce přístupu SSH k hybridnímu Cloud Manageru.
HCM |   Cloudová brána|TCP (HTTPS) |8123| Odeslání pokynů pro replikaci na základě hostitelů do brány hybridního cloudu.
HCM |   Cloudová brána  |    HTTP TCP (HTTPS) |    9443  |  Pošlete pokyny pro správu do místní hybridní cloudové brány pomocí REST API.
Cloudová brána|      L2C |    TCP (HTTP)  |   443 |   Poslat pokyny pro správu ze služby Cloud Gateway do L2C, když L2C používá stejnou cestu jako brána hybridního cloudu.
Cloudová brána |     Hostitelé ESXi |     TCP |    80 902  |   Nasazení správy a OVF
Cloudová brána (místní)|     Cloudová brána (vzdálená)|     UDP  |   4500 | Vyžadováno pro protokol IPSEC<br>   Protokol IKEv2 (Internet Key Exchange) pro zapouzdření úloh pro obousměrný tunel. Překládání adres (NAT-T) je podporované i pro procházení síťových adres.
Cloudová brána (místní)  |   Cloudová brána (vzdálená)  |   UDP  |   500   | Vyžadováno pro protokol IPSEC<br> Protokol ISAKMP (Internet Key Exchange) pro obousměrný tunel.
Místní síť vCenter|      Síť pro správu privátního cloudu|      TCP  |    8000    |  vMotion virtuálních počítačů z místního serveru vCenter do privátního cloudu vCenter   |     

## <a name="dhcp"></a>DHCP

Aplikace a úlohy běžící v prostředí privátního cloudu vyžadují překlad IP adres a služby DHCP pro vyhledání a přiřazení IP adresy. K poskytování těchto služeb se vyžaduje správná infrastruktura DHCP a DNS. Virtuální počítač můžete nakonfigurovat tak, aby poskytoval tyto služby v prostředí vašeho privátního cloudu.  
Doporučuje se použít službu DHCP integrovanou do NSX nebo místní server DHCP v privátním cloudu místo směrování provozu DHCP přes síť WAN zpátky do místního prostředí.

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky na připojení k síti
> * Protokol DHCP v AVS

## <a name="next-steps"></a>Další kroky

Jakmile budete mít správné sítě, přejděte k dalšímu kurzu a vytvořte privátní cloud služby AVS.

> [!div class="nextstepaction"]
> [Kurz: Vytvoření privátního cloudu služby AVS](tutorial-create-private-cloud.md)
