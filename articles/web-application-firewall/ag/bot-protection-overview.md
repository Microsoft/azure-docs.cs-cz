---
title: WAF na Azure Application Gateway bot přehled ochrany
titleSuffix: Azure Web Application Firewall
description: Tento článek obsahuje přehled brány firewall webových aplikací (WAF) na ochranu robotů application gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027092"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Web Application Firewall na Azure Application Gateway ochrana přehled

Zhruba 20% veškerého internetového provozu pochází ze špatných robotů. Dělají věci, jako je škrábání, skenování a hledání zranitelných míst ve vaší webové aplikaci. Když jsou tyto roboty zastaveny u brány firewall webových aplikací (WAF), nemohou na vás zaútočit. Také nemohou využívat vaše prostředky a služby, jako jsou vaše back-endy a další základní infrastruktura.

Můžete povolit sadu spravovaných pravidel ochrany robota pro waf, která blokuje nebo protokoluje požadavky ze známých škodlivých IP adres. IP adresy jsou získávány z informačního kanálu Microsoft Threat Intelligence. Inteligentní graf zabezpečení pohání microsoftovou analýzu hrozeb a používá ho několik služeb včetně Azure Security Center.

> [!IMPORTANT]
> Sada pravidel ochrany robota je aktuálně ve verzi Public Preview a je vybavena předběžnou smlouvou o úrovni služeb. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [doplňkových podmínkách použití pro Microsoft Azure Preview.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="use-with-owasp-rulesets"></a>Použití s sadami pravidel OWASP

Sadu pravidel ochrany botmůžete použít společně s některou z pravidel OWASP (2.2.9, 3.0 a 3.1). V daném okamžiku lze použít pouze jednu sadu pravidel OWASP. Sada pravidel ochrany robota obsahuje další pravidlo, které se zobrazí ve vlastní sadě pravidel. Je s názvem **Microsoft_BotManagerRuleSet_0.1**a můžete jej povolit nebo zakázat jako ostatní pravidla OWASP.

![Sada pravidel botu](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aktualizace sady pravidel

Seznam známých chybných IP adres botů se aktualizuje několikrát denně z kanálu Microsoft Threat Intelligence, aby zůstal synchronizován s roboty. Vaše webové aplikace jsou neustále chráněny, i když se mění vektory útoku robotů.

## <a name="next-steps"></a>Další kroky

- [Konfigurace ochrany botů pro bránu webových aplikací v bráně Aplikace Azure (Preview)](bot-protection.md)
