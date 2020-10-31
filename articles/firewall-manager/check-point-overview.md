---
title: Zabezpečte virtuální rozbočovače Azure pomocí kontrolního bodu cloudguard Connect
description: Další informace o službě Check Point CloudGuard Connect k zabezpečení virtuálních rozbočovačů Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 759b8211aa8790e81c273f3d90d6679e70012ea4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133561"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Zabezpečená virtuální centra pomocí Check Point cloudguard Connect

Check Point CloudGuard Connect je důvěryhodný partner zabezpečení ve Správci Azure Firewall. Chrání globálně distribuovanou pobočku pro připojení k Internetu (B2I) nebo virtuální síť k Internetu (V2I) s pokročilou ochranou před hrozbami. 

Díky jednoduché konfiguraci ve Správci Azure Firewall můžete směrovat připojení k síti rozbočovače a virtuální sítě k Internetu prostřednictvím služby CloudGuard Connect Security as a Service (SECaaS). Provoz je chráněný při přenosu z vašeho rozbočovače do cloudové služby Check Point v tunelech IPsec VPN.

Povolíte-li automatickou synchronizaci na portálu kontrolního bodu, všechny prostředky označené jako *zabezpečené* v Azure Portal budou automaticky zabezpečeny. Nemusíte spravovat prostředky dvakrát. Můžete je jednoduše zabezpečit jednou v Azure Portal.

Kontrolní bod sjednocuje více služeb zabezpečení v rámci jednoho zastřešující. Integrovaný přenos zabezpečení je jednou dešifrován a zkontrolován v rámci jednoho průchodu. Řízení aplikací, filtrování adres URL a povědomí o obsahu (DLP) vynuťte bezpečné webové používání a ochranu vašich dat. IP a antivirová ochrana uživatele před známými síťovými zneužití. Anti-bot blokuje připojení k příkazům a řízení serverů a upozorňuje na to, jestli je hostitel napadený.

Emulace hrozeb (sandboxing) chrání uživatele před neznámými a nulovými hrozbami. Check Point SandBlast Zero-Day Protection je technologie pro seznámení v prostředí písku hostovaná v cloudu, kde jsou soubory rychle umístěné do karantény a kontrolovány. Běží ve virtuálním izolovaném prostoru (sandbox) a před tím, než přejde do vaší sítě, zjistí škodlivé chování. Zabrání hrozbám, než se dokončí škoda za účelem úspory cenných zaměstnanců při reakci na hrozby. 

## <a name="deployment-example"></a>Příklad nasazení

Podívejte se na následující video, kde se dozvíte, jak nasadit kontrolní bod CloudGuard připojit jako důvěryhodný partner pro zabezpečení Azure.

> [!VIDEO https://youtu.be/C8AuN76DEmU]

## <a name="next-steps"></a>Další kroky

- [Nasazení poskytovatele partnera pro zabezpečení](deploy-trusted-security-partner.md)