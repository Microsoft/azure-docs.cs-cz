---
title: Porovnání Azure Mobile Engagement s jinými službami podobné Azure
description: Porovnání s jinými podobné službami Azure - HockeyApp, AppInsights, centra oznámení Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 57be3b235c5f439741155a70df7ddbe8a80906f2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Porovnání Azure Mobile Engagement s jinými službami podobné Azure
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Seznam služeb, které nabízí Microsoft Azure je někdy rozšiřování a v některých případech může zajímat, jak se liší od této služby, který jste právě číst nebo uslyšíme Azure Mobile Engagement. Tento článek se pokusí vymazat záměně a přesměruje, abyste si zvolili Azure Mobile Engagement, když je nejvhodnější pro vaše použití. 

Azure Mobile Engagement je služba, konkrétně cílem **pro digitální obchodníci/CMOs** , ale může podle **vlastníka mobilní aplikace nebo vydavatele** kdo chce zvýšit využití, uchovávání a možnostmi finančního zhodnocení z jejich mobilních aplikací. 

*Je platformu zapojení uživatelů software jako služba (SaaS), která poskytuje datové přehledy o využití aplikace, segmentaci uživatelů v reálném čase a umožňuje zasílat kontextová nabízená oznámení a zasílání zpráv v aplikaci.* 

Rozdělení tuto definici, máme následující klíčové vlastnosti, které se také označuje jeho nabídky jedinečnou hodnotu:

1. **Kontextová nabízená oznámení a zasílání zpráv v aplikaci**
   
   Toto je základní fokus produktu – provádí cílové a přizpůsobené nabízená oznámení. A pro k tomu dojít, shromažďujeme bohaté chování analytická data. 
2. **Řízené daty přehledy o využití aplikace**
   
   Poskytujeme křížové platformy sady SDK ke shromažďování analýzy chování o uživatele aplikace. Poznámka: analýzy chování pojem (vůči analýzy výkonu), protože zaměříme na to jak uživatelé aplikaci používají aplikaci. Shromažďovány základní výkon analytická data o chyby, dojde k chybě atd, ale který je není fokus základní produktu. 
3. **Segmentaci uživatelů v reálném čase**
   
   Po shromáždění dat pro vypracování analýzy chování uživatelů aplikace, nám umožní segmentu cílovou skupinu na základě různých parametrů a shromažďují data vám umožní spustit cílové nabízené kampaně. 
4. **Software-as-a-service (SaaS):**
   
   Máme nezávislý na portál pro správu Azure, která je optimalizovaná pro interakci a zobrazení bohaté analýzy chování o uživatelích, aplikace a spuštění marketingových kampaní nabízených portál. Produkt je cílem si přejdete v žádný čas!   

S touto sadou rozdíl v dolním tady je, jak jsme porovnávat s další zdánlivě podobné nabídky Azure – Upozorňujeme, že článek neprovádí úrovně porovnání funkcí, ale má další scénář založené na přístup k definování, který produkt funguje nejlépe:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) je mobilní řešení DevOps společnosti Microsoft. Pomocí něho můžete distribuovat sestavení pro beta testery, shromažďovat data o chybách a získávat zpětnou vazbu od uživatelů. Je integrován se sadou Visual Studio Team Services umožňující snadné nasazení sestavení a integraci pracovních položek. 
   
   Zaměřuje se na DevOps a shromažďování výkonu analytická data o mobilních aplikacích. Můžete skončit s integrací obou HockeyApps a nebude se Mobile Engagement v aplikaci a že neobvyklé, protože i když některé překrývajících v shromážděná data se fokus základní produkty se liší a pomáhají v dosažení jiný cíl pro vás.  
2. [Application Insights](../application-insights/app-insights-overview.md) Pokud má vaše mobilní aplikace straně serveru, pak budete používat Application Insights pro monitorování na straně serveru webové aplikace, ale pro stranu klienta mobilní aplikace, měli byste použít HockeyApp. 
3. [Centra oznámení](https://azure.microsoft.com/services/notification-hubs/) poskytuje odlehčený službu v tom smyslu, že nepotřebujete sady SDK integrovaná do mobilní aplikace a může mít plnou kontrolu nad mobilní aplikace a umožňuje odesílání nabízených oznámení pomocí základních funkcí označování. Toto je skvělá pro všechny aplikace vlastníka, který zdroje menší o cílení a více o odesílání komunikaci informace okamžitě uživatelům aplikace (vysílání velké populaci). 
   
   Všimněte si zaměřuje na odesílání svěží rychlé oznámení se schopností základní segmentace. 

Podívejme se na některých scénářích:

1. TIM je součástí digitální uvedeni v úložišti společnosti Adventure Works, která publikuje mobilní aplikace pro uživatele. TIM je cílem je zajistit, že uživatelé, kteří stáhnout své mobilní aplikace ho nadále používat a často. To nejenom zvýší brand připojit s uživatele aplikace, ale taky zvyšuje možnostmi finančního zhodnocení při nákupech uživatele aplikace, pomocí mobilní aplikace. Pro tento Tim muset odesílat cílená oznámení uživatelům aplikace, které budou užitečné, otevřete aplikaci, a vraťte se k němu často podporovat. Toto je, kde Tim užitečné Mobile Engagement. 
2. Joann je součástí týmu pro vývoj mobilních aplikací ve společnosti Adventure Works a hledá produkt do protokolu informace o selhání, výjimky a získat výkonu telemetrie z aplikace. Toto je, kde Joann užitečné HockeyApp. Joann může integrovat HockeyApp pro svůj scénáře vývojáře zaměřuje i Azure Mobile Engagement pro Tim ve stejné aplikaci získat nejlepší z obou. 
3. Každý s každým je součástí týmu pro vývoj mobilních aplikací v síti Contoso zprávy a všechny, které Jana chce, aby se budou odesílat nejnovější zprávy upozornění pro všechny uživatele bez mnohem segmentace, jakmile je výstraha zprávy. Toto je, kde každý s každým užitečné centra oznámení. 
   V tomto scénáři je možné, ale během existence mobilní aplikace se požadavek dělat mnohem širší segmentace a získat tak podrobné údaje o chování uživatele aplikace. V tomto okamžiku bude mít každý s každým vyhodnotit Azure Mobile Engagement. 

Recap, účel Mobile Engagement není právě ke shromažďování analytics – není "ještě jiné Analytics produkt společnosti Microsoft". Jde o odesílání cílové nabízených oznámení a pro tuto cílovou jsme shromažďování dat pro vypracování analýzy chování ale fokus zůstává na odesílání nabízených oznámení, které zajistit nejvhodnější uživatelům aplikace, takže není setkat za spam. 

Další podrobnosti – prohlédněte si to [rychlé video](mobile-engagement-overview.md) o Mobile Engagement v nutshell. 

