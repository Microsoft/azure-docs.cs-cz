---
title: Přehled protokolu IPv6 pro vyrovnávání zatížení Azure | Microsoft Docs
description: Principy podporu IPv6 pro vyrovnávání zatížení Azure a virtuálních počítačů s vyrovnáváním zatížení.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
keywords: protokol IPv6, nástroje pro vyrovnávání zatížení azure, duálním zásobníkem, veřejnou IP adresu, nativní protokol ipv6, mobilní, iot
ms.assetid: 6a1d583f-a305-40fd-a94b-fa42e1943bbb
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 9622ad4922aa98efe093e7f809a490a8797eb1fd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189660"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Přehled protokolu IPv6 pro vyrovnávání zatížení Azure


>[!NOTE] 
>Azure Load Balancer podporuje dva různé typy: Basic a Standard. Tento článek popisuje Load Balancer úrovně Basic. Další informace o standardní nástroje pro vyrovnávání zatížení najdete v tématu [nástroj pro vyrovnávání zatížení přehled](load-balancer-standard-overview.md).

Internetové služby Vyrovnávání zatížení můžete nasadit pomocí adresy IPv6. Kromě připojení pomocí protokolu IPv4 to umožňuje následující možnosti:

* Nativní začátku do konce IPv6 připojení mezi klienty veřejného Internetu a virtuální počítače (VM) Azure prostřednictvím nástroje pro vyrovnávání zatížení.
* Nativní klient server IPv6 odchozí připojení mezi virtuální počítače a veřejné klienty používající Internetu IPv6.

Následující obrázek znázorňuje IPv6 funkce nástroje pro vyrovnávání zatížení Azure.

![Nástroje pro vyrovnávání zatížení Azure s IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Po nasazení klientem IPv4 nebo IPv6 povolené Internetu může komunikovat s veřejné adresy IPv4 nebo IPv6 (nebo názvy hostitelů) Azure internetové služby Vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení směrování paketů IPv6 privátní IPv6 adresy virtuálních počítačů pomocí překlad síťových adres (NAT). IPv6 Internet klient nemůže komunikovat přímo s adresou IPv6 virtuálních počítačů.

## <a name="features"></a>Funkce

Poskytuje nativní podporu IPv6 pro virtuální počítače nasazené prostřednictvím Správce Azure Resource Manager:

1. Vyrovnávání zatížení služby IPv6 pro IPv6 klientů na Internetu
2. Nativní protokol IPv6 a IPv4 koncových bodů na virtuálních počítačích ("dva skládaný")
3. Příchozí a odchozí spouštěná nativní IPv6 připojení
4. Podporované protokoly, jako je například TCP, UDP a HTTP (S) povolte celou řadu architektury služby

## <a name="benefits"></a>Výhody

Tato funkce umožňuje následující klíčové výhody:

* Splňují předpisy government vyžadující, že nové aplikace být dostupný klientům pouze protokol IPv6
* Povolit mobilní a Internet věcí (IOT) vývojářům používat duální skládaný virtuální počítače Azure (IPv4 + IPv6) Chcete-li vyřešit rostoucí mobile & trhů IOT

## <a name="details-and-limitations"></a>Podrobnosti a omezení

Podrobnosti

* Služba Azure DNS obsahuje záznamy, název IPv4 A i IPv6 AAAA a odpoví oba záznamy pro vyrovnávání zatížení. Klient zvolí které adresy (IPv4 nebo IPv6) ke komunikaci s.
* Když virtuální počítač zahájí připojení do veřejných zařízení připojená k Internetu IPv6, Virtuálního počítače zdrojovou adresu IPv6 je síťová adresa přeložit (NAT) na veřejnou adresu IPv6 služby Vyrovnávání zatížení.
* Virtuální počítače s operačním systémem Linux musí být nakonfigurované pro příjem IPv6 IP adresu prostřednictvím DHCP. Mnoho Linux obrázků v galerii Azure jsou již nakonfigurována na podporovat protokol IPv6 bez úprav. Další informace najdete v tématu [DHCPv6 konfiguraci pro virtuální počítače s Linuxem](load-balancer-ipv6-for-linux.md)
* Pokud chcete použít test stavu se nástroj pro vyrovnávání zatížení, vytvořit test paměti IPv4 a jeho použití s IPv4 a IPv6 koncové body. Pokud službu na virtuální počítač přestane fungovat, koncové body IPv4 i IPv6 se vyjímají z otočení.

Omezení

* Nelze přidat pravidla Vyrovnávání zatížení IPv6 na portálu Azure. Pravidla lze vytvořit pouze pomocí šablony, rozhraní příkazového řádku, prostředí PowerShell.
* Nemusí upgradovat stávající virtuální počítače použít adresu IPv6. Je nutné nasadit nové virtuální počítače.
* Jedna adresa IPv6 lze přiřadit k jedno síťové rozhraní v jednotlivých virtuálních počítačů.
* Veřejné adresy IPv6 nelze přiřadit k virtuálnímu počítači. Můžete lze přiřadit pouze k nástroji pro vyrovnávání zatížení.
* Nelze konfigurovat zpětné vyhledávání DNS pro vaše veřejné adresy IPv6.
* Virtuální počítače s adresami IPv6 nemohou být členy cloudové služby Azure. Tyto může být připojen k virtuální síti Azure (VNet) a vzájemně komunikovat prostřednictvím jejich adresy IPv4.
* Privátní IPv6 adresy můžou být nasazené na jednotlivé virtuální počítače ve skupině prostředků, ale nelze nasadit do skupiny prostředků prostřednictvím sady škálování.
* Virtuální počítače Azure se nemůže připojit přes protokol IPv6 pro ostatní virtuální počítače, ostatní služby Azure nebo místní zařízení. Nástroje pro vyrovnávání zatížení Azure mohou pouze komunikovat přes protokol IPv6. Však může komunikovat s tyto prostředky, které pomocí protokolu IPv4.
* Skupina zabezpečení sítě (NSG) ochrany pro protokol IPv4 je podporována v nasazeních duální sada protokolů (IPv4 + IPv6). Skupiny Nsg se nevztahují ke koncovým bodům protokol IPv6.
* Koncový bod IPv6 adresy ve virtuálním počítači není přístup přímo k Internetu. Je za službou Vyrovnávání zatížení. Pouze porty, které jsou určené v pravidla nástroje pro vyrovnávání zatížení jsou přístupné přes protokol IPv6.
* Změna parametr IdleTimeout pro protokol IPv6 je **aktuálně není podporována**. Výchozí hodnota je 4 minut.
* Změna parametr loadDistributionMethod pro protokol IPv6 je **aktuálně není podporována**.
* Vyhrazené IP adresy IPv6 (kde IPAllocationMethod = statické) jsou **aktuálně není podporována**.

## <a name="next-steps"></a>Další postup

Informace o nasazení Vyrovnávání zatížení s IPv6.

* [Dostupnost podle oblasti IPv6](https://go.microsoft.com/fwlink/?linkid=828357)
* [Nasazení Vyrovnávání zatížení s IPv6 pomocí šablony](load-balancer-ipv6-internet-template.md)
* [Nasazení Vyrovnávání zatížení s IPv6 pomocí Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Nasazení Vyrovnávání zatížení s IPv6 pomocí rozhraní příkazového řádku Azure](load-balancer-ipv6-internet-cli.md)
