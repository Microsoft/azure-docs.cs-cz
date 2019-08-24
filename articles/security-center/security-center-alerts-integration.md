---
title: Integrace produktů zabezpečení Azure v Azure Security Center | Microsoft Docs
description: Toto téma představuje produkty zabezpečení Azure, které jsou integrované s Azure Security Center.
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
ms.openlocfilehash: beac7d08a973dd8dc7e450840669bfd5687e76ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013309"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integrace produktů zabezpečení Azure v Azure Security Center

Azure Security Center poskytuje další licence Microsoftu pro práci s následujícími produkty zabezpečení:

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## WAF Azure<a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace jsou stále více zaměřené na škodlivé útoky, které využívají často známá ohrožení zabezpečení. Application Gateway WAF vychází ze základní sady pravidel 3,0 nebo 2.2.9 v otevřeném projektu zabezpečení webové aplikace. WAF se automaticky aktualizuje, aby chránila před novými chybami zabezpečení bez nutnosti další konfigurace. WAF výstrahy se streamují do Security Center. Další informace o výstrahách vygenerovaných službou WAF najdete v tématu [skupiny pravidel a pravidla pro Firewall webových aplikací](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS Protection<a name="azure-ddos"></a>

Je známo, že je možné snadno spustit útok s cílem odepření služeb (DDoS). Jsou to skvělé problémy se zabezpečením, zejména pokud přesouváte aplikace do cloudu. 

Útoky DDoS se pokoušejí vyčerpat prostředky aplikace, aby byla aplikace nedostupná oprávněným uživatelům. Útoky DDoS můžou cílit na libovolný koncový bod, ke kterému se dá získat přístup přes Internet.

Azure DDoS Protection v kombinaci s osvědčenými postupy návrhu aplikace poskytují ochranu před útoky DDoS. DDoS Protection poskytuje různé úrovně služeb. Další informace najdete v tématu [přehled Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection Standard může zmírnit následující typy útoků:

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěn objem útoků**|Cílem tohoto útoku je zahlcení síťové vrstvy významnou velikostí zdánlivě legitimního provozu. Zahrnuje zaplavení UDP, zahlcení zesílení a další falešná zaplave paketů. DDoS Protection Standard snižuje riziko těchto potenciálních útoků s více gigabajty tím, že je absorbuje a nachází z globálního škálování do sítě, a to automaticky.|
|**Byl zjištěn útok protokolu**|Tyto útoky generují cíl nepřístupný, protože využívají slabiny vrstev 3 a 4 protokolu vrstvy 4. Zahrnuje útoky na zaplavení SYN, útoky na reflexi a další útoky protokolu. DDoS Protection Standard tyto útoky omezuje, rozlišuje mezi škodlivým a oprávněným provozem interakci s klientem a blokuje škodlivý provoz.|
|**Byl zjištěn útok na vrstvu prostředků (aplikace).**|Tyto útoky cílí na pakety webových aplikací, aby narušily přenos dat mezi hostiteli. K útokům patří narušení protokolu HTTP, vkládání SQL, skriptování mezi weby a další útoky vrstvy 7. K obraně před těmito útoky použijte Azure Application Gateway WAF s DDoS Protectionm standardem. K dispozici jsou také nabídky WAF třetích stran, které jsou k dispozici v Azure Marketplace.|
