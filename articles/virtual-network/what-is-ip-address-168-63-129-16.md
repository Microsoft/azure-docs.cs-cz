---
title: Co je IP adresa 168.63.129.16? | Dokumenty Microsoft
description: Přečtěte si o 168.63.129.16 IP adres a o tom, jak funguje s vašimi prostředky.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 0ea8a8ec1a92a7dbc01dddc175f7116825ba00f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067778"
---
# <a name="what-is-ip-address-1686312916"></a>Co je IP adresa 168.63.129.16?

IP adresa 168.63.129.16 je virtuální veřejná IP adresa, která se používá k usnadnění komunikačního kanálu k prostředkům platformy Azure. Zákazníci můžou v Azure definovat libovolný adresní prostor pro svou privátní virtuální síť. Proto se prostředky platformy Azure musí prezentovat jako jedinečná veřejná IP adresa. Tato virtuální veřejná IP adresa usnadňuje následující akce:

- Povolí agentovi virtuálního počítače komunikovat s platformou Azure k signalizaci, že je ve stavu "připraveno".
- Umožňuje komunikaci s virtuálním serverem DNS poskytovat filtrované rozlišení názvů k prostředkům (například k virtuálnímu počítači), které nemají vlastní server DNS. Toto filtrování zajistí, že zákazníci můžou přeložit jenom názvy hostitelů svých prostředků.
- Povolí [sondy stavu z Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) a určí stav virtuálních počítačů.
- Umožňuje virtuálnímu počítači získat dynamickou IP adresu ze služby DHCP v Azure.
- Povoluje zprávy prezenčního signálu agenta hosta pro roli PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Rozsah IP adres 168.63.129.16

Veřejná IP adresa 168.63.129.16 se používá ve všech oblastech a v národních cloudech. Tato speciální veřejná IP adresa je vlastněná Microsoftem a nebude se měnit. Povoluje se výchozí pravidlo skupiny zabezpečení sítě. Tuto IP adresu doporučujeme povolit v jakýchkoli místních zásadách brány firewall v příchozím i odchozím směru. Komunikace mezi touto zvláštní IP adresou a prostředky je bezpečná, protože z této IP adresy může zdroj zprávy vytvořit jenom interní platforma Azure. Pokud je tato adresa zablokovaná, může dojít k neočekávanému chování v nejrůznějších scénářích.

Z této IP adresy pocházejí [Azure Load Balancer sondy stavu](../load-balancer/load-balancer-custom-probe-overview.md) . Pokud zablokujete tuto IP adresu, testy se nezdaří.

Ve scénáři nevirtuální sítě (Classic) se sonda stavu nachází z privátní IP adresy a 168.63.129.16 se nepoužije.

## <a name="next-steps"></a>Další postup

- [Skupiny zabezpečení](security-overview.md)
- [Vytvoření, změna nebo odstranění skupiny zabezpečení sítě](manage-network-security-group.md)
