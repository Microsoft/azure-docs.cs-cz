---
title: Ochrana sítě v Azure Security Center | Microsoft Docs
description: Tento dokument adresy doporučení v Azure Security Center, které vám pomůžou chránit vaši síť Azure a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 00b715507a7c3a4d784b800e7bf0c700f6ea6ff1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866475"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Ochrana sítě v Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a aplikace.

Tento článek se zaměřuje na doporučení, které se vztahují k vaší síti.  Síťového střediska doporučení ohledně další brány firewall generace, skupiny zabezpečení sítě, konfiguraci pravidla pro příchozí provoz a další.  Použijte v následující tabulce vám pomohou pochopit dostupné síťové doporučení a co každé z nich dělá Pokud použijete ho jako odkaz.

## <a name="available-network-recommendations"></a>Doporučení k dispozici sítě
| Doporučení | Popis |
| --- | --- |
| [Přidání brány firewall příští generace](security-center-add-next-generation-firewall.md) |Doporučuje přidat brány Firewall pro další generace (NGFW) z partnera společnosti Microsoft pro zvýšení ochrany vaší zabezpečení. |
| [Směrování provozu jenom přes NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Doporučuje konfiguraci pravidla zabezpečení skupiny (NSG) sítě, které vynutit příchozí provoz do virtuálního počítače prostřednictvím vaší NGFW. |
| [Povolení skupin zabezpečení sítě pro podsítě nebo virtuální počítače](security-center-enable-network-security-groups.md) |Doporučuje, abyste povolili skupiny Nsg na podsítě nebo virtuálních počítačů. |
| [Omezení přístupu prostřednictvím internetové koncový bod](security-center-restrict-access-through-internet-facing-endpoints.md) |Doporučuje konfigurace pravidla pro příchozí provoz pro skupiny Nsg. |

## <a name="see-also"></a>Viz také
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů v Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací v Azure Security Center](security-center-application-recommendations.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
