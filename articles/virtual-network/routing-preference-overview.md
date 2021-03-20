---
title: Předvolby směrování v Azure
description: Přečtěte si, jak si můžete vybrat, jak vaše trasy přenosů mezi Azure a internetem a prioritou směrování.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 56fd9898065dd55b9a56547a775f7f2aa4a56156
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693726"
---
# <a name="what-is-routing-preference"></a>Co je předvolba směrování?

Předvolby směrování Azure vám umožní zvolit způsob přenosu tras mezi Azure a internetem. Provoz můžete směrovat buď přes síť Microsoft, nebo prostřednictvím sítě poskytovatele internetových služeb (veřejný Internet). Tyto možnosti se také označují jako *Směrování studených brambor* a *horké směrování brambor* . Cena za odchozí přenosy dat se liší v závislosti na výběru směrování. Při vytváření veřejné IP adresy můžete zvolit možnost směrování. Veřejná IP adresa může být přidružená k prostředkům, jako jsou virtuální počítače, sady škálování virtuálních počítačů, internetový nástroj pro vyrovnávání zatížení atd. Můžete také nastavit předvolby směrování pro prostředky služby Azure Storage, jako jsou objekty blob, soubory, webové služby a Azure datalake. Ve výchozím nastavení jsou přenosy směrovány prostřednictvím globální sítě Microsoft pro všechny služby Azure.

## <a name="routing-via-microsoft-global-network"></a>Směrování prostřednictvím globální sítě Microsoft

Při směrování provozu přes *globální síť Microsoftu* se provoz doručuje přes jednu z největších sítí na celém světě, která pokrývá více než 160 000 mil na vláknu s více než 165 body přítomnosti (pop). Síť je dobře zřízena s několika redundantními vlákny a zajišťuje tak mimořádně vysokou spolehlivost a dostupnost. Technologie provozu je spravovaná softwarem, který je definovaný pro síťový adaptér WAN, který zajišťuje výběr cest s nízkou latencí pro provoz a nabízí špičkový výkon sítě.

![Směrování prostřednictvím globální sítě Microsoft](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Příchozí přenos dat:** Globální oznámení o příchozím přenosu BGP zajišťuje, že příchozí provoz vstoupí do sítě Microsoftu co nejblíže uživateli. Pokud například uživatel z Singapuru přistupuje k prostředkům Azure hostovaným v Chicagu, USA, pak se provoz do globální sítě Microsoft v rámci Singapuru Edge zahájí a v síti se zaznamená do služby hostované v Chicagu.

**Odchozí přenos dat:** Odchozí přenosy se řídí stejným principem. Provoz cestuje v rámci své cesty v globální síti Microsoft a ukončuje se nejblíže k uživateli. Například pokud je provoz z Azure Chicago určen uživateli ze Singapuru, provoz se v síti Microsoftu přenáší z Chicago do Singapuru a ukončí síť Microsoftu v rámci nástroje pro Singapur Edge.

Příchozí i výstupní provoz zachová velkou cestu k globální síti Microsoftu. Tento postup se označuje také jako *Směrování studených brambor*.


## <a name="routing-over-public-internet-isp-network"></a>Směrování přes veřejný Internet (síť poskytovatele internetových služeb)

Nová možnost směrování *sítě Internet* minimalizuje služební cestu k globální síti Microsoft a používá tranzitní síť poskytovatele internetových služeb ke směrování provozu. Tato možnost směrování s optimálním výkonem nabízí výkon sítě, který je porovnatelný z jiných poskytovatelů cloudu.

![Směrování přes veřejný Internet](media/routing-preference-overview/route-via-isp-network.png)

**Příchozí přenos dat:** Cesta příchozího provozu používá *aktivní směrování brambor* , což znamená, že provoz vstoupí do sítě Microsoftu, která je nejblíže oblasti hostované služby. Například pokud uživatel z Singapuru přistupuje k prostředkům Azure hostovaným v Chicagu, provoz se prochází přes veřejný Internet a přejde do globální sítě Microsoft v Chicagu.

**Odchozí přenos dat:** Odchozí přenosy se řídí stejným principem. Provoz ukončí síť Microsoftu ve stejné oblasti, ve které je služba hostovaná. Pokud je například provoz z vaší služby v Azure v Chicagu pro uživatele určený od Singapuru, pak provoz ukončí síť Microsoftu v Chicagu a v rámci veřejného Internetu se přenese do uživatele v Singapuru.

## <a name="supported-services"></a>Podporované služby

Veřejná IP adresa s volbou předvolby směrování "globální síť Microsoft" může být přidružená k jakékoli službě Azure. Veřejné IP adresy s volbou předvolby směrování si **ale můžete** přidružit k následujícím prostředkům Azure:

* Virtuální počítač
* Škálovací sada virtuálních počítačů
* Azure Kubernetes Service (AKS)
* Internetový nástroj pro vyrovnávání zatížení
* Application Gateway
* Azure Firewall

Pro úložiště primární koncové body vždy používají **globální síť Microsoft**. Můžete povolit sekundární koncové body s **internetem** podle vašeho výběru pro směrování provozu. Podporované služby úložiště:

* Objekty blob
* Soubory
* Web
* Azure DataLake

## <a name="pricing"></a>Ceny
Cenový rozdíl mezi oběma možnostmi se odráží v ceně za přenos dat z Internetu. Směrování prostřednictvím služby **Microsoft Global Network** Data Transfer je stejné jako aktuální cena za internetovou cenu. Nejnovější informace o cenách najdete na [stránce s cenami na šířku pásma Azure](https://azure.microsoft.com/pricing/details/bandwidth/) .

## <a name="limitations"></a>Omezení


* Preference směrování je kompatibilní jenom se standardní SKU zóny s redundantními verzemi veřejné IP adresy. Základní SKU veřejné IP adresy se nepodporuje.
* Preference směrování aktuálně podporuje jenom veřejné IP adresy IPv4. Veřejné IP adresy protokolu IPv6 nejsou podporovány.


## <a name="next-steps"></a>Další kroky

* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurace předvolby směrování pro virtuální počítač pomocí Azure CLI](configure-routing-preference-virtual-machine-cli.md)
