---
title: Přehled diagnostiky služby Azure App Service | Dokumentace Microsoftu
description: Zjistěte, jak je řešit problémy s vaší aplikací pomocí diagnostiky služby App Service.
keywords: služby App service, služby azure app service, Diagnostika, podpora, webové aplikace s řešením problémů, samoobslužné podpory
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539749"
---
# <a name="azure-app-service-diagnostics-overview"></a>Přehled diagnostiky služby Azure App Service

Pokud používáte webovou aplikaci, budete chtít připravit pro všechny problémy, které mohou vzniknout z 500 chyb pro uživatele o tom, že váš web je vypnutý. Diagnostika služby App Service je inteligentní a interaktivní prostředí při řešení problémů aplikace bez nezbytné konfigurace. Pokud narazíte na problémy s vaší aplikací, diagnostiky App Service poukazuje na čem je problém a provede vás na ty správné informace k snadno a rychle odstraňovat potíže a řešit potíže.

I když toto prostředí je nejužitečnější, pokud máte problémy s vaší aplikací během posledních 24 hodin, diagnostických grafy jsou vždy k dispozici pro vás k analýze.

Diagnostika služby App Service funguje nejen vaší aplikace na Windows, ale také aplikace na [Linux/kontejnery](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [služby App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro), a [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Otevřít diagnostiky App Service

Pro přístup k diagnostice App Service, přejděte do služby App Service webovou aplikaci nebo službu App Service Environment v [webu Azure portal](https://portal.azure.com). V levém navigačním panelu klikněte na **diagnostikovat a řešit problémy**.

Pro službu Azure Functions, přejděte do aplikace function app a v horním navigačním panelu a potom klikněte na **funkce platformy**a vyberte **diagnostikovat a řešit problémy** z **správy prostředků** oddílu.

V domovské stránky diagnostiky služby App Service můžete zvolit kategorii, která nejlépe popisuje problém s vaší aplikací s použitím klíčových slov na každé dlaždici na domovské stránce. Kromě toho tato stránka je místo, kde najdete **diagnostické nástroje** pro aplikace Windows. Zobrazit [diagnostické nástroje (pouze pro aplikace pro Windows)](#diagnostic-tools-only-for-windows-app).

![Domovská stránka](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktivní rozhraní

Po výběru kategorie domovskou stránku, která nejlépe odpovídá vaší aplikace problém interaktivní rozhraní diagnostiky App Service, Genie, může vás provedou diagnostiku a řešení problémů s vaší aplikací. Můžete použít klávesové zkratky dlaždice poskytované Genie zobrazíte úplné diagnostickou sestavu kategorii problému, že máte zájem. Klávesové zkratky dlaždice poskytují přímé způsob přístupu k diagnostických metrik.

![Klávesové zkratky dlaždice](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknutí na těchto dlaždicích, zobrazí se seznam témat souvisejících s popsané v dlaždici. Tato témata poskytují fragmenty důležité informace z úplnou sestavu. Můžete kliknout na kterýkoli z těchto témat budete-li tyto potíže dále prozkoumat. Kromě toho můžete kliknout na **zobrazit úplnou sestavu** prozkoumat všechna témata na jednu stránku.

![Témata](./media/app-service-diagnostics/application-logs-insights-3.png)

![Zobrazit úplnou sestavu](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostickou sestavu

Po zvolení možnosti k prošetření problému dále kliknutím na téma, můžete zobrazit další podrobnosti o téma často doplnit díky grafům a markdowns. Diagnostická zpráva může být výkonný nástroj pro přesné určení problém s vaší aplikací.

![Diagnostickou sestavu](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Kontrola stavu

Pokud nevíte, co je špatně s vaší aplikací nebo nevíte, kde proces řešení potíží vaše potíže, kontrola stavu je dobrým začátkem. Kontrola stavu analyzuje vaše aplikace získáte rychlém, interaktivním přehledu, která poukazuje na co je v pořádku a v čem je problém, o tom, kde hledat k prošetření problému. Rozhraní intelligent a interaktivní vám poskytne pokyny k řešení problémů s procesem. Kontrola stavu je integrovaná s Genie prostředí pro aplikace Windows a webová aplikace dolů diagnostické sestavy pro Linuxové aplikace.

### <a name="health-checkup-graphs"></a>Grafy Kontrola stavu

Existují čtyři různé grafy v Kontrola stavu.

- **požadavky a chyby:** Graf, který zobrazuje počet požadavků provedených za posledních 24 hodin spolu s chyby serveru HTTP.
- **výkon aplikace:** Graf zobrazující doba odezvy během posledních 24 hodin pro různé skupiny percentil.
- **Využití procesoru:** Graf zobrazující celkové procento využití procesoru na instanci za posledních 24 hodin.  
- **využití paměti:** Graf zobrazující využití procento celkové fyzické paměti na instanci za posledních 24 hodin.

![Kontrola stavu](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Zkoumání potíží s kódem aplikace (jenom pro aplikace pro Windows)

Protože mnoho problémů s aplikací se vztahují na problémy v kódu aplikace, diagnostiky App Service integruje [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) výjimky a problémy s závislostí ke korelaci s vybranou výpadky. Application Insights má být povoleno samostatně.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Chcete-li zobrazit výjimek Application Insights a závislosti, vyberte **webové aplikace dolů** nebo **webová aplikace je pomalá** dlaždici klávesové zkratky.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Řešení potíží (pouze pro aplikace pro Windows)

Pokud s konkrétním problémem kategorií během posledních 24 hodin zjistí problém, můžete zobrazit úplné diagnostickou zprávu a diagnostice App Service můžete být vyzváni k zobrazení více řešení problémů s Rady a další kroky pro prostředí více s asistencí.

![Application Insights a řešení potíží a další kroky](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostické nástroje (pouze pro aplikace pro Windows)

Diagnostické nástroje zahrnují rozšířené diagnostické nástroje, zjistěte aplikace help kódu problémy, pomalého chodu, připojovací řetězce a další. a proaktivní nástroje, které vám pomůžou zmírnit problémy s využití procesoru, požadavků a paměti.

### <a name="proactive-cpu-monitoring"></a>Proaktivní monitorování využití procesoru

Proaktivní monitorování procesoru poskytuje snadný a proaktivní způsob k provedení akce, když vaše aplikace nebo podřízený proces pro vaši aplikaci spotřebovává vysoké prostředky procesoru. Můžete nastavit vlastní pravidla prahové hodnoty CPU dočasně vysoké využití procesoru problém zmírnit, dokud nebude nalezen skutečné příčinu neočekávaný problém.

![Proaktivní monitorování využití procesoru](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proaktivní automatické opravy

Jako Proaktivní monitorování využití procesoru, proaktivní automatické opravy nabízí jednoduchý a proaktivní přístup ke zmírnění neočekávané chování vaší aplikace. Můžete nastavit vlastní pravidla založená na počet požadavků, pomalých požadavků, omezení paměti a stavový kód HTTP na akce ke zmírnění aktivační události. Tento nástroj slouží ke zmírnění dočasně na neočekávané chování, dokud nebude nalezen skutečné příčinu problému. Další informace o proaktivní automatické opravy, navštivte [oznamujeme novou automatické opravy prostředí v diagnostice app service](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Proaktivní automatické opravy](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>Analýza změn

V agilním vývojové prostředí někdy se může být obtížné udržovat přehled o všechny změny provedené do vaší aplikace a umožnit samostatně kotvícímu bodu na změnu, která způsobila není v pořádku chování. Změna analýzy můžete zúžit na změny do vaší aplikace pro usnadnění odstraňování poruch prostředí. Změna analýzy je součástí diagnostickou sestavu jako **aplikace spadne** tak můžete současně jiné metriky.

![Změnit analýzy výchozí stránku](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![Rozdílové zobrazení](./media/app-service-diagnostics/diff-view-12.png)

Změna analýzy musí být povoleno před použitím funkce. Další informace o analysis změnit, navštivte [oznamujeme nové prostředí analýzy změn v diagnostice App Service](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).