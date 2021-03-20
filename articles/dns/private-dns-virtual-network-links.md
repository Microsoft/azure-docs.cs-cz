---
title: Co je podprostředek odkazu na virtuální síť Azure DNS privátní zóny
description: Přehled dílčího prostředku propojení virtuální sítě a Azure DNS privátní zóna
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 5e43ad35cdaad89d5b9f43007bad8782f358c1a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954337"
---
# <a name="what-is-a-virtual-network-link"></a>Co je propojení virtuální sítě?

Po vytvoření privátní zóny DNS v Azure není k dispozici okamžitě z žádné virtuální sítě. Musíte ho propojit s virtuální sítí, aby měl virtuální počítač hostovaný v této síti přístup k privátní zóně DNS.
Pokud chcete propojit privátní zónu DNS s virtuální sítí, musíte vytvořit odkaz virtuální sítě v privátní zóně DNS. Každá privátní zóna DNS má kolekci podřízených prostředků propojení virtuální sítě. Každý z těchto prostředků představuje připojení k virtuální síti.

Virtuální síť můžete propojit s privátní zónou DNS jako registrační virtuální síť nebo jako virtuální síť pro překlad.

## <a name="registration-virtual-network"></a>Registrační virtuální síť

Při [vytváření propojení](./private-dns-getstarted-portal.md#link-the-virtual-network) mezi privátní zónou DNS a virtuální sítí máte možnost zapnout pro virtuální počítače funkci [autoregistrování](./private-dns-autoregistration.md) záznamů DNS. Pokud zvolíte tuto možnost, virtuální síť se bude pro privátní zónu DNS stala registrační virtuální sítí. Pro virtuální počítače, které nasazujete v síti, se automaticky vytvoří záznam DNS. Pro virtuální počítače, které jste už ve virtuální síti nasadili, se vytvoří záznamy DNS. Z hlediska virtuální sítě se privátní zóna DNS bude zónou registrace pro tuto virtuální síť.
Jedna privátní zóna DNS může mít více registračních virtuálních sítí, ale každá virtuální síť může mít přidruženou přesně jednu registrační zónu.

## <a name="resolution-virtual-network"></a>Překlad virtuální sítě

Když vytvoříte odkaz na virtuální síť v privátní zóně DNS a zvolíte možnost nepovolit autoregistraci záznamů DNS, bude virtuální síť považována za virtuální síť jenom pro řešení. Záznamy DNS pro virtuální počítače nasazené v těchto sítích se automaticky nevytvoří v propojené privátní zóně DNS. Virtuální počítače nasazené v takové síti ale můžou úspěšně dotazovat záznamy DNS z privátní zóny DNS. Tyto záznamy můžete ručně vytvořit sami nebo můžete naplnit z jiných virtuálních sítí propojených jako registrační sítě s privátní zónou DNS.
Jedna privátní zóna DNS může mít více virtuálních sítí pro řešení a virtuální síť může mít k sobě přidruženo více zón řešení.

## <a name="limits"></a>Omezení

Informace o tom, kolik sítí pro registraci a rozlišení, můžete propojit s privátními zónami DNS, najdete v tématu [omezení Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) .

## <a name="other-considerations"></a>Další důležité informace

* Virtuální sítě nasazené pomocí modelu nasazení Classic nejsou podporované.

* Můžete vytvořit jenom jedno propojení mezi privátní zónou DNS a virtuální sítí.

* Každé propojení virtuální sítě v privátní zóně DNS musí mít jedinečný název v kontextu privátní zóny DNS. V různých privátních zónách DNS můžete mít odkazy se stejným názvem.

* Po vytvoření propojení virtuální sítě ověřte pole "stav propojení" prostředku propojení virtuální sítě. V závislosti na velikosti virtuální sítě může trvat několik minut, než se propojení provede, a stav propojení se změní na *dokončeno*.

* Když odstraníte virtuální síť, automaticky se odstraní všechny odkazy virtuální sítě a automaticky zaregistrované záznamy DNS v různých privátních zónách DNS.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak propojit virtuální síť s privátní zónou DNS pomocí [Azure Portal](./private-dns-getstarted-portal.md#link-the-virtual-network)

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).