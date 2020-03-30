---
title: Použití podokna nastavení profileru přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Viz Stav profileru a zahájení profilování relací
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671626"
---
# <a name="configure-application-insights-profiler"></a>Konfigurace profileru přehledů aplikací

## <a name="updated-profiler-agent"></a>Aktualizovaný agent profileru
Funkce aktivační události fungují pouze s verzí 2.6 nebo novější agenta profileru. Pokud používáte službu Azure App Service, váš agent se automaticky aktualizuje. Můžete zjistit, jakou verzi agenta používáte, pokud přejdete na adresu URL Kudu pro váš web https://yourwebsite.scm.azurewebsites.net/diagnosticservicesa připojíte \DiagnosticServices na konec, například takto: . Webová úloha Profiler application insights by měla být verze 2.6 nebo novější. Upgrade můžete vynutit restartováním webové aplikace. 

Pokud používáte profiler na virtuálním počítači nebo cloudové službě, musíte mít nainstalovanou rozšíření 16.0.4 (Windows Azure Diagnostics) (WAD) nebo novější. Verzi protokolu WAD můžete zkontrolovat tak, že se přihlásíte k virtuálnímu počítači a podíváte se do tohoto adresáře: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Název adresáře je nainstalovaná verze serveru WAD. Agent virtuálního počítače Azure aktualizuje wad automaticky, když jsou k dispozici nové verze.

## <a name="profiler-settings-page"></a>Stránka nastavení profileru

Pokud chcete otevřít podokno nastavení profileru Přehledů aplikací Azure, přejděte do podokna Výkon přehledů aplikací a vyberte tlačítko **Konfigurovat profiler.**

![Odkaz na stránku nastavení profileru][configure-profiler-entry]

Tím se otevře stránka, která vypadá takto:

![Stránka nastavení profileru][configure-profiler-page]

Stránka **Konfigurovat profilování přehledů aplikací** obsahuje tyto funkce:

| | |
|-|-|
Profil nyní | Spustí profilování relací pro všechny aplikace, které jsou propojeny s touto instancí Application Insights.
Aktivační události | Umožňuje konfigurovat aktivační události, které způsobí spuštění profileru. 
Nedávné profilování relací | Zobrazí informace o minulých relacích profilování.

## <a name="profile-now"></a>Profil nyní
Tato možnost umožňuje spustit relaci profilování na vyžádání. Po kliknutí na tento odkaz začnou všichni agenti profileru, kteří odesílají data do této instance Application Insights, zachycovat profil. Po 5 až 10 minutách se relace profilu zobrazí v seznamu níže.

Pro uživatele ručně aktivovat relaci profileru, které vyžadují minimálně "zápis" přístup na jejich roli pro součást Application Insights. Ve většině případů získáte tento přístup automaticky a není potřeba žádná další práce. Pokud máte problémy, role oboru předplatného přidat by role "Application Insights komponenty přispěvatele" role. [Další informace o řízení přístupu k rolím pomocí Azure Monitoring](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

## <a name="trigger-settings"></a>Nastavení aktivační události
![Informační rámeček Nastavení aktivační události][trigger-settings-flyout]

Kliknutím na tlačítko Aktivační události na řádku nabídek se otevře pole nastavení aktivační události. Můžete nastavit aktivační událost pro spuštění profilování, když procento využití procesoru nebo paměti dosáhne nastavené úrovně.

| | |
|-|-|
Tlačítko Zapnutí/vypnutí | Zapnuto: profiler lze spustit touto aktivační událostí; Vypnuto: profiler nebude spuštěn touto aktivační událostí.
Prahová hodnota paměti | Pokud je toto procento paměti používáno, spustí se profiler.
Doba trvání | Nastaví dobu, po kterou bude profiler spuštěn při spuštění.
Cooldown | Nastaví dobu, po kterou bude profiler čekat, než znovu zkontroluje využití paměti nebo procesoru po jeho spuštění.

## <a name="recent-profiling-sessions"></a>Nedávné relace profilování
V této části stránky jsou uvedeny informace o nedávných relacích profilování. Profilování relace představuje období, kdy agent profiler byl s profilem na jednom z počítačů hostujících vaši aplikaci. Profily z relace můžete otevřít kliknutím na jeden z řádků. Pro každou relaci ukazujeme:

| | |
|-|-|
Spuštěno | Jak byla relace spuštěna, buď aktivační událostí, profilem nebo výchozím vzorkováním. 
Název aplikace | Název aplikace, která byla profilována.
Instance počítače | Název počítače, na který agent profileru běžel.
Časové razítko | Čas, kdy byl profil zachycen.
Stopa | Počet trasování, které byly připojeny k jednotlivým požadavkům.
% využití procesoru | Procento procesoru, který byl používán v době, kdy byl spuštěn profiler.
Paměť % | Procento paměti, která byla používána v době, kdy byl spuštěn profiler.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>Generování provozu do aplikace pomocí testů výkonu webu

Profileru můžete spustit ručně jediným kliknutím. Předpokládejme, že používáte test výkonu webu. Budete potřebovat trasování, které vám pomohou pochopit, jak vaše webová aplikace běží při zatížení. Mít kontrolu nad tím, kdy jsou zachyceny trasování, je zásadní, protože víte, kdy bude spuštěn zátěžový test. Ale náhodný interval vzorkování by to mohl chybět.

Následující části ilustrují, jak tento scénář funguje:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1: Generování návštěvnosti webové aplikace spuštěním testu výkonu webu

Pokud vaše webová aplikace již má příchozí provoz nebo chcete pouze ručně generovat provoz, přeskočte tuto část a pokračujte krokem 2.

1. Na portálu Application Insights vyberte **Konfigurovat** > **testování výkonu**. 

1. Chcete-li spustit nový test výkonu, vyberte tlačítko **Nový.**

   ![vytvořit nový test výkonu][create-performance-test]

1. V podokně **Nové testování výkonu** nakonfigurujte cílovou adresu URL testu. Přijměte všechna výchozí nastavení a pak vyberte **Spustit test** a spusťte zátěžový test.

    ![Konfigurace zátěžového testu][configure-performance-test]

    Nový test je zařazen do fronty jako první, následovaný stavem *probíhá*.

    ![Zátěžový test je odeslán a zařazen do fronty.][load-test-queued]

    ![Probíhá zátěžový test.][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2: Zahájení relace profileru na vyžádání

1. Když je spuštěn zátěžový test, spusťte Profiler pro zachycení trasování ve webové aplikaci, zatímco přijímá zatížení.

1. Přejděte do podokna **Konfigurovat profiler.**


### <a name="step-3-view-traces"></a>Krok 3: Zobrazení trasování

Po dokončení spuštění profileru postupujte podle pokynů k oznámení a přejděte do podokna Výkon a zobrazte trasování.

## <a name="next-steps"></a>Další kroky
[Povolení profileru a trasování zobrazení](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

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
