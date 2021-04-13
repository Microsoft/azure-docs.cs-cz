---
title: Co je Azure DNS privátní zóna
description: Přehled privátní zóny DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 0e04d7525cbd0c707ba0050f31414c2472602d1b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311409"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Co je privátní zóna Azure DNS

Azure Privátní DNS poskytuje spolehlivou a zabezpečenou službu DNS pro správu a překlad názvů domén ve virtuální síti, aniž by bylo nutné přidávat vlastní řešení DNS. Pomocí privátních zón DNS můžete místo dostupných názvů poskytovaných v Azure použít vlastní názvy domén, a to v současnosti. 

Záznamy obsažené v privátní zóně DNS se nedají přeložit z Internetu. Překlad DNS na privátní zónu DNS funguje jenom z virtuálních sítí, které jsou s ním propojené.

Privátní zónu DNS můžete propojit s jednou nebo více virtuálními sítěmi vytvořením [propojení virtuálních](./private-dns-virtual-network-links.md)sítí.
Funkci automatické [registrace](./private-dns-autoregistration.md) můžete také povolit, aby automaticky spravovala životní cyklus záznamů DNS pro virtuální počítače, které se nasazují ve virtuální síti.

## <a name="limits"></a>Omezení

Informace o tom, kolik privátních zón DNS můžete vytvořit v rámci předplatného a kolik sad záznamů je v privátní zóně DNS podporované, najdete v tématu [omezení Azure DNS](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) .

## <a name="restrictions"></a>Omezení

* Privátní zóny DNS s jedním popiskem nejsou podporovány. Vaše privátní zóna DNS musí mít dva nebo více jmenovek. Například contoso.com má dva popisky oddělené tečkou. Privátní zóna DNS může mít maximálně 34 popisků.
* Delegování zóny (záznamy NS) nemůžete vytvořit v privátní zóně DNS. Pokud máte v úmyslu použít podřízenou doménu, můžete doménu vytvořit přímo jako soukromou zónu DNS. Pak ho můžete propojit s virtuální sítí bez nastavení delegování názvový server z nadřazené zóny.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).