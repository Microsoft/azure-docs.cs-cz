---
title: Ochrana sítě pomocí Azure Security Center | Dokumentace Microsoftu
description: Tato dokument řeší doporučení ve službě Azure Security Center, které vám pomůžou chránit vaši síť Azure a zůstaňte souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: terrylan
ms.openlocfilehash: 12c00d6dfac6c9c2a377a8c142118ff6fd0af751
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302268"
---
# <a name="protecting-your-network-in-azure-security-center"></a>Ochrana sítě pomocí Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a aplikace.

Tento článek se zabývá doporučení, které se vztahují k vaší síti.  Sítě System center doporučení týkající se další generace bran firewall, skupiny zabezpečení sítě, konfiguraci pravidla pro příchozí provoz a další.  V následující tabulce použijte jako odkaz vám pomůžou pochopit doporučení dostupných síťových a každý z nich, co dělá když je použijete.

## <a name="available-network-recommendations"></a>Doporučení pro síť k dispozici
| Doporučení | Popis |
| --- | --- |
| [Přidání brány firewall příští generace](security-center-add-next-generation-firewall.md) |Doporučuje se přidání brány Firewall pro další generace (NGFW) od partnera Microsoftu o navýšení vaší ochrany zabezpečení. |
| [Směrování provozu jenom přes NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Doporučuje konfiguraci sítě pravidla skupiny zabezpečení (NSG), které vynucují příchozí provoz k vašemu virtuálnímu počítači přes vaše NGFW. |
| [Povolit skupiny zabezpečení sítě u podsítí nebo virtuálních počítačů](security-center-enable-network-security-groups.md) |Doporučuje povolit skupiny Nsg na podsítě nebo virtuálních počítačů. |
| [Omezit přístup přes internetový koncový bod](security-center-restrict-access-through-internet-facing-endpoints.md) |Doporučuje nakonfigurovat pravidla pro příchozí provoz pro skupiny zabezpečení sítě. |

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů pomocí Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana služby Azure SQL ve službě Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
