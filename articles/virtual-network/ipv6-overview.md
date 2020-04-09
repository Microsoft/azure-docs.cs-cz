---
title: Přehled IPv6 pro virtuální síť Azure
titlesuffix: Azure Virtual Network
description: Popis IPv6 koncových bodů IPv6 a datových cest ve virtuální síti Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 312e9db594983f85372285bdff415a2d5dc76ed3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984006"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Co je IPv6 pro virtuální síť Azure?

IPv6 pro virtuální síť Azure (VNet) umožňuje hostovat aplikace v Azure pomocí připojení IPv6 a IPv4 v rámci virtuální sítě i do a z Internetu. Vzhledem k vyčerpání veřejných IPv4 adres jsou nové sítě pro mobilitu a Internet věcí (IoT) často postaveny na IPv6. Dokonce i dlouho zavedené ISP a mobilní sítě jsou transformovány na IPv6. Služby pouze pro IPv4 se mohou ocitnout ve skutečné nevýhodě na stávajících i rozvíjejících se trzích. Připojení IPv4/IPv6 se dvěma zásobníky umožňuje službám hostovaným v Azure procházet tuto technologickou mezeru pomocí globálně dostupných dvouskládaných služeb, které se snadno připojují jak k existujícím IPv4, tak k těmto novým zařízením a sítím IPv6.

Původní připojení IPv6 azure usnadňuje poskytování připojení k internetu s dvěma stacky (IPv4/IPv6) pro aplikace hostované v Azure. Umožňuje jednoduché nasazení virtuálních počítače s připojením IPv6 s vyrovnáváním zatížení pro příchozí i odchozí inicialovaná připojení. Tato funkce je stále k dispozici a další informace jsou k dispozici [zde](../load-balancer/load-balancer-ipv6-overview.md).
Virtuální síť IPv6 pro Azure je mnohem plnější – umožňuje nasazení úplných architektur řešení IPv6 v Azure.


Následující diagram znázorňuje jednoduché nasazení duálního zásobníku (IPv4/IPv6) v Azure:

![Diagram nasazení sítě IPv6](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Výhody

Výhody sítě IPv6 pro virtuální síť Azure:

- Pomáhá rozšířit dosah vašich aplikací hostovaných v Azure na rostoucí trhy s mobilními zařízeními a internetem věcí.
- Dva skládané virtuální počítače IPv4/IPv6 poskytují maximální flexibilitu nasazení služby. Jedna instance služby se může připojit k internetovým klientům podporujícím protokol IPv4 i IPv6.
- Staví na dlouho zavedeném a stabilním připojení IPv6 virtuálního počítače Azure k Internetu.
- Ve výchozím nastavení je zabezpečené, protože připojení IPv6 k Internetu je vytvořeno pouze v případě, že o něj explicitně požádáte ve vašem nasazení.

## <a name="capabilities"></a>Možnosti

IPv6 pro virtuální síť Azure obsahuje následující funkce:

- Zákazníci Azure můžou definovat svůj vlastní adresní prostor virtuální sítě IPv6 tak, aby vyhovoval potřebám svých aplikací, zákazníků, nebo se bez problémů integrovat do svého místního IP prostoru.
- Virtuální sítě se dvěma zásobníky (IPv4 a IPv6) s podsítěmi se dvěma zásobníky umožňují aplikacím připojit se k prostředkům IPv4 i IPv6 ve své virtuální síti nebo v Internetu.
    > [!IMPORTANT]
    > Podsítě pro IPv6 musí mít přesně /64 velikost.  To zajistí budoucí kompatibilitu, pokud se rozhodnete povolit směrování podsítě do místní sítě, protože některé směrovače mohou přijímat pouze trasy IPv6 /64.  
- Chraňte své prostředky pomocí pravidel Protokolu IPv6 pro skupiny zabezpečení sítě.
    - A ochrany distribuovaného odmítnutí služby (DDoS) platformy Azure jsou rozšířeny na veřejné IP adresy
- Přizpůsobte směrování provozu IPv6 ve vaší virtuální síti pomocí uživatelem definovaných tras, zejména při využití síťových virtuálních zařízení k rozšíření aplikace.
- Virtuální počítače s Linuxem a Windows můžou používat IPv6 pro virtuální síť Azure
- [Standardní veřejná podpora ipv6 pro](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) vytváření odolných a škálovatelných aplikací, které zahrnují:
    - Volitelné IPv6 sonda stavu k určení, které instance back-endfondu jsou stav a proto můžete přijímat nové toky.
    - Volitelná odchozí pravidla, která poskytují úplnou deklarativní kontrolu nad odchozím připojením, aby tuto schopnost škálovala a vylaďovala podle vašich konkrétních potřeb.
    - Volitelné více front-end konfigurace, které umožňují jeden nástroj pro vyrovnávání zatížení používat více IPv6 veřejné IP adresy- stejný front-end protokol a port lze znovu použít přes front-end adresy.
    - Volitelné porty IPv6 lze znovu použít v back-endových instancích pomocí funkce *Plovoucí IP* pravidla vyrovnávání zatížení 
    - Poznámka: Vyrovnávání zatížení neprovádí žádný překlad protokolu (bez NAT64). 
- Standardní interní podpora [IPv6 pro vytváření](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) odolných vícevrstvých aplikací v rámci virtuálních aplikací Azure.   
- Základní veřejná podpora ipv6 pro kompatibilitu se staršími nasazeními
- [Rezervované veřejné IP adresy IPv6 a rozsahy adres](ipv6-public-ip-address-prefix.md) poskytují stabilní a předvídatelné adresy IPv6, které usnadňují seznam povolených aplikací hostovaných v Azure pro vaši společnost a vaše zákazníky.
- Veřejná IP adresa na úrovni instance poskytuje připojení k Internetu IPv6 přímo jednotlivým virtuálním počítačům.
- [Přidejte protokol IPv6 do stávajících nasazení pouze s Protokolem IPv4](ipv6-add-to-existing-vnet-powershell.md)– tato funkce umožňuje snadno přidat připojení IPv6 ke stávajícím nasazením pouze s Protokolem IPv4 bez nutnosti znovu vytvářet nasazení.  Provoz sítě IPv4 není během tohoto procesu ovlivněn, takže v závislosti na vaší aplikaci a operačním systému můžete přidat IPv6 i do živých služeb.    
- Ušetřujte internetovým klientům bezproblémový přístup k vaší aplikaci se dvěma zásobníky pomocí jejich zvoleného protokolu pomocí azure dns podpory pro záznamy IPv6 (AAAA). 
- Vytvářejte aplikace se dvěma zásobníky, které se automaticky škálují podle vašeho zatížení pomocí škálovacích sad virtuálních strojů pomocí iPv6.
- [Partnerský vztah virtuální sítě (VNET)](virtual-network-peering-overview.md) – v rámci regionálního i globálního partnerského vztahu – umožňuje zdánlivě připojit virtuální sítě s duálním zásobníkem – koncové body IPv4 i IPv6 na virtuálních počítačích v partnerských sítích budou moci vzájemně komunikovat. Můžete dokonce peer dual stack s IPv4 pouze VNETs, jak jste přechod u vašich nasazení do dvou zásobníku. 
- Odstraňování problémů a diagnostika protokolu IPv6 jsou k dispozici s metrikami/výstrahami nástroje pro vyrovnávání zatížení a funkcemi sledování sítě, jako je zachytávání paketů, protokoly toku nsg, odstraňování problémů s připojením a monitorování připojení.   

## <a name="scope"></a>Rozsah
IPv6 pro virtuální síť Azure je základní sada funkcí, která zákazníkům umožňuje hostovat aplikace s dvěma stacky (IPv4+IPv6) v Azure.  Máme v úmyslu přidat podporu IPv6 k dalším síťovým funkcím Azure v průběhu času a nakonec nabídnout verze dvou zásobníků služeb Azure PaaS, ale mezitím všechny služby Azure PaaS lze přistupovat prostřednictvím koncových bodů IPv4 na virtuálních počítačích s dvěma zásobníky.   

## <a name="limitations"></a>Omezení
Aktuální verze Virtuální sítě IPv6 pro Azure má následující omezení:
- IPv6 pro virtuální síť Azure je k dispozici ve všech globálních oblastech Azure Commercial pomocí všech metod nasazení.  Nasazení v cloudu vlády USA je dočasně omezeno na šablonu ARM (JSON), rozhraní příkazového řádku (CLI) a powershell.  Podpora IPv6 na cloudovém portálu vlády USA bude brzy k dispozici.  
- Brány ExpressRoute lze použít pro přenosy pouze s Protokolem IPv4 ve virtuální síti s povoleným protokolem IPv6.  Podpora provozu IPv6 je na našem plánu.   
- Brány VPN nelze použít ve virtuální síti s povolenou technologií IPv6, přímo nebo v partnerě "UseRemoteGateway".
- Platforma Azure (AKS atd.) nepodporuje komunikaci IPv6 pro kontejnery.  

## <a name="pricing"></a>Ceny

Prostředky IPv6 Azure a šířka pásma se účtují stejným tempem jako IPv4. Za IPv6 se neúčtují žádné další nebo jiné poplatky. Můžete najít podrobnosti o cenách pro [veřejné IP adresy](https://azure.microsoft.com/pricing/details/ip-addresses/), [šířku pásma sítě](https://azure.microsoft.com/pricing/details/bandwidth/)nebo [Balancer zatížení](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [nasadit aplikaci iPv6 dual stack pomocí Azure PowerShellu](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Zjistěte, jak [nasadit aplikaci iPv6 dual stack pomocí rozhraní příkazového příkazového příkazu Azure](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Zjistěte, jak [nasadit aplikaci IPv6 dual stack pomocí šablon Správce prostředků (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
