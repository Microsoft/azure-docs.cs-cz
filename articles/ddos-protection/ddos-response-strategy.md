---
title: Komponenty strategie DDoS Response
description: Zjistěte, jak pomocí Azure DDoS Protection Standard reagovat na útoky DDoS.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 23822ce5b311dc479824128d66bc18a15473862d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905174"
---
# <a name="components-of-a-ddos-response-strategy"></a>Komponenty strategie DDoS Response

Útok DDoS, který cílí na prostředky Azure, obvykle vyžaduje minimální zásah z pohledu uživatele. I když v rámci strategie reakce na incidenty, která zahrnuje zmírnění DDoS, pomáhá minimalizovat dopad na kontinuitu podnikových aplikací.

## <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft má rozsáhlou síť s přehledem hrozeb. Tato síť využívá kolektivní znalosti rozšířené komunity zabezpečení, která podporuje Microsoft online služby, partnery Microsoftu a vztahy v rámci komunity Internet Security. 

Microsoft jako důležitý poskytovatel infrastruktury obdrží včasné upozornění na hrozby. Společnost Microsoft shromažďuje analýzy hrozeb ze svých online služby a ze své globální zákaznické základny. Microsoft zahrnuje veškerou tuto analýzu hrozeb zpátky do Azure DDoS Protection produktů.

Také jednotka digitálních zločinů společnosti Microsoft (DCU) provádí urážlivé strategie proti botnety. Botnety jsou společný zdroj příkazů a řízení pro útoky DDoS.

## <a name="risk-evaluation-of-your-azure-resources"></a>Hodnocení rizik vašich prostředků Azure

Je naprosto důležité porozumět vašemu riziku, co je DDoS útok, nepřetržitě. Pravidelně se zeptat:

- Jaké nové veřejně dostupné prostředky Azure potřebují chránit?

- Je ve službě jediný bod selhání? 

- Jak se dají izolovat služby, aby se omezil dopad útoku, zatímco stále jsou dostupné služby pro platné zákazníky?

- Existují virtuální sítě, ve kterých by měl být povolený DDoS Protection Standard, ale ne? 

- Jsou moje služby aktivní/aktivní s převzetím služeb při selhání napříč více oblastmi?

Je důležité, abyste porozuměli normálnímu chování aplikace a připravili se na to, jestli se aplikace během útoku na DDoS nechová podle očekávání. Máte monitory nakonfigurované pro vaše důležité podnikové aplikace, které napodobují chování klienta, a upozorňovat na to, když se zjistí relevantní anomálie. [Osvědčené postupy pro monitorování a diagnostiku](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) vám pomůžou získat přehled o stavu aplikace.

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací (APM) pro webové vývojáře na různých platformách. Pomocí Application Insights můžete monitorovat živou webovou aplikaci. Automaticky detekuje anomálie výkonu. Obsahuje analytické nástroje, které vám pomohou diagnostikovat problémy a pochopit, co uživatelé s vaší aplikací dělají. Je navržena tak, aby pomáhala průběžně vylepšovat výkon a možnosti využití.

## <a name="customer-ddos-response-team"></a>Tým zákaznických odpovědí na DDoS

Vytvoření týmu DDoS Response je klíčový krok v reakci na útok rychle a efektivně. Identifikujte kontakty ve vaší organizaci, kteří budou dohlížet na plánování i provádění. Tento tým DDoS Response by měl důkladně pochopit službu Azure DDoS Protection Standard. Ujistěte se, že tým může identifikovat a zmírnit útok pomocí koordinace s interními a externími zákazníky, včetně týmu podpory společnosti Microsoft. 

Doporučujeme používat cvičení simulace jako běžnou součást plánování dostupnosti a kontinuity služeb a tato cvičení by měla zahrnovat testování škálování. Podívejte se na [testování prostřednictvím simulací](test-through-simulations.md) , kde se dozvíte, jak simulovat DDoS testovací provoz na veřejné koncové body Azure.

## <a name="alerts-during-an-attack"></a>Výstrahy během útoku

Azure DDoS Protection Standard identifikuje a zmírnit útoky DDoS bez zásahu uživatele. Chcete-li dostávat oznámení, když dojde k aktivnímu zmírnění chráněné veřejné IP adresy, můžete [nakonfigurovat výstrahu](telemetry-monitoring-alerting.md) u metriky **v části útok DDoS nebo ne** . Můžete se rozhodnout, že vytvoříte výstrahy pro ostatní metriky DDoS, abyste porozuměli rozsahu útoku, zahození provozu a dalším podrobnostem.

### <a name="when-to-contact-microsoft-support"></a>Kdy kontaktovat podporu Microsoftu

Zákazníci s Azure DDoS Protection standard mají přístup ke týmu DDoS Rapid Response (DRR), který může pomáhat s vyšetřováním útoků během útoku a analýzou po útoku. Další podrobnosti najdete v tématu [DDoS Rapid Response](ddos-rapid-response.md) , včetně toho, kdy byste měli tým DRR zapojit.

## <a name="post-attack-steps"></a>Kroky po útoku

Je vždycky dobré strategii Postmortem po útoku a podle potřeby upravit strategii DDoS Response. Co je potřeba vzít v úvahu:

- Mohlo dojít k narušení provozu služby nebo uživatele z důvodu nedostatku škálovatelné architektury?

- Které aplikace nebo služby utrpěly nejvíc?

- Jak efektivní je strategie DDoS Response a jak se dá zlepšit?

Pokud máte podezření, že jste s útokem na DDoS, Projděte si normální kanály podpory Azure.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit plán DDoS Protection](manage-ddos-protection.md).