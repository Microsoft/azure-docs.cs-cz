---
title: Co je dílčí zdroj propojení virtuální sítě privátních zón Azure DNS
description: Přehled dílčího prostředku propojení virtuální sítě privátní zóny Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646757"
---
# <a name="what-is-a-virtual-network-link"></a>Co je propojení virtuální sítě?

Jakmile vytvoříte privátní zónu DNS v Azure, není okamžitě přístupná z žádné virtuální sítě. Musíte ji propojit s virtuální sítí, než bude mít virtuální počítač hostovaný v této síti přístup k privátní zóně DNS.
Chcete-li propojit privátní zónu DNS s virtuální sítí, musíte vytvořit propojení virtuální sítě v privátní zóně DNS. Každá privátní zóna DNS má kolekci podřízených prostředků propojení virtuální sítě. Každý z těchto prostředků představuje připojení k virtuální síti.

Virtuální síť můžete propojit s privátní zónou DNS jako registrační virtuální síť nebo jako virtuální síť s rozlišením.

## <a name="registration-virtual-network"></a>Registrace virtuální sítě

Když [vytvoříte propojení](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) mezi privátní zónou DNS a virtuální sítí, máte možnost zapnout [automatickou registraci](./private-dns-autoregistration.md) záznamů DNS pro virtuální počítače. Pokud zvolíte tuto možnost, virtuální síť se stane registrační virtuální síť pro privátní zónu DNS. Záznam DNS se automaticky vytvoří pro virtuální počítače, které nasazujete v síti. Záznamy DNS se vytvářejí pro virtuální počítače, které jste už nasadili ve virtuální síti. Z hlediska virtuální sítě se privátní zóna DNS stane registrační zónou pro tuto virtuální síť.
Jedna privátní zóna DNS může mít více registračních virtuálních sítí, ale každá virtuální síť může mít přesně jednu registrační zónu.

## <a name="resolution-virtual-network"></a>Virtuální síť – rozlišení

Když vytvoříte propojení virtuální sítě v privátní zóně DNS a rozhodnete se nepovolit automatickou registraci záznamu DNS, bude virtuální síť považována za virtuální síť, která se bude chovat pouze jako virtuální síť. Záznamy DNS pro virtuální počítače nasazené v těchto sítích nebudou automaticky vytvořeny v propojené privátní zóně DNS. Virtuální počítače nasazené v takové síti však mohou úspěšně dotazovat záznamy DNS z privátní zóny DNS. Tyto záznamy můžete vytvořit ručně nebo mohou být naplněny z jiných virtuálních sítí, které byly propojeny jako registrační sítě se soukromou zónou DNS.
Jedna privátní zóna DNS může mít více virtuálních sítí s rozlišením a virtuální síť může mít více zón řešení, které jsou k ní přidruženy.

## <a name="limits"></a>Omezení

Informace o počtu registračních a rezolučních sítí můžete propojit se soukromými zónami DNS, viz [Limity Azure DNS](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>Další aspekty

* Virtuální sítě nasazené pomocí klasického modelu nasazení nejsou podporovány.

* Mezi privátní zónou DNS a virtuální sítí můžete vytvořit pouze jedno propojení.

* Každé propojení virtuální sítě v privátní zóně DNS musí mít jedinečný název v kontextu privátní zóny DNS. V různých privátních zónách DNS můžete mít odkazy se stejným názvem.

* Po vytvoření propojení virtuální sítě zkontrolujte pole Stav propojení prostředku propojení virtuální sítě. V závislosti na velikosti virtuální sítě může trvat několik minut, než je propojení v provozu a stav propojení se změní na *Dokončeno*.

* Když odstraníte virtuální síť, automaticky se odstraní všechna propojení virtuálních sítí a automaticky registrované záznamy DNS, které jsou k ní přidruženy v různých privátních zónách DNS.

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak propojit virtuální síť s privátní zónou DNS pomocí [portálu Azure](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)

* Zjistěte, jak vytvořit privátní zónu ve Službě Azure DNS pomocí [Azure PowerShellu](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých [běžných scénářích privátní zóny,](./private-dns-scenarios.md) které se můžou realizovat pomocí privátních zón v Azure DNS.

* Běžné otázky a odpovědi týkající se privátních zón ve službě Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v [tématu Nejčastější dotazy k privátním DNS](./dns-faq-private.md).
