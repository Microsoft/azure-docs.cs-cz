---
title: Security Center integraci s produkty zabezpečení Azure | Dokumentace Microsoftu
description: Toto téma představuje produkty zabezpečení Azure, které integrovaly s Azure Security Center.
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
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571735"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Security Center integrace s produkty Azure zabezpečení ASC

Security Center nabízí zákazníkům s další licence společnosti Microsoft pro připojení ke službě Security Center svá zjištění a zobrazení konsolidovaných způsobem.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway nabízí Firewall webových aplikací (WAF) poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení.

Webové aplikace stále častěji cílí útoky se zlými úmysly, které zneužívají běžně známé chyby zabezpečení. Waf služby Application Gateway je založená na základní pravidlo nastavte (CRS) 3.0 nebo 2.2.9 z Open Web Application zabezpečení projektu (OWASP). WAF se aktualizuje automaticky chránit proti nová ohrožení zabezpečení, nepotřebují žádnou další konfiguraci. Výstrahy vygenerované WAF se streamují do služby Security Center. Další informace o výstrahách generovaných WAF, najdete v tomto [článku](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Distribuované útoky na dostupnost služeb (DDoS) se ví, že se mělo snadno provést. Proto budou problém skvělé zabezpečení pro zákazníky, kteří jsou přesun aplikací do cloudu. 

Útoky DDoS se pokoušejí vyčerpat prostředky aplikace, aby byla aplikace nedostupná oprávněným uživatelům. Útoky DDoS můžete cílit na libovolný koncový bod, který můžete kontaktovat přes Internet.

Azure DDoS protection v kombinaci s aplikací osvědčené postupy pro navrhování, poskytují ochranu před útoky DDoS. Azure DDoS protection nabízí různých úrovní služeb. Další informace najdete v tématu [Přehled služby Azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS Protection standardní lze zmírnit tyto typy útoků:

> [!div class="mx-tableFixed"]

|Výstrahy|Popis|
|---|---|
|**Zjištěna odměrné útoku**|Cílem tohoto útoku je k vyplnění síťové vrstvě s vyžadovat značné množství zdánlivě legitimní provoz. Obsahuje UDP záplavy zesílení záplavy a dalších záplavy falešné paketů. DDoS Protection Standard omezuje tyto potenciální útoky více GB zajistit plynulý provoz a čištění, škálování globální sítě Azure, automaticky.|
|**Protokol útoku zjistil**|Tyto útoky cíl vykreslování nedostupný, zneužitím slabé stránky ve vrstvě 3 a 4 protokolů vrstvy. Zahrnuje, SYN útokům zahlcení reflexe útoky a dalších útoků protokolu. DDoS Protection standardní zmírňuje tyto útoky rozlišování škodlivým a legitimní provoz tak, že komunikaci s klientem a blokování škodlivý provoz.|
|**Útok na prostředku (aplikace) zjistil**|Tyto útoky směrovat pakety webové aplikace, narušit přenos dat mezi hostiteli. Útoky patří HTTP narušením protokolu SQL vkládání, skriptování napříč weby a další útoky vrstvy 7. Pomocí Azure Application Gateway firewall webových aplikací, DDoS Protection standardní braňte se proti těmto útokům. Existují také nabídky brány firewall třetích stran webových aplikací k dispozici na webu Azure Marketplace.|
