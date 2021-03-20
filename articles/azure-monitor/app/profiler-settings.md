---
title: Použití podokna nastavení Azure Application Insights Profiler | Microsoft Docs
description: Viz téma stav profileru a spuštění relací profilace.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499405"
---
# <a name="configure-application-insights-profiler"></a>Konfigurace Application Insights Profiler

## <a name="updated-profiler-agent"></a>Aktualizovaný agent profileru
Funkce triggeru fungují jenom s verzí 2,6 nebo novějším agenta profileru. Pokud používáte Azure App Service, Agent se automaticky aktualizuje. Můžete si prohlédnout, jakou verzi agenta používáte, pokud přejdete na adresu URL Kudu webu a připojíte \DiagnosticServices na konec IT, například:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . Webová úloha Application Insights Profiler by měla verze 2,6 nebo novější. Upgrade můžete vynutit restartováním webové aplikace. 

Pokud používáte Profiler na virtuálním počítači nebo cloudové službě, musíte mít nainstalovanou příponu Windows Azure Diagnostics (WAD) verze 16.0.4 nebo novější. Verzi WAD můžete ověřit tak, že se přihlásíte k VIRTUÁLNÍmu počítači a vyhledáte tento adresář: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4.. Název adresáře je verze WAD, která je nainstalována. Agent virtuálního počítače Azure aktualizuje WAD automaticky, když jsou dostupné nové verze.

## <a name="profiler-settings-page"></a>Stránka nastavení profileru

Chcete-li otevřít podokno nastavení Azure Application Insights Profiler, otevřete podokno výkon Application Insights a pak klikněte na tlačítko **Konfigurovat Profiler** .

![Odkaz na stránku otevřít nastavení profileru][configure-profiler-entry]

Tím se otevře stránka, která vypadá takto:

![Stránka nastavení profileru][configure-profiler-page]

Stránka **konfigurace Application Insights Profiler** obsahuje tyto funkce:

| Funkce | Popis |
|-|-|
Profilovat teď | Spustí relace profilování pro všechny aplikace, které jsou propojené s touto instancí Application Insights.
Aktivační události | Umožňuje konfigurovat triggery, které způsobují, že se Profiler spustí. 
Nedávné relace profilování | Zobrazí informace o minulých relacích profilace.

## <a name="profile-now"></a>Profilovat teď
Tato možnost umožňuje spustit relaci profilování na vyžádání. Po kliknutí na tento odkaz začnou všichni agenti profileru, kteří odesílají data do této instance Application Insights, zachytí profil. Po 5 až 10 minutách se relace profilu zobrazí v následujícím seznamu.

Aby mohl uživatel ručně aktivovat relaci profileru, musí pro Application Insights komponentu vyžadovat minimální přístup "Write" na jejich roli. Ve většině případů tento přístup získáte automaticky a nebudete potřebovat žádnou další práci. Pokud máte problémy, obor předplatného, který se má přidat, by byl rolí "Application Insights Přispěvatel komponent". [Přečtěte si další informace o řízení přístupu k rolím pomocí monitorování Azure](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Nastavení aktivační události
![Informační rámeček nastavení triggeru][trigger-settings-flyout]

Kliknutím na tlačítko triggery na řádku nabídek otevřete okno nastavení triggeru. Můžete nastavit aktivační událost pro spuštění profilování, když procento využití procesoru nebo paměti narazí na úroveň, kterou jste nastavili.

| Nastavení | Popis |
|-|-|
Tlačítko pro zapnutí/vypnutí | Zapnuto: profiler může spustit Tato aktivační událost; Vypnuto: Profiler nespustí Tato aktivační událost.
Prahová hodnota paměti | V případě, že se toto procento paměti používá, bude spuštěn Profiler.
Doba trvání | Nastavuje dobu, po kterou se Profiler spustí, když se aktivuje.
Cooldown | Nastaví dobu, po kterou Profiler počká, než bude znovu kontrolovat paměť nebo využití procesoru po jeho aktivaci.

## <a name="recent-profiling-sessions"></a>Nedávné relace profilování
Tato část stránky zobrazuje informace o nejnovějších relacích profilování. Relace profilování představuje časový interval, po který agent profileru přebírá profil na jednom z počítačů, které hostují vaši aplikaci. Můžete otevřít profily z relace kliknutím na jeden z řádků. Pro každou relaci se zobrazuje:

| Nastavení | Popis |
|-|-|
Aktivoval | Jak byla relace spuštěna, buď triggerem, profilem nyní, nebo s výchozím vzorkováním. 
Název aplikace | Název aplikace, která byla profilace.
Instance počítače | Název počítače, ve kterém byl spuštěn Agent profileru.
Timestamp | Čas, kdy byl profil zachycen.
Trasovat | Počet trasování, které byly připojeny k jednotlivým požadavkům.
CPU % | Procento využití procesoru, které bylo používáno během běhu profileru.
Rezident | Procento paměti, která se použila během běhu profileru.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Použití testů výkonnosti webu k vygenerování provozu do vaší aplikace

Profiler můžete aktivovat ručně jediným kliknutím. Předpokládejme, že používáte test výkonnosti webu. Budete potřebovat trasování, které vám pomůžou pochopit, jak je webová aplikace spuštěná při zatížení. Kontrola nad tím, kdy jsou trasování zaznamenávána, je zásadní, protože víte, kdy bude zátěžový test spuštěn. Ale interval náhodného vzorkování může být neúspěšný.

Následující části znázorňují, jak tento scénář funguje:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1: vygenerujte provoz do webové aplikace spuštěním testu výkonnosti webu

Pokud vaše webová aplikace již má příchozí provoz nebo pokud chcete pouze vygenerovat provoz, přeskočte tuto část a pokračujte krokem 2.

1. Na portálu Application Insights vyberte **Konfigurovat**  >  **testování výkonu**. 

1. Chcete-li spustit nový test výkonnosti, vyberte tlačítko **Nový** .

   ![vytvořit nový test výkonnosti][create-performance-test]

1. V podokně **Nový test výkonu** nakonfigurujte adresu URL cíle testu. Přijměte všechna výchozí nastavení a potom vyberte **Spustit test** , aby se spustil zátěžový test.

    ![Konfigurovat zátěžový test][configure-performance-test]

    Nejprve se nový test zařadí do fronty následovaný stavem *probíhá*.

    ![Zátěžový test se odešle a zařadí do fronty.][load-test-queued]

    ![Zátěžový test běží v průběhu][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2: spuštění relace na vyžádání profileru

1. Když je spuštěn zátěžový test, spusťte profiler pro zachycení trasování webové aplikace během jejího přijetí.

1. Přejít do podokna **Konfigurace profileru** .


### <a name="step-3-view-traces"></a>Krok 3: zobrazení trasování

Po dokončení profileru postupujte podle pokynů v části oznámení a přejděte do podokna výkon a zobrazte trasování.

## <a name="next-steps"></a>Další kroky
[Povolení profileru a zobrazení trasování](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
