---
title: Nástroj diagnostika a řešení
description: Zjistěte, jak můžete řešit problémy s vaší aplikací ve službě Azure App Service pomocí nástroje diagnostiky a řešení na webu Azure Portal.
keywords: služby aplikace, služby azure app, diagnostika, podpora, webová aplikace, řešení potíží, svépomoc
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: e06e71d4436ac6c64ff2edc876d7849d084482f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671638"
---
# <a name="azure-app-service-diagnostics-overview"></a>Přehled diagnostiky služby Azure App Service

Když používáte webovou aplikaci, chcete být připraveni na všechny problémy, které mohou vzniknout, od 500 chyb až po uživatele, kteří vám říkají, že váš web je mimo provoz. Diagnostika služby App Service je inteligentní a interaktivní prostředí, které vám pomůže vyřešit potíže s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, diagnostika služby App Service poukazuje na to, co je špatné, aby vás vedlo ke správným informacím snadněji a rychleji řešit a vyřešit problém.

I když je toto prostředí velmi užitečné, když máte problémy s vaší aplikací během posledních 24 hodin, všechny diagnostické grafy jsou vždy k dispozici k analýze.

Diagnostika služby App Service funguje nejen pro vaši aplikaci ve Windows, ale také pro aplikace v [Linuxu/kontejnerech](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [prostředí App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)a Azure [Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Otevřít diagnostiku služby App Service

Chcete-li získat přístup k diagnostice služby App Service, přejděte na portál Azure na [webu App](https://portal.azure.com)Service do webové aplikace App Service nebo prostředí služby App Service . V levém navigačním panelu klikněte na **Diagnostikovat a vyřešit problémy**.

V části Funkce Azure přejděte do aplikace funkce a v horní navigaci klikněte na **funkce platformy**a v části **Správa prostředků** vyberte **Diagnostikovat a řešit problémy.**

Na domovské stránce diagnostiky služby App Service můžete zvolit kategorii, která nejlépe popisuje problém s vaší aplikací, a to pomocí klíčových slov na každé dlaždici domovské stránky. Na této stránce najdete také **diagnostické nástroje** pro aplikace pro Windows. Viz [Diagnostické nástroje (pouze pro aplikaci pro Windows).](#diagnostic-tools-only-for-windows-app)

![Domovská stránka](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktivní rozhraní

Jakmile vyberete kategorii domovské stránky, která nejlépe odpovídá problému vaší aplikace, interaktivní rozhraní diagnostiky služby App Service, Genie, vás může provést diagnostikou a řešením problému s vaší aplikací. Pomocí klávesových zkratek dlaždic, které genie poskytuje, můžete zobrazit úplnou diagnostickou zprávu o kategorii problémů, která vás zajímá. Klávesové zkratky dlaždic poskytují přímý způsob přístupu k diagnostickým metrikám.

![Klávesové zkratky dlaždic](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknutí na tyto dlaždice, můžete vidět seznam témat souvisejících s problémem popsaným v dlaždici. Tato témata obsahují úryvky pozoruhodných informací z celé sestavy. Můžete kliknout na některou z těchto témat, abyste prošetřili problémy dále. Můžete také kliknout na **zobrazit celou sestavu** a prozkoumat všechna témata na jedné stránce.

![Témata](./media/app-service-diagnostics/application-logs-insights-3.png)

![Zobrazit úplnou zprávu](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostická zpráva

Poté, co se rozhodnete problém dále prozkoumat kliknutím na téma, můžete zobrazit další podrobnosti o tématu často doplněné o grafy a markdowns. Diagnostická sestava může být výkonný nástroj pro určení problému s vaší aplikací.

![Diagnostická zpráva](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Kontrola stavu

Pokud nevíte, co je s vaší aplikací špatně, nebo nevíte, kde začít řešit problémy, je kontrola stavu dobrým místem, kde začít. Kontrola stavu analyzuje vaše aplikace, aby vám poskytla rychlý a interaktivní přehled, který poukazuje na to, co je zdravé a co je špatně, a řekne vám, kde hledat, abyste problém prozkoumali. Jeho inteligentní a interaktivní rozhraní vám poskytuje pokyny prostřednictvím procesu řešení problémů. Kontrola stavu je integrovaná s genie prostředí pro aplikace pro Windows a webové aplikace dolů diagnostické sestavy pro linuxové aplikace.

### <a name="health-checkup-graphs"></a>Grafy kontroly stavu

Existují čtyři různé grafy v kontrole stavu.

- **žádosti a chyby:** Graf, který zobrazuje počet požadavků za posledních 24 hodin spolu s chybami serveru HTTP.
- **výkon aplikace:** Graf, který zobrazuje dobu odezvy za posledních 24 hodin pro různé skupiny percentilů.
- **Využití procesoru:** Graf, který zobrazuje celkové procento využití procesoru na instanci za posledních 24 hodin.  
- **využití paměti:** Graf, který zobrazuje celkové procento využití fyzické paměti na instanci za posledních 24 hodin.

![Kontrola stavu](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Prozkoumání problémů s kódem aplikace (jenom pro aplikaci pro Windows)

Vzhledem k tomu, že mnoho problémů s aplikacemi souvisí s problémy v kódu aplikace, diagnostika služby App Service se integruje s [Application Insights,](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aby zvýraznila výjimky a problémy se závislostmi, aby korelovala s vybranými prostoji. Application Insights musí být povolena samostatně.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Chcete-li zobrazit výjimky a závislosti Application Insights, vyberte **zástupce webových aplikací dolů** nebo pomalou dlaždicovou **aplikaci.**

### <a name="troubleshooting-steps-only-for-windows-app"></a>Postup řešení potíží (pouze pro aplikaci pro Windows)

Pokud je zjištěn problém s konkrétní kategorií problému během posledních 24 hodin, můžete zobrazit úplnou diagnostickou zprávu a diagnostika služby App Service vás může vyzvat k zobrazení dalších rad pro řešení potíží a dalších kroků pro více řízené prostředí.

![Přehledy aplikací a řešení potíží a další kroky](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostické nástroje (pouze pro aplikaci pro Windows)

Diagnostické nástroje obsahují pokročilejší diagnostické nástroje, které vám pomohou prozkoumat problémy s kódem aplikace, pomalost, připojovací řetězce a další. a proaktivní nástroje, které vám pomohou zmírnit problémy s využitím procesoru, požadavky a paměti.

### <a name="proactive-cpu-monitoring"></a>Proaktivní monitorování procesoru

Proaktivní monitorování procesoru poskytuje snadný a proaktivní způsob, jak provést akci, když vaše aplikace nebo podřízený proces pro vaši aplikaci spotřebovává vysoké prostředky procesoru. Můžete nastavit vlastní pravidla prahové hodnoty procesoru dočasně zmírnit vysoký problém procesoru, dokud není nalezena skutečná příčina neočekávaného problému. Další informace naleznete [v tématu Zmírnění problémů s procesorem dříve, než k nim dojde](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktivní monitorování procesoru](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-healing a proaktivní auto-healing

Automatické ozdravování je akce zmírnění, kterou můžete provést, když vaše aplikace má neočekávané chování. Můžete nastavit vlastní pravidla na základě počtu požadavků, pomalé žádosti, limitu paměti a stavového kódu HTTP pro aktivaci akcí ke zmírnění. Pomocí nástroje dočasně zmírnit neočekávané chování, dokud nenajdete hlavní příčinu. Další informace najdete [v tématu Oznámení nového automatického léčení v diagnostice služby App Service](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatické hojení](./media/app-service-diagnostics/auto-healing-10.png)

Stejně jako proaktivní monitorování procesoru je proaktivní automatické hojení řešením na klíč, které zmírňuje neočekávané chování vaší aplikace. Proaktivní automatické ozdravování restartuje vaši aplikaci, když služba App Service zjistí, že je vaše aplikace v neopravitelném stavu. Další informace naleznete [v tématu Introducing Proactive Auto Heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigátor a analýza změn (pouze pro aplikaci pro Windows)

Ve velkém týmu s průběžnou integrací a kde vaše aplikace má mnoho závislostí, může být obtížné určit konkrétní změnu, která způsobuje nezdravé chování. Navigátor pomáhá získat přehled o topologii vaší aplikace tím, že automaticky vykresluje mapu závislostí vaší aplikace a všechny prostředky ve stejném předplatném. Navigátor umožňuje zobrazit konsolidovaný seznam změn provedených vaší aplikací a její závislosti a zúžit na změnu, která způsobuje nezdravé chování. Je přístupný prostřednictvím domovské stránky dlaždice **Navigator** a musí být povolena před použitím poprvé. Další informace najdete [v tématu Získání přehledu o závislostech aplikace pomocí navigátoru](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Výchozí stránka navigátoru](./media/app-service-diagnostics/navigator-default-page-11.png)

![Rozdílové zobrazení](./media/app-service-diagnostics/diff-view-12.png)

Ke změně pro změny v aplikaci lze přistupovat prostřednictvím klávesových **zkratek, změn aplikací** a selhání **aplikací** v **dostupnosti a výkonu,** takže ji můžete používat souběžně s jinými metrikami. Před použitím funkce je nutné ji nejprve povolit. Další informace najdete [v tématu Oznámení nového prostředí analýzy změn v diagnostice služby App Service](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Zveřejněte své dotazy nebo zpětnou vazbu na [uservoice](https://feedback.azure.com/forums/169385-web-apps) přidáním "[Diag]" v názvu.
