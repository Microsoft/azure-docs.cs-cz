---
title: Odolnost prostřednictvím monitorování a analýzy pomocí Azure AD B2C | Microsoft Docs
description: Odolnost prostřednictvím monitorování a analýzy pomocí Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a834d4d30c40b618b1601a7f8901c68143ef4912
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648513"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Odolnost prostřednictvím monitorování a analýzy

Monitorování maximalizuje dostupnost a výkon vašich aplikací a služeb. Poskytuje komplexní řešení pro shromažďování, analýzu a jednání telemetrie z vaší infrastruktury a aplikací. Výstrahy proaktivně upozorňují na problémy nalezené u vaší služby nebo aplikací. Umožňují identifikovat a řešit problémy dříve, než je koncoví uživatelé vaší služby všimnete. [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) pomáhá analyzovat, Hledat protokoly auditu a protokoly přihlášení a vytvářet vlastní zobrazení.

## <a name="monitor-and-get-notified-through-alerts"></a>Monitorování a upozorňování prostřednictvím výstrah

Monitorování systému a infrastruktury je důležité, aby se zajistil celkový stav vašich služeb. Začíná definicí obchodních metrik, jako je například doručení nového uživatele, sazby ověřování koncového uživatele a převod. Nakonfigurujte takové indikátory, které se mají monitorovat. Pokud plánujete, že máte v úmyslu nadcházející nárůst z důvodu propagačního nebo nedovoleného provozu, Revidujte své odhady konkrétně pro událost a odpovídající srovnávací testy pro obchodní metriky. Po události se vraťte k předchozímu srovnávacímu testu.

Podobně pro detekci selhání nebo přerušení výkonu můžete nastavit dobrý směrný plán a následně definovat upozorňování, což je nepostradatelný postup pro okamžité reakce na nově vznikající problémy.

![Obrázek ukazuje komponenty monitorování a analýzy](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Postup implementace monitorování a upozorňování

- **Monitorování**: pomocí [Azure monitor](../../active-directory-b2c/azure-monitor.md) můžete průběžně monitorovat stav pro klíčové cíle na úrovni služby (SLO) a dostávat oznámení vždy, když dojde ke kritické změně. Začněte tím, že identifikujete zásady Azure AD B2C nebo aplikaci jako důležitou součást vaší firmy, jejíž stav je potřeba monitorovat, aby bylo možné zachovat SLO. Identifikujte klíčové indikátory, které odpovídají vašemu slo.
Například můžete sledovat následující metriky, protože náhlé zrušení v podniku vede ke ztrátě v podnikání.

  - **Celkový počet požadavků**: celkový počet požadavků odeslaných do zásad Azure AD B2C celkem "n".

  - **Úspěšnost (%)**: úspěšné požadavky/celkový počet požadavků.

  Přístup k [klíčovým indikátorům](../../active-directory-b2c/view-audit-logs.md) v [application Insights](../../active-directory-b2c/analytics-with-application-insights.md) , Azure AD B2C kde se ukládají protokoly na základě zásad, [protokoly auditu](../../active-directory-b2c/analytics-with-application-insights.md)a protokoly pro přihlášení.  

   - **Vizualizace**: pomocí řídicích panelů pro sestavení Log Analytics můžete vizuálně monitorovat klíčové indikátory.

   - **Aktuální období**: Vytvořte dočasné grafy, abyste zobrazili změny v celkových požadavcích a úspěšnosti (%). v aktuálním období, například aktuální týden.

   - **Předchozí období**: Vytvořte dočasné grafy, abyste zobrazili změny v celkových požadavcích a úspěšnosti (%). v některém z předchozích období pro referenční účely, například minulý týden.

- **Upozornění**: pomocí Log Analytics definujte [výstrahy](../../azure-monitor/alerts/alerts-log.md) , které se aktivují, když se v klíčových ukazatelích dostanou náhlé změny. Tyto změny mohou negativně ovlivnit slo. Výstrahy využívají různé formy metod oznámení, včetně e-mailu, SMS a webhooků. Začněte tím, že definujete kritérium, které funguje jako prahová hodnota, na které se výstraha aktivuje. Například:
  - Výstraha proti náhlému poklesu celkového počtu požadavků: aktivuje výstrahu v případě, že celkový počet požadavků vynechává náhlé. Pokud je třeba 25% poklesu celkového počtu požadavků v porovnání s předchozím obdobím, vyvolejte výstrahu.  
  - Výstraha v důsledku významné míry poklesu úspěšnosti (%): aktivovat výstrahu, když je míra úspěšnosti vybrané zásady významně odložena.
  - Po přijetí výstrahy vyřešte problém pomocí [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)a [rozšíření vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pro Azure AD B2C. Po vyřešení problému a nasazení aktualizované aplikace nebo zásady bude nadále monitorovat klíčové indikátory, dokud se nevrátí zpět do normálního rozsahu.

- **Výstrahy služby**: pomocí [Azure AD B2C výstrah na úrovni služby](../../service-health/service-health-overview.md) se dozvíte o problémech se službami, plánované údržbě, poradenském poradenství a poradním zpravodaji zabezpečení.

- **Vytváření sestav**: pomocí [Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)můžete sestavovat sestavy, které vám pomůžou získat přehled o informacích o uživateli, technických výzev a možnostech růstu.
  - **Řídicí panel stav**: vytvoření [vlastních řídicích panelů pomocí funkce řídicího panelu Azure](../../azure-monitor/app/tutorial-app-dashboards.md) , která podporuje přidávání grafů pomocí Log Analytics dotazů. Například Identifikujte vzor úspěšných a neúspěšných přihlášení, důvody selhání a telemetrii o zařízeních použitých k vytvoření požadavků.
  - **Opuštění Azure AD B2C cest**: pomocí [sešitu](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) sledujte Seznam zrušených Azure AD B2Cch cest, kde uživatel spustil registraci nebo cestu k registraci, ale nikdy ji nedokončil. Poskytuje vám podrobné informace o ID zásad a rozdělení kroků, které uživatel provedl před opuštěním cesty.
  - **Azure AD B2C monitorování sešitů**: pomocí [sešitů monitorování](https://github.com/azure-ad-b2c/siem), které zahrnují Azure AD B2C řídicího panelu, služby Multi-Factor Authentication (MFA), sestavy podmíněného přístupu a protokoly hledání podle ID korelace, získáte lepší přehled o stavu vašeho Azure AD B2C prostředí.
  
## <a name="next-steps"></a>Další kroky

- [Prostředky odolnosti pro vývojáře Azure AD B2C](resilience-b2c.md)
  - [Odolné prostředí koncového uživatele](resilient-end-user-experience.md)
  - [Odolná rozhraní s externími procesy](resilient-external-processes.md)
  - [Zajištění odolnosti pomocí osvědčených postupů pro vývojáře](resilience-b2c-developer-best-practices.md)
- [Odolnost sestavení v infrastruktuře ověřování](resilience-in-infrastructure.md)
- [Zvýšení odolnosti při ověřování a autorizaci ve vašich aplikacích](resilience-app-development-overview.md)