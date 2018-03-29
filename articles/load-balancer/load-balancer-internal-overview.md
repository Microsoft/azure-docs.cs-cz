---
title: Přehled služby Azure interní nástroj pro vyrovnávání zatížení | Microsoft Docs
description: Interní nástroj jak funguje ve službě Azure a scénáře pro konfiguraci vnitřních koncových bodů.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Přehled nástroje pro vyrovnávání zatížení Azure interní



Azure vyrovnávání interní zatížení (ILB) pouze přesměruje přenosy na prostředky, které jsou uvnitř cloudové služby nebo které využívají sítě VPN pro přístup k infrastruktury Azure. V tomto ohledu ILB se liší od Vyrovnávání zatížení internetové brány. Infrastrukturu Azure omezuje přístup na Vyrovnávání zatížení virtuální IP (VIP) adresy cloudové služby nebo k virtuální síti. Virtuální adresa IP adresy a virtuální sítě se zveřejňují nikdy přímo pro koncový bod sítě internet. Interní-obchodní aplikace spustit v Azure a ke kterým se přistupuje z Azure nebo z místních prostředků.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Proč může být nutné k nástroji pro vyrovnávání zatížení interní

Interní nástroj pro vyrovnávání zatížení poskytuje vyrovnávání zátěže mezi virtuální počítače (VM), které jsou umístěny uvnitř cloudovou službu nebo virtuální síť s místní obor. Informace o virtuálních sítích v místní rozsahu, najdete v tématu [regionálních virtuálních sítí](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) blog in the Azure. Existující virtuální sítě, které jsou nakonfigurovány pro skupiny vztahů nelze použít ILB.

ILB umožňuje následující typy Vyrovnávání zatížení:

* V rámci cloudové služby: vyrovnávání z virtuálních počítačů na sadu virtuálních počítačů, které se nacházejí v cloudové službě stejné zatížení. Toto <a href="#figure1">příklad</a>.
* V rámci virtuální sítě: Vyrovnávání zatížení z virtuálních počítačů v virtuální sítě na sadu virtuálních počítačů, které se nacházejí v rámci stejné cloudové služby ve virtuální síti. Toto <a href="#figure2">příklad</a>.
* Pro virtuální síť mezi různými místy: vyrovnávání z místního počítače na sadu virtuálních počítačů, které se nacházejí v rámci stejné cloudové služby ve virtuální síti zatížení. Toto <a href="#figure3">příklad</a>.
* U vícevrstvých aplikací: pro internetový vícevrstvé aplikace, kde vrstvy back-end nejsou internetového Vyrovnávání zatížení. Back-end úrovně vyžadují z internetového vrstvy Vyrovnávání zatížení provozu.
* Pro-obchodní aplikace: pro-obchodní aplikace, které jsou hostované v Azure bez další zátěže vyrovnávání hardwaru nebo softwaru Vyrovnávání zatížení. Tento scénář zahrnuje na místní servery, které jsou v sadě počítačů, jejichž provoz s vyrovnáváním zatížení.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Vyrovnávání zatížení pro internetový vícevrstvé aplikace

Webová vrstva obsahuje internetových koncových bodů pro internetové klienty a je součástí skupiny s vyrovnáváním zatížení. ILB distribuuje příchozí provoz z webových klientů pro port TCP 443 (HTTPS) na webové servery.

Databázové servery jsou za ILB koncový bod, který webové servery používají pro úložiště. Koncový bod ILB je databáze Vyrovnávání zatížení koncového bodu služby. Přenosy jsou vyrovnávání zatížení mezi servery databáze v sadě ILB.

Následující obrázek ukazuje interní Vyrovnávání zatížení pro internetový vícevrstvé aplikace v rámci stejné cloudové služby.

<a name="figure1"></a>
![Internetový vícevrstvé aplikace](./media/load-balancer-internal-overview/IC736321.png)

Další možností je k dispozici pro vícevrstvé aplikace. Nástroje pro vyrovnávání zatížení se nasadí do jiné cloudové služby od toho, který využívá službu pro ILB.

Cloudové služby, které používají stejné virtuální síti mají přístup k koncovým bodem ILB. Následující obrázek znázorňuje front-end webové servery, které jsou v rámci různých cloudové služby z databáze back-end. Servery front-end použijte koncovým bodem ILB v rámci stejné virtuální síti jako back-end.

<a name="figure2"></a>
![Servery front-end v rámci různých cloudové služby](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Vyrovnávání zatížení pro intranet-obchodní aplikace

Přenosy od klientů v místní síti jsou vyrovnávání zatížení mezi několik serverů obchodní, které používají připojení VPN k síti Azure.

Klientský počítač mají přístup ke adresu IP ze služby Azure VPN pomocí sítě VPN point-to-site. Obchodní aplikace může být hostovaný za koncovým bodem ILB.

<a name="figure3"></a>
![Obchodní aplikace hostované za koncovým bodem ILB](./media/load-balancer-internal-overview/IC744148.png)

Další možností pro-obchodní aplikace je VPN site-to-site k virtuální síti, kde je koncovým bodem ILB nakonfigurované. Místní síťový provoz se směruje na koncovým bodem ILB.

<a name="figure4"></a>
![Místní síťový provoz směrovat na koncovým bodem ILB](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Omezení

Interní nástroj pro vyrovnávání zatížení konfigurace nepodporují překládat pomocí SNAT. V tomto článku překládat pomocí SNAT odkazuje na scénáře, které zahrnují vydávají port překlad síťových adres zdroje. Virtuální počítač ve fondu nástroje pro vyrovnávání zatížení musí být doručena front-end IP adresu služby Vyrovnávání zatížení příslušné interní. Když toku Vyrovnávání zatížení sítě k virtuálnímu počítači, který byl odeslán toku dojde k selhání připojení. Tyto scénáře nejsou podporovány pro ILB. Místo toho je nutné použít nástroj pro vyrovnávání zatížení stylu proxy serveru.

## <a name="next-steps"></a>Další postup

* [Podporu správce prostředků Azure pro nástroj pro vyrovnávání zatížení Azure](load-balancer-arm.md)
* [Začínáme s konfigurace vyrovnávání zatížení internetového](load-balancer-get-started-internet-arm-ps.md)
* [Začínáme s konfigurace vyrovnávání zatížení interní](load-balancer-get-started-ilb-arm-ps.md)
* [Konfigurace distribučního režimu nástroje pro vyrovnávání zatížení](load-balancer-distribution-mode.md)
* [Konfigurace nastavení časového limitu nečinnosti protokolu TCP pro nástroj pro vyrovnávání zatížení](load-balancer-tcp-idle-timeout.md)
