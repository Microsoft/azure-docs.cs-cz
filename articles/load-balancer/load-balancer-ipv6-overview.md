---
title: Přehled IPv6 – Nástroje pro vyrovnávání zatížení Azure
description: S tímto studijním programem můžete začít s podporou IPv6 pro Azure Load Balancer a virtuální počítače s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure balancer, dual stack, public ip, nativní ipv6, mobilní, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: d2966f32fcf7f8be2a93d1639f0a63f49768c306
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981847"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Přehled IPv6 pro Azure Load Balancer


>[!NOTE] 
>Tento obsah byl nahrazen [IPv6 pro Azure VNet Přehled](https://docs.microsoft.com/azure/virtual-network/ipv6-overview). Azure doporučuje, aby nová nasazení IPv6 používala nové funkce IPv6 pro virtuální sítě Azure.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o standardním nástroje pro vyrovnávání zatížení naleznete v [tématu Standardní přehled nástroje pro vyrovnávání zatížení](load-balancer-standard-overview.md).

Základní internetové vyvažovače skladové položky lze nasadit s adresou IPv6. Kromě připojení IPv4 to umožňuje následující funkce:

* Nativní komplexní připojení IPv6 mezi veřejnými internetovými klienty a virtuálními počítači (VM) azure prostřednictvím nástroje pro vyrovnávání zatížení.
* Nativní end-to-end IPv6 odchozí připojení mezi virtuálními zařízeními a veřejnými internetovými klienty s podporou IPv6.

Následující obrázek znázorňuje funkci IPv6 pro Nástroje pro vyrovnávání zatížení Azure.

![Azure Load Balancer s IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po nasazení může internetový klient s podporou IPv4 nebo IPv6 komunikovat s veřejnými adresami IPv4 nebo IPv6 (nebo názvy hostitelů) zařízení pro vyrovnávání zatížení Azure pro Internet. Balancer na zatížení směruje pakety IPv6 na privátní adresy IPv6 virtuálních připojení pomocí překladu síťových adres (NAT). Internetový klient IPv6 nemůže komunikovat přímo s adresou IPv6 virtuálních operátorů.

## <a name="features"></a>Funkce

Nativní podpora IPv6 pro virtuální počítače nasazené prostřednictvím Azure Resource Manager poskytuje:

1. Služby IPv6 s vyrovnáváním zatížení pro klienty IPv6 na Internetu
2. Nativní koncové body IPv6 a IPv4 na virtuálních počítačích ("duální skládaný")
3. Příchozí a odchozí nativní připojení IPv6
4. Podporované protokoly, jako jsou TCP, UDP a HTTP(S) umožňují celou řadu architektur služeb

## <a name="benefits"></a>Výhody

Tato funkce umožňuje následující klíčové výhody:

* Splňovat vládní předpisy, které vyžadují, aby nové aplikace byly přístupné klientům pouze iPv6
* Umožněte vývojářům mobilních zařízení a internetu věcí (IOT) používat virtuální počítače Azure s dvěma skládanými (IPv4+IPv6) k řešení rostoucího mobilního & trzích IOT

## <a name="details-and-limitations"></a>Podrobnosti a omezení

Podrobnosti

* Služba Azure DNS obsahuje záznamy názvů IPv4 A i IPv6 AAAA a reaguje oběma záznamy pro vyrovnávání zatížení. Klient zvolí, se kterou adresou (IPv4 nebo IPv6) bude komunikovat.
* Když virtuální hod iniciuje připojení k veřejnému zařízení připojenému k Internetu IPv6, zdrojová adresa IPv6 virtuálního aplikace je síťová adresa přeložená (NAT) na veřejnou adresu IPv6 nástroj pro vyrovnávání zatížení.
* Virtuální počítače s operačním systémem Linux musí být nakonfigurovány tak, aby přijímaly ip adresu IPv6 přes DHCP. Mnoho imitek Linuxu v Galerii Azure už je nakonfigurovaných tak, aby podporovalo IPv6 bez úprav. Další informace [najdete v tématu Konfigurace DHCPv6 pro virtuální počítače s Linuxem](load-balancer-ipv6-for-linux.md)
* Pokud se rozhodnete použít sondu stavu s vyrovnáváním zatížení, vytvořte sondu IPv4 a použijte ji s koncovými body IPv4 i IPv6. Pokud služba na vašem virtuálním počítači přejde dolů, koncové body IPv4 i IPv6 jsou vyřazeny z rotace.

Omezení

* Na webu Azure Portal nelze přidat pravidla vyrovnávání zatížení IPv6. Pravidla lze vytvořit pouze prostřednictvím šablony, CLI, PowerShell.
* Je možné, že existující virtuální hody nelze upgradovat tak, aby používaly adresy IPv6. Je nutné nasadit nové virtuální chod.
* Jednu adresu IPv6 lze přiřadit k jednomu síťovému rozhraní v každém virtuálním modulu.
* Veřejné adresy IPv6 nelze přiřadit k virtuálnímu virtuálnímu soudu. Lze je přiřadit pouze k vyvyprávě zatížení.
* Zpětné vyhledávání DNS pro veřejné adresy IPv6 nelze konfigurovat.
* Virtuální počítače s adresami IPv6 nemohou být členy cloudové služby Azure. Můžou být připojeni k virtuální síti Azure (VNet) a vzájemně spolu komunikovat přes jejich adresy IPv4.
* Privátní adresy IPv6 lze nasadit na jednotlivé virtuální počítači ve skupině prostředků, ale nelze je nasadit do skupiny prostředků pomocí škálovacích sad.
* Virtuální počítače Azure se nemůžou připojit přes IPv6 k jiným virtuálním počítačům, jiným službám Azure nebo místním zařízením. Můžou komunikovat jenom s Azure balancer přes IPv6. Mohou však komunikovat s těmito jinými prostředky pomocí IPv4.
* Ochrana skupiny zabezpečení sítě (NSG) pro IPv4 je podporována v nasazeních s dvěma zásobníky (IPv4+ IPv6). NsG se nevztahují na koncové body IPv6.
* Koncový bod IPv6 na virtuálním počítači není vystavenpřímo k Internetu. Je za vyrovnáváním zatížení. Pouze porty zadané v pravidlech vyrovnávání zatížení jsou přístupné přes IPv6.
* Změna parametru IdleTimeout pro IPv6 **není aktuálně podporována**. Výchozí hodnota je čtyři minuty.
* Změna parametru loadDistributionMethod pro IPv6 **není aktuálně podporována**.
* Vyhrazené IP adresy IPv6 (kde ipallocationmethod = statické) nejsou **aktuálně podporovány**.
* NAT64 (překlad IPv6 na IPv4) není podporován.
* Připojení sekundární síťové rozhraní, která odkazuje na podsíť IPv6 do fondu back-end, není **aktuálně podporováno**.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak nasadit vyvyčovávač zatížení pomocí iPv6.

* [Dostupnost Protokolu IPv6 podle oblasti](https://go.microsoft.com/fwlink/?linkid=828357)
* [Nasazení vykladače zatížení pomocí iPv6 pomocí šablony](load-balancer-ipv6-internet-template.md)
* [Nasazení vykladače zatížení pomocí IPv6 pomocí Azure PowerShellu](load-balancer-ipv6-internet-ps.md)
* [Nasazení vykladače zatížení pomocí IPv6 pomocí azure cli](load-balancer-ipv6-internet-cli.md)
