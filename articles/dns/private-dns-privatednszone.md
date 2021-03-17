---
title: Co je Azure DNS privátní zóna
description: Přehled privátní zóny DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9eaa320e79f1d595303c6d9fe1399df12cb6c52b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954405"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Co je privátní zóna Azure DNS

Azure Privátní DNS poskytuje spolehlivou a zabezpečenou službu DNS pro správu a překlad názvů domén ve virtuální síti, aniž by bylo nutné přidávat vlastní řešení DNS. Pomocí privátních zón DNS můžete místo dostupných názvů poskytovaných v Azure použít vlastní názvy domén, a to v současnosti. 

Záznamy obsažené v privátní zóně DNS nelze přeložit z Internetu. Překlad DNS na privátní zónu DNS funguje jenom z virtuálních sítí, které jsou s ním propojené.

Privátní zónu DNS můžete propojit s jednou nebo více virtuálními sítěmi vytvořením [propojení virtuálních](./private-dns-virtual-network-links.md)sítí.
Funkci [automatické registrace](./private-dns-autoregistration.md) můžete také povolit, aby automaticky spravovala životní cyklus záznamů DNS pro virtuální počítače nasazené ve virtuální síti.

## <a name="limits"></a>Omezení

Informace o počtu privátních zón DNS, které můžete v rámci předplatného vytvořit, a o tom, kolik sad záznamů je v privátní zóně DNS podporováno, najdete [Azure DNS omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) .

## <a name="restrictions"></a>Omezení

* Privátní zóny DNS s jedním popiskem nejsou podporovány. Vaše privátní zóna DNS musí mít dva nebo více jmenovek. Například contoso.com má dva popisky oddělené tečkou. Privátní zóna DNS může mít maximálně 34 popisků.
* Delegování zóny (záznamy NS) nemůžete vytvořit v privátní zóně DNS. Pokud máte v úmyslu použít podřízenou doménu, můžete doménu vytvořit přímo jako soukromou zónu DNS a propojit ji s virtuální sítí bez nastavování delegování názvový server z nadřazené zóny.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak vytvořit privátní zónu v Azure DNS pomocí [Azure PowerShell](./private-dns-getstarted-powershell.md) nebo [Azure CLI](./private-dns-getstarted-cli.md).

* Přečtěte si o některých běžných [scénářích privátní zóny](./private-dns-scenarios.md) , které je možné v Azure DNS realizovat s privátními zónami.

* Běžné otázky a odpovědi týkající se privátních zón v Azure DNS, včetně konkrétního chování, které můžete očekávat u určitých druhů operací, najdete v tématu [privátní DNS Nejčastější dotazy](./dns-faq-private.md).