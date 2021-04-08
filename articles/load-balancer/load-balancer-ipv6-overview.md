---
title: Přehled protokolu IPv6-Azure Load Balancer
description: Pomocí této cesty výukového programu začněte s podporou protokolu IPv6 pro Azure Load Balancer a virtuální počítače s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, duální zásobník, veřejná IP adresa, nativní IPv6, mobilní zařízení, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94700675"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Přehled protokolu IPv6 pro Azure Load Balancer


>[!NOTE] 
>Tento obsah byl nahrazen [protokolem IPv6 pro virtuální síť Azure](../virtual-network/ipv6-overview.md). Azure doporučuje nová nasazení IPv6 používat nové funkce protokolu IPv6 pro virtuální sítě Azure.

>[!NOTE]
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o Standard Load Balancer najdete v tématu [Standard Load Balancer Overview](./load-balancer-overview.md).

Základní služba Vyrovnávání zatížení na základě Internetu se dá nasadit s adresou IPv6. Kromě připojení protokolem IPv4 Tato funkce umožňuje následující možnosti:

* Nativní připojení k síti IPv6 mezi veřejnými internetovými klienty a Azure Virtual Machines (virtuální počítače) prostřednictvím nástroje pro vyrovnávání zatížení.
* Nativní koncová konektivita protokolu IPv6 mezi virtuálními počítači a veřejnými klienty podporujícími internetovou IPv6.

Následující obrázek znázorňuje funkce protokolu IPv6 pro Azure Load Balancer.

![Azure Load Balancer s protokolem IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po nasazení může internetový klient s podporou protokolu IPv4 nebo IPv6 komunikovat s veřejnými adresami IPv4 nebo IPv6 (nebo názvy hostitelů) internetového Load Balancer Azure. Nástroj pro vyrovnávání zatížení směruje pakety IPv6 na privátní adresy IPv6 virtuálních počítačů pomocí překladu adres (NAT). Internetový klient IPv6 nemůže komunikovat přímo s adresou IPv6 virtuálních počítačů.

## <a name="features&quot;></a>Funkce

Nativní podpora protokolu IPv6 pro virtuální počítače nasazené prostřednictvím Azure Resource Manager poskytuje:

1. Služby IPv6 s vyrovnáváním zatížení pro klienty IPv6 na internetu
2. Nativní koncové body IPv6 a IPv4 na virtuálních počítačích (&quot;duální stackd")
3. Příchozí a odchozí připojení iniciovaná nativní IPv6
4. Podporované protokoly, jako jsou TCP, UDP a HTTP (S), umožňují celou škálu architektur služeb.

## <a name="benefits"></a>Výhody

Tato funkce umožňuje využít tyto klíčové výhody:

* Splnění právních předpisů pro státní správu vyžadující, aby nové aplikace byly dostupné jenom pro klienty s protokolem IPv6
* Umožněte vývojářům v mobilních sítích a Internetu věcí (IOT) používat pro Azure Virtual Machines Dual-Stack (IPv4 + IPv6), aby se vyřešily rostoucí mobilní trhy & IOT.

## <a name="details-and-limitations"></a>Podrobnosti a omezení

Podrobnosti

* Služba Azure DNS obsahuje záznamy IPv4 a i IPv6 AAAA a reaguje na oba záznamy pro nástroj pro vyrovnávání zatížení. Klient zvolí, s jakou adresou (IPv4 nebo IPv6) komunikuje.
* Když virtuální počítač iniciuje připojení k veřejnému zařízení připojenému k Internetu, je zdrojová adresa IPv6 přeložená síťovou adresou (NAT) na veřejnou IPv6 adresu nástroje pro vyrovnávání zatížení.
* Virtuální počítače s operačním systémem Linux musí být nakonfigurované tak, aby přijímaly IP adresu IPv6 přes protokol DHCP. Řada imagí pro Linux v galerii Azure je už nakonfigurovaná tak, aby podporovala protokol IPv6 beze změn. Další informace najdete v tématu [Konfigurace protokolu DHCPv6 pro virtuální počítače se systémem Linux](load-balancer-ipv6-for-linux.md) .
* Pokud se rozhodnete použít sondu stavu s vaším nástrojem pro vyrovnávání zatížení, vytvořte sondu IPv4 a použijte ji s koncovými body IPv4 i IPv6. Pokud služba na vašem VIRTUÁLNÍm počítači přestane být vypnutá, koncové body IPv4 i IPv6 se vyberou mimo rotaci.

Omezení

* Do Azure Portal nemůžete přidat pravidla vyrovnávání zatížení protokolu IPv6. Pravidla je možné vytvořit jenom prostřednictvím šablony, rozhraní příkazového řádku PowerShellu.
* Existující virtuální počítače nemůžete upgradovat na použití IPv6 adres. Je nutné nasadit nové virtuální počítače.
* Jedna adresa IPv6 se dá přiřadit k jednomu síťovému rozhraní v každém virtuálním počítači.
* Veřejné adresy IPv6 nelze přiřadit k virtuálnímu počítači. Dají se přiřadit jenom nástroji pro vyrovnávání zatížení.
* Nemůžete nakonfigurovat reverzní vyhledávání DNS pro vaše veřejné adresy IPv6.
* Virtuální počítače s adresami IPv6 nemůžou být členy cloudové služby Azure. Můžou být připojené ke službě Azure Virtual Network (VNet) a vzájemně komunikovat prostřednictvím jejich IPv4 adres.
* Privátní adresy IPv6 se dají nasadit na jednotlivé virtuální počítače ve skupině prostředků, ale nedají se nasadit do skupiny prostředků prostřednictvím sad škálování.
* Virtuální počítače Azure se nemohou připojit přes IPv6 k jiným virtuálním počítačům, jiným službám Azure nebo místním zařízením. Můžou komunikovat jenom s nástrojem pro vyrovnávání zatížení Azure přes IPv6. Mohou ale komunikovat s těmito dalšími prostředky pomocí protokolu IPv4.
* Ochrana skupiny zabezpečení sítě (NSG) pro protokol IPv4 je podporovaná v nasazeních s duálním zásobníkem (IPv4 + IPv6). Skupin zabezpečení sítě se nevztahuje na koncové body IPv6.
* Koncový bod IPv6 na virtuálním počítači se nezveřejňuje přímo na internetu. Je za nástrojem pro vyrovnávání zatížení. Přes IPv6 jsou přístupné jenom porty zadané v pravidlech nástroje pro vyrovnávání zatížení.
* Změna parametru IdleTimeout pro protokol IPv6 se v tuto **chvíli** nepodporuje. Výchozí hodnota je čtyři minuty.
* Změna parametru loadDistributionMethod pro protokol IPv6 se v tuto **chvíli** nepodporuje.
* Protokol IPv6 pro základní Load Balancer je uzamčený na **dynamickou** SKU.  Protokol IPv6 pro Standard Load Balancer je pevně nastaven na **statickou** SKU.
* NAT64 (převod IPv6 na IPv4) není podporován.
* Připojení sekundárního síťového adaptéru, který odkazuje na podsíť IPv6 do fondu back-end, se v tuto **chvíli** nepodporuje.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nasadit nástroj pro vyrovnávání zatížení s protokolem IPv6.

* [Dostupnost protokolu IPv6 podle oblasti](https://go.microsoft.com/fwlink/?linkid=828357)
* [Nasazení nástroje pro vyrovnávání zatížení s protokolem IPv6 pomocí šablony](load-balancer-ipv6-internet-template.md)
* [Nasazení nástroje pro vyrovnávání zatížení s protokolem IPv6 pomocí Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Nasazení nástroje pro vyrovnávání zatížení s protokolem IPv6 pomocí Azure CLI](load-balancer-ipv6-internet-cli.md)