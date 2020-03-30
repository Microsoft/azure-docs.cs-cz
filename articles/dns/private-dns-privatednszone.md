---
title: Co je prizučná zóna Azure DNS
description: Přehled privátní zóny DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646791"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Co je privátní zóna Azure DNS

Azure Private DNS poskytuje spolehlivou a zabezpečenou službu DNS pro správu a překlad názvů domén ve virtuální síti bez nutnosti přidávat vlastní řešení DNS. Pomocí privátních zón DNS můžete použít vlastní názvy domén, nikoli názvy poskytované Azure, které jsou dnes k dispozici. 

Záznamy obsažené v privátní zóně DNS nelze řešit z Internetu. Rozlišení DNS proti privátní zóně DNS funguje pouze z virtuálních sítí, které jsou s ní propojeny.

Privátní zónu DNS můžete propojit s jednou nebo více virtuálními sítěmi vytvořením [propojení virtuálních sítí](./private-dns-virtual-network-links.md).
Funkci automatické [registrace](./private-dns-autoregistration.md) můžete také povolit k automatické správě životního cyklu záznamů DNS pro virtuální počítače nasazené ve virtuální síti.

## <a name="limits"></a>Omezení

Informace o tom, kolik privátních zón DNS můžete vytvořit v předplatném a kolik sad záznamů je v privátní zóně DNS podporováno, viz [Limity Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Omezení

* Privátní zóny DNS s jedním popiskem nejsou podporovány. Soukromá zóna DNS musí mít dva nebo více popisků. Například contoso.com má dva popisky oddělené tečkou. Soukromá zóna DNS může mít maximálně 34 popisků.
* Delegování zón (záznamy NS) nelze vytvořit v privátní zóně DNS. Pokud chcete použít podřízenou doménu, můžete přímo vytvořit doménu jako privátní zónu DNS a propojit ji s virtuální sítí bez nastavení delegování názvového serveru z nadřazené zóny.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak vytvořit privátní zónu ve Službě Azure DNS pomocí [Azure PowerShellu](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých [běžných scénářích privátní zóny,](./private-dns-scenarios.md) které se můžou realizovat pomocí privátních zón v Azure DNS.

* Běžné otázky a odpovědi týkající se privátních zón ve službě Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v [tématu Nejčastější dotazy k privátním DNS](./dns-faq-private.md).
