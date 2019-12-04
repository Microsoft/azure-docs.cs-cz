---
title: Azure Security Center doporučení pro práci v síti
description: V tomto článku jsou uvedená doporučení zabezpečení Azure Security Center, která vám pomůžou chránit vaše síťové prostředky.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781966"
---
# <a name="network-recommendations---reference-guide"></a>Doporučení pro síť – referenční příručka

Tento článek poskytuje úplný seznam doporučení, která se můžou zobrazit v Azure Security Center týkající se topologie vaší sítě a vašich internetových koncových bodů.

Vysvětlení, jak tyto funkce Najít a jak je vyřešit, najdete [v tématu.](security-center-network-recommendations.md)

## <a name="network-recommendations"></a>Doporučení pro síť

|Název doporučení|Popis|Závažnost|Bezpečnostní skóre|Typ prostředku|
|----|----|----|----|----|----|
|Měly by být povolené skupiny zabezpečení sítě na úrovni podsítě.|Povolte skupinám zabezpečení sítě řídit síťový přístup k prostředkům nasazeným v podsítích.|Vysoká/střední|30|Podsíť|
|Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.|Povolte skupinám zabezpečení sítě řízení přístupu k síti virtuálních počítačů.|Vysoká/střední|30|Virtuální počítač|
|Přístup by měl být omezený na povolující skupiny zabezpečení sítě u virtuálních počítačů s přístupem k Internetu.|Tím, že omezíte přístup ke stávajícím pravidlům povolení, posílíte skupiny zabezpečení sítě vašich virtuálních počítačů s přístupem k Internetu.|Vysoký|20|Virtuální počítač|
|Pravidla pro webové aplikace v IaaS skupin zabezpečení sítě by se měla posílit.|Posílit zabezpečení skupiny zabezpečení sítě (NSG) virtuálních počítačů, na kterých běží webové aplikace, s pravidly NSG, která jsou přesná, s ohledem na porty webových aplikací.|Vysoký|20|Virtuální počítač|
|Přístup k App Services by měl být omezený.|Omezte přístup k vašemu App Services změnou konfigurace sítě tak, aby odepřela příchozí provoz z rozsahů, které jsou příliš široké.|Vysoký|10|App Service|
|Porty pro správu by měly být uzavřeny na virtuálních počítačích|Posílení skupiny zabezpečení sítě virtuálních počítačů pro omezení přístupu k portům pro správu.|Vysoký|10|Virtuální počítač|
Měla by být povolená DDoS Protection Standard.|Chraňte virtuální sítě obsahující aplikace s veřejnými IP adresami tím, že povolíte službu DDoS Protection Service Standard. DDoS Protection umožňuje zmírnit snižování objemu sítě a útoků protokolu.|Vysoký|10|Virtuální síť|
|Předávání IP na virtuálním počítači by mělo být zakázané.|Zakažte předávání IP. Když je na síťové kartě virtuálního počítače povolené předávání IP, může tento počítač přijímat přenosy adresované do jiných cílů. Předávání IP adres se nevyžaduje zřídka (například při použití virtuálního počítače jako síťového virtuálního zařízení), a proto by mělo být přezkoumáno týmem zabezpečení sítě.|Střední|10|Virtuální počítač|
|Webová aplikace by měla být přístupná jen přes protokol HTTPS|Povolí přístup pouze HTTPS k webovým aplikacím. Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy.|Střední|20|Webová aplikace|
|Na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu|Použijte řízení přístupu k virtuálnímu počítači JIT (just-in-time) k trvalému uzamčení přístupu k vybraným portům a povolte oprávněným uživatelům, aby je mohli otevřít přes JIT, jenom po omezené množství času.|Vysoký|20|Virtuální počítač|
|Aplikace Function app by měly být přístupné jenom přes HTTPS|Povolte přístup pouze HTTPS pro aplikace Function App. Použití protokolu HTTPS zajišťuje ověřování serveru nebo služby a chrání data při přenosu z útoků s odposloucháváním síťové vrstvy.|Střední|20|Function App|
|Zabezpečený přenos do účtů úložiště by měl být povolený.|Povolte zabezpečený přenos do účtů úložiště. Zabezpečený přenos je možnost, která vynutí, aby váš účet úložiště přijímal požadavky jenom od zabezpečených připojení (HTTPS). Použití protokolu HTTPS zajišťuje ověřování mezi serverem a službou a chrání přenášená data z útoků síťové vrstvy, jako jsou například útoky prostředníkem, odposlouchávání a napadení relace.|Vysoký|20|Účet úložiště|


## <a name="next-steps"></a>Další kroky
Další informace o doporučeních, která se vztahují na jiné typy prostředků Azure, najdete v následujících tématech:

* [Monitorování identity a přístupu](security-center-identity-access.md)
* [Ochrana počítačů a aplikací](security-center-virtual-machine-protection.md)
* [Ochrana služby Azure SQL](security-center-sql-service-recommendations.md)

