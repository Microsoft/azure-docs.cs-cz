---
title: Přehled protokolu IPv6 pro Azure Load Balancer | Dokumentace Microsoftu
description: Principy podpory protokolu IPv6 pro Azure Load Balancer a virtuálních počítačů s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejné IP adresy, nativní protokol ipv6, mobilní zařízení, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: kumud
ms.openlocfilehash: 379407c8d9e5c6a5a0476fd88ea2dda6e7994e0f
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739324"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Přehled protokolu IPv6 pro Azure Load Balancer


>[!NOTE] 
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard přehled](load-balancer-standard-overview.md).

Internetové nástroje pro vyrovnávání zatížení je možné nasadit s adresou typu IPv6. Kromě připojení pomocí protokolu IPv4 to umožňuje následující funkce:

* Nativní začátku do konce protokolu IPv6 připojení mezi veřejné internetové klienty a Azure Virtual Machines (VM) pomocí nástroje pro vyrovnávání zatížení.
* Nativní začátku do konce protokolu IPv6 odchozí připojení mezi virtuálními počítači a veřejné klienty s podporou Internetu IPv6.

Na následujícím obrázku je znázorněný funkcí protokolu IPv6 pro Azure Load Balancer.

![Azure Load Balancer s IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po nasazení IPv4 nebo IPv6 povolené Internet klient může komunikovat s veřejné adresy IPv4 nebo IPv6 (nebo názvy hostitelů) nástroje pro vyrovnávání zatížení přístupem k Internetu v Azure. Nástroje pro vyrovnávání zatížení směruje pakety IPv6 na privátní adresy IPv6 virtuální počítače, použijte překlad síťových adres (NAT). Internet s protokolem IPv6 klient nemůže komunikovat přímo s IPv6 adres virtuálních počítačů.

## <a name="features"></a>Funkce

Nativní podpora protokolu IPv6 pro virtuální počítače nasazené prostřednictvím Azure Resource Manageru poskytuje:

1. Služby IPv6 s vyrovnáváním zatížení IPv6 klientů na Internetu
2. Nativní protokol IPv6 a IPv4 koncových bodů na virtuálních počítačích ("duální skládaný")
3. Příchozí a odchozí iniciované nativních připojení IPv6
4. Podporované protokoly, například TCP, UDP a HTTP (S) povolit široké spektrum architektur služeb

## <a name="benefits"></a>Výhody

Tato funkce umožňuje následující klíčové výhody:

* Splnění vládním předpisům vyžadující, že nové aplikace být dostupný klientům výhradně s protokolem IPv6
* Povolit mobilní zařízení a Internet věcí (IOT) vývojářům používat k adresování rostoucí mobilní zařízení a trhy IOT skládaný dvou virtuálních počítačích Azure (IPv4 + IPv6)

## <a name="details-and-limitations"></a>Podrobnosti a omezení

Podrobnosti

* Služba Azure DNS obsahuje název záznamy IPv4 A a IPv6 AAAA a odpoví oba záznamy pro nástroj pro vyrovnávání zatížení. Klient vybere která adresa (IPv4 nebo IPv6) pro komunikaci s.
* Když virtuální počítač inicializuje připojení k veřejné zařízení připojeného k Internetu IPv6, Virtuálního počítače zdrojovou adresu IPv6 je síťová adresa přeložit (NAT) na veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení.
* Virtuální počítače s operačním systémem Linux musí být nakonfigurovaný pro příjem IP adresu protokolu IPv6 adresu prostřednictvím DHCP. Mnoho Linuxové Image v galerii Azure jsou již nakonfigurována pro podporu IPv6 beze změn. Další informace najdete v tématu [konfigurace protokolu DHCPv6 pro virtuální počítače s Linuxem](load-balancer-ipv6-for-linux.md)
* Pokud budete chtít použijte sondu stavu pomocí služby load balancer, vytvořte sondu protokolu IPv4 a jeho použití s koncovými body pro protokol IPv4 i IPv6. Pokud služba na váš virtuální počítač přestane fungovat, koncové body protokolu IPv4 i IPv6 pocházejí ze smyčky.

Omezení

* Pravidla Vyrovnávání zatížení IPv6 nelze přidat na portálu Azure portal. Pravidla lze vytvořit pouze pomocí šablony, rozhraní příkazového řádku, prostředí PowerShell.
* Nemůže upgradovat stávající virtuální počítače použít adresu IPv6. Je nutné nasadit nové virtuální počítače.
* Jedna adresa IPv6 je možné přiřadit jedno síťové rozhraní do každého virtuálního počítače.
* Veřejné IPv6 adresy nelze přiřadit k virtuálnímu počítači. Je možné přiřadit pouze nástroji pro vyrovnávání zatížení.
* Nelze konfigurovat zpětného vyhledávání DNS pro veřejné IPv6 adresy.
* Virtuální počítače s adresami IPv6 nemohou být členy cloudové služby Azure. Se dá připojit ke službě Azure Virtual Network (VNet) a vzájemně komunikovat přes jejich adresy IPv4.
* Privátní IPv6 adresy je možné nasadit s jednotlivými virtuálními počítači ve skupině prostředků, ale nemůže být nasazena do skupiny prostředků prostřednictvím Škálovací sady.
* Virtuální počítače Azure se nemůže připojit přes protokol IPv6 pro ostatní virtuální počítače, další služby Azure nebo na místních zařízeních. S nástrojem Azure load balancer můžete pouze komunikaci přes protokol IPv6. Však může komunikovat s těchto dalších zdrojů pomocí protokolu IPv4.
* Ochrana skupiny zabezpečení sítě (NSG) pro protokol IPv4 je podporovaná v nasazení s duální sadou protokolů (IPv4 + IPv6). Skupiny Nsg se nevztahují na koncové body IPv6.
* Koncový bod IPv6 adresy na virtuálním počítači nezveřejňují přímo k Internetu. Je za nástroj pro vyrovnávání zatížení. Pouze porty určených v pravidlech nástroje pro vyrovnávání zatížení jsou přístupné přes protokol IPv6.
* Změna parametru IdleTimeout pro protokol IPv6 je **aktuálně nepodporuje**. Výchozí hodnota je 4 minut.
* Změna parametru loadDistributionMethod pro protokol IPv6 je **aktuálně nepodporuje**.
* Vyhrazené IP adresy protokolu IPv6 (kde IPAllocationMethod = statické) jsou **aktuálně nepodporuje**.
* NAT64 (překlad IPv6 na IPv4) se nepodporuje.

## <a name="next-steps"></a>Další postup

Zjistěte, jak nasadit nástroj pro vyrovnávání zatížení s protokolem IPv6.

* [Dostupnost podle oblasti IPv6](https://go.microsoft.com/fwlink/?linkid=828357)
* [Nasadit nástroj pro vyrovnávání zatížení s IPv6 pomocí šablony](load-balancer-ipv6-internet-template.md)
* [Nasadit nástroj pro vyrovnávání zatížení s IPv6 pomocí Azure Powershellu](load-balancer-ipv6-internet-ps.md)
* [Nasadit nástroj pro vyrovnávání zatížení s IPv6 pomocí rozhraní příkazového řádku Azure](load-balancer-ipv6-internet-cli.md)
