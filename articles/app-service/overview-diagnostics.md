---
title: Přehled diagnostiky Azure App Service | Microsoft Docs
description: Přečtěte si, jak můžete řešit problémy s vaší aplikací pomocí App Service diagnostiky.
keywords: App Service, Azure App Service, Diagnostics, podpora, Webová aplikace, řešení potíží, samoobslužná pomoc
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 2919c7a2fa3a8912de0272af5131583988184704
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755517"
---
# <a name="azure-app-service-diagnostics-overview"></a>Přehled diagnostiky Azure App Service

Pokud používáte webovou aplikaci, chcete být připravená na všechny problémy, které mohou nastat, od 500 chyb uživatelům, kteří vás sdělují, že váš web je mimo provoz. Diagnostika App Service je inteligentní a interaktivní prostředí, které vám může pomoct při odstraňování potíží s aplikací bez nutnosti konfigurace. Když narazíte na problémy s vaší aplikací, App Service Diagnostika ukazuje, co je špatné, aby vás provedla tyto správné informace pro snazší a rychlé řešení potíží a vyřešení problému.

I když je toto prostředí nejužitečnější, když máte během posledních 24 hodin problémy s vaší aplikací, jsou všechny diagnostické grafy k dispozici vždy, když je budete chtít analyzovat.

App Service Diagnostika funguje nejen pro vaši aplikaci ve Windows, ale také pro aplikace na [platformě Linux/kontejnery](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)a [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Otevřít diagnostiku App Service

Pokud chcete získat přístup k diagnostice App Service, přejděte v [Azure Portal](https://portal.azure.com)do webové aplikace App Service nebo App Service Environment. V levém navigačním panelu klikněte na **diagnostikovat a řešte problémy**.

Pro Azure Functions přejděte do aplikace Function App a v horním navigačním panelu klikněte na **funkce platformy**a v části **Správa prostředků** vyberte **diagnostikovat a vyřešit problémy** .

Na domovské stránce diagnostiky App Service můžete zvolit kategorii, která nejlépe popisuje problém s vaší aplikací pomocí klíčových slov na každé dlaždici domovské stránky. Na této stránce můžete také najít **diagnostické nástroje** pro aplikace pro Windows. Viz [diagnostické nástroje (jenom pro aplikaci pro Windows)](#diagnostic-tools-only-for-windows-app).

![Domovské stránky](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktivní rozhraní

Jakmile vyberete kategorii domovské stránky, která nejlépe odpovídá problému vaší aplikace, App Service interaktivní rozhraní Genie Diagnostics, které vám pomůže s diagnostikou a řešením problémů s vaší aplikací. Pomocí klávesových zkratek, které poskytuje Genie, můžete zobrazit úplnou diagnostickou sestavu kategorie problému, kterou vás zajímá. Zástupci dlaždic poskytují přímý způsob přístupu k diagnostickým metrikám.

![Zástupci dlaždic](./media/app-service-diagnostics/tile-shortcuts-2.png)

Po kliknutí na tyto dlaždice uvidíte seznam témat souvisejících s problémem popsaným na dlaždici. Tato témata poskytují fragmenty důležitých informací z celé sestavy. Můžete kliknout na kterékoli z těchto témat a prozkoumat tak problémy. Můžete také kliknout na **Zobrazit úplnou sestavu** a prozkoumat všechna témata na jedné stránce.

![Témata](./media/app-service-diagnostics/application-logs-insights-3.png)

![Zobrazit úplnou sestavu](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnostická sestava

Po výběru možnosti prozkoumat problém kliknutím na téma můžete zobrazit další podrobnosti o tématu často doplněné pomocí grafů a markdowns. Diagnostická zpráva může být výkonným nástrojem pro určení problému s vaší aplikací.

![Diagnostická sestava](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Stavový Checkup

Pokud si nejste jisti, co je u vaší aplikace chybné, nebo Pokud nevíte, kde začít řešit problémy, je dobrým místem, kde se spouští řešení potíží. Health Checkup analyzuje vaše aplikace a poskytne vám rychlý a interaktivní přehled, který ukazuje, co je v pořádku a co je špatně, a oznamuje vám, kde můžete problém prozkoumat. Inteligentní a interaktivní rozhraní vám poskytne pokyny k procesu řešení potíží. Nástroj Health Checkup je integrovaný s prostředím Genie pro aplikace pro Windows a webovou aplikací o diagnostickou sestavu pro aplikace pro Linux.

### <a name="health-checkup-graphs"></a>Grafy Health Checkup

V nástroji Health Checkup existují čtyři různé grafy.

- **požadavky a chyby:** Graf, který zobrazuje počet požadavků provedených za posledních 24 hodin spolu s chybami serveru HTTP.
- **výkon aplikace:** Graf, který zobrazuje dobu odezvy za posledních 24 hodin pro různé skupiny percentilu.
- **Využití CPU:** Graf, který zobrazuje celkové procento využití procesoru na instanci za posledních 24 hodin.  
- **využití paměti:** Graf, který zobrazuje celkové procento využití fyzické paměti na instanci za posledních 24 hodin.

![Stavový Checkup](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Prozkoumat problémy s kódem aplikace (jenom pro aplikaci pro Windows)

Vzhledem k tomu, že mnoho problémů s aplikacemi souvisí s problémy v kódu aplikace, App Service Diagnostika je integrována s [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) , aby vybrala výjimky a problémy závislostí s vybranými výpadky. Application Insights musí být povolená samostatně.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Pokud chcete zobrazit Application Insights výjimky a závislosti, vyberte možnost **Webová aplikace** v podobě **pomalých** klávesových zkratek aplikace nebo webové aplikace.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Postup řešení potíží (jenom pro aplikaci pro Windows)

Pokud se v posledních 24 hodinách zjištěn problém s konkrétní kategorií problému, můžete zobrazit úplnou diagnostickou sestavu a diagnostika App Service vás může zobrazit další informace o řešení potíží a dalších krocích pro další průvodce.

![Application Insights a řešení potíží a další kroky](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnostické nástroje (jenom pro aplikaci pro Windows)

Diagnostické nástroje obsahují pokročilejší diagnostické nástroje, které vám pomůžou prozkoumat problémy s kódem aplikace, zpomalení, připojovací řetězce a další. a proaktivní nástroje, které pomáhají zmírnit problémy s využitím procesoru, požadavky a paměti.

### <a name="proactive-cpu-monitoring"></a>Proaktivní monitorování CPU

Proaktivní monitorování procesoru poskytuje snadný a aktivní způsob, jak provést akci, když vaše aplikace nebo podřízený proces vaší aplikace spotřebovává vysoké prostředky procesoru. Můžete nastavit vlastní mezní hodnoty CPU, abyste dočasně zmírnili vysoký problém s PROCESORem, dokud nezjistíte skutečnou příčinu neočekávaného problému. Další informace najdete v tématu [zmírnění problémů s procesorem před tím, než k nim dojde](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktivní monitorování CPU](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Automatické opravy a proaktivní automatické opravy

Automatické opravy je zmírňující opatření, které můžete provést v případě neočekávaného chování vaší aplikace. Můžete nastavit vlastní pravidla na základě počtu požadavků, pomalých požadavků, limitů paměti a stavového kódu HTTP a aktivovat tak zmírňující akce. Pomocí tohoto nástroje můžete dočasně zmírnit neočekávané chování, dokud nenajdete hlavní příčinu. Další informace najdete v tématu představení [nového možnosti automatického retušování v tématu Diagnostika služby App Service](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Automatické opravení](./media/app-service-diagnostics/auto-healing-10.png)

Stejně jako proaktivní monitorování procesoru je proaktivní automatické opravy pro zmírnění neočekávaného chování vaší aplikace. Proaktivní automatické retušování aplikace restartuje, když App Service zjistí, že je vaše aplikace v neobnovitelné stavu. Další informace najdete v tématu [představení proaktivní automatických](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)oprav.

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigátor a změna analýz (jenom pro aplikace pro Windows)

Ve velkém týmu se kontinuální integrací a v případě, kde má vaše aplikace mnoho závislostí, může být obtížné určit konkrétní změnu, která způsobí špatné chování. Navigátor pomáhá získat přehled o topologii vaší aplikace tím, že automaticky vykreslovat mapu závislostí vaší aplikace a všech prostředků ve stejném předplatném. Navigátor vám umožní zobrazit konsolidovaný seznam změn provedených vaší aplikací a jejími závislostmi a zúžit se na změnu, která způsobuje chybné chování. Lze k němu přistupovat prostřednictvím **navigátoru** dlaždic domovské stránky a musí být povolen před prvním použitím. Další informace najdete v tématu [získání viditelnosti závislostí vaší aplikace pomocí Navigátoru](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html).

![Výchozí stránka navigátor](./media/app-service-diagnostics/navigator-default-page-11.png)

![Zobrazení rozdílů](./media/app-service-diagnostics/diff-view-12.png)

Změnou analýzy pro změny aplikací lze získat pøístup prostřednictvím zástupců dlaždic, **změn aplikací** a **chyb aplikací** v **dostupnosti a výkonu** , abyste je mohli používat souběžně s jinými metrikami. Před použitím této funkce je nutné ji nejprve povolit. Další informace najdete v tématu [oznamujeme nové možnosti analýzy změn v diagnostice App Service](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

Vystavte své dotazy nebo připomínky na webu [UserVoice](https://feedback.azure.com/forums/169385-web-apps) přidáním "[diag]" v názvu.
