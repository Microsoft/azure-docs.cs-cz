---
title: Co je funkce automatické registrace privátních zón Azure DNS
description: Přehled funkce automatické registrace privátních zón Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961210"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Jaká je funkce automatické registrace privátních zón Azure DNS

Funkce automatické registrace privátních zón Azure DNS odvádí bolest ze správy záznamů DNS pro virtuální počítače nasazené ve virtuální síti. Když [propojíte virtuální síť](./private-dns-virtual-network-links.md) se soukromou zónou DNS a povolíte automatickou registraci pro všechny virtuální počítače, záznamy DNS pro virtuální počítače nasazené ve virtuální síti se automaticky vytvoří v privátní zóně DNS. Kromě záznamů dopřednépohledu (záznamy A) se pro virtuální počítače automaticky vytvářejí také záznamy zpětného vyhledávání (záznamy PTR).
Pokud do virtuální sítě přidáte další virtuální počítače, budou záznamy DNS pro tyto virtuální počítače automaticky vytvořeny také v propojené privátní zóně DNS.

Když odstraníte virtuální počítač, záznamy DNS pro virtuální počítač se automaticky odstraní ze soukromé zóny DNS.

Automatickou registraci můžete povolit výběrem možnosti "Povolit automatickou registraci" při vytváření propojení virtuální sítě.

![Povolit automatickou registraci](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Omezení

* Automatická registrace funguje pouze pro virtuální počítače. Pro všechny ostatní prostředky, jako jsou interní nástroje pro vyrovnávání zatížení atd., můžete ručně vytvořit záznamy DNS v privátní zóně DNS propojené s virtuální sítí.
* Záznamy DNS se vytvářejí automaticky pouze pro primární nic virtuálního počítače . Pokud vaše virtuální počítače mají více než jednu síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou síťovou.
* automatická registrace pro záznamy IPv6 (AAAA) není podporována.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit privátní zónu ve Službě Azure DNS pomocí [Azure PowerShellu](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých [běžných scénářích privátní zóny,](./private-dns-scenarios.md) které se můžou realizovat pomocí privátních zón v Azure DNS.

* Běžné otázky a odpovědi týkající se privátních zón ve službě Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v [tématu Nejčastější dotazy k privátním DNS](./dns-faq-private.md).
