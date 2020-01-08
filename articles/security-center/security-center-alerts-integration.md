---
title: Detekce hrozeb pro produkty zabezpečení Azure v Azure Security Center
description: Toto téma prezentuje produkty zabezpečení Azure, které Azure Security Center můžou poskytovat detekci hrozeb.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 1ea207f0ba09e0637a08632d5c56591fd1335b22
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665721"
---
# <a name="threat-detection-for-azure-waf-and-azure-ddos-protection"></a>Detekce hrozeb pro Azure WAF a Azure DDoS Protection

Azure Security Center může poskytovat detekci hrozeb pro následující produkty zabezpečení Azure (pro každý produkt se vyžaduje samostatná licence):

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## WAF Azure<a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Application Gateway WAF vychází ze základní sady pravidel 3,0 nebo 2.2.9 v otevřeném projektu zabezpečení webové aplikace. WAF se automaticky aktualizuje, aby chránila před novými chybami zabezpečení bez nutnosti další konfigurace. WAF výstrahy se streamují do Security Center. Další informace o výstrahách vygenerovaných službou WAF najdete v tématu [skupiny pravidel a pravidla pro Firewall webových aplikací](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

Je známo, že je možné snadno spustit útok s cílem odepření služeb (DDoS). Jsou to skvělé problémy se zabezpečením, zejména pokud přesouváte aplikace do cloudu. 

Útoky DDoS se pokoušejí vyčerpat prostředky aplikace, aby byla aplikace nedostupná oprávněným uživatelům. Útoky DDoS můžou cílit na libovolný koncový bod, ke kterému se dá získat přístup přes Internet.

Azure DDoS Protection v kombinaci s osvědčenými postupy návrhu aplikace poskytují ochranu před útoky DDoS. DDoS Protection poskytuje různé úrovně služeb. Další informace najdete v tématu [přehled Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Seznam výstrah Azure DDoS Protection najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-azureddos).