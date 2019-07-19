---
title: Integrace Security Center s produkty zabezpečení Azure | Microsoft Docs
description: Tato témata představují produkty zabezpečení Azure integrované s Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 64a636cc4452de1ef4a2d0e94629e7d8e3a5878d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295741"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Integrace Security Center s produkty zabezpečení Azure v ASC

Security Center nabízí zákazníkům s dalšími licencemi Microsoftu k zaregistrování svých nálezů, aby je Security Center a provedli konsolidovaným způsobem.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## WAF Azure<a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Application Gateway WAF vychází ze základní sady pravidel (počítačový systém) 3,0 nebo 2.2.9 z otevřeného projektu webové aplikace Security (OWASP). WAF se automaticky aktualizuje, aby chránila před novými chybami zabezpečení bez nutnosti další konfigurace. Výstrahy generované službou WAF se streamují do Security Center. Další informace o výstrahách vygenerovaných službou WAF najdete v tomto [článku](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Je známo, že je možné snadno spustit útok s cílem odepření služeb (DDoS). Proto se pro zákazníky, kteří přesunují své aplikace do cloudu, stali skvělými problémy se zabezpečením. 

Útoky DDoS se pokoušejí vyčerpat prostředky aplikace, aby byla aplikace nedostupná oprávněným uživatelům. Útoky DDoS můžou cílit na libovolný koncový bod, ke kterému se dá získat přístup přes Internet.

Ochrana Azure DDoS v kombinaci s osvědčenými postupy návrhu aplikací zajišťuje ochranu před útoky DDoS. Azure DDoS Protection poskytuje různé úrovně služeb. Další informace najdete v tématu [přehled Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection Standard může zmírnit následující typy útoků:

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěn objem útoků**|Cílem tohoto útoku je zahlcení síťové vrstvy významnou velikostí zdánlivě legitimního provozu. Zahrnuje zaplavení UDP, zahlcení zesílení a další falešná zaplave paketů. DDoS Protection Standard snižuje riziko těchto potenciálních útoků s více gigabajty tím, že je absorbuje a je bude používat globální škálování v síti Azure, a to automaticky.|
|**Byl zjištěn útok protokolu**|Tyto útoky vykreslují cíl nepřístupný, protože využívají slabiny vrstvy 3 a protokolu vrstvy 4. Zahrnuje útoky na zaplavení SYN, útoky na reflexi a další útoky protokolu. DDoS Protection Standard tyto útoky omezuje, rozlišuje mezi škodlivým a oprávněným provozem interakci s klientem a blokuje škodlivý provoz.|
|**Byl zjištěn útok na vrstvu prostředků (aplikace).**|Tyto útoky cílí na pakety webových aplikací, aby narušily přenos dat mezi hostiteli. K útokům patří narušení protokolu HTTP, vkládání SQL, skriptování mezi weby a další útoky vrstvy 7. K obraně před těmito útoky použijte bránu firewall webových aplikací Azure Application Gateway s DDoS Protectionm standardem. K dispozici jsou také nabídky firewallu webových aplikací třetích stran dostupné v Azure Marketplace.|
