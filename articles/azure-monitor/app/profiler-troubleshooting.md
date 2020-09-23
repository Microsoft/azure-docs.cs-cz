---
title: Řešení potíží s Azure Application Insights Profiler
description: Tento článek představuje postup řešení potíží a informace, které vývojářům umožňují problémy s povolením nebo používáním Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d9acd322c454002613e21e8591c3e83aeec2d51e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979388"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Řešení potíží s povolením nebo zobrazením Application Insights Profiler

> [!CAUTION]
> Při spuštění profileru pro ASP.NET Core aplikací na Azure App Service došlo k chybě. Máme opravu, ale pro nasazení celého světa bude trvat několik týdnů. Tuto chybu můžete obejít tak, že do své aplikace přidáte sadu Application Insights SDK s [pokyny.](./asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Obecné řešení potíží

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profily se odesílají jenom v případě, že vaše aplikace obsahuje žádosti, když je profiler spuštěný.

Azure Application Insights Profiler shromažďuje data profilování po dobu dvou minut každou hodinu. Shromažďuje také data po výběru tlačítka **profilace nyní** v podokně **Konfigurovat Application Insights Profiler** . Data profilování se ale odesílají jenom v případě, že je možné je připojit k požadavku, ke kterému došlo během běhu profileru. 

Profiler zapisuje trasovací zprávy a vlastní události do prostředku Application Insights. Pomocí těchto událostí můžete zjistit, jak profiler běží. Pokud si myslíte, že Profiler by měl běžet a zachytávání trasování, ale nezobrazuje se v podokně **výkon** , můžete zjistit, jak profiler běží:

1. Vyhledejte trasovací zprávy a vlastní události odeslané profilerem do vašeho prostředku Application Insights. Pomocí tohoto vyhledávacího řetězce můžete najít relevantní data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Následující obrázek ukazuje dva příklady hledání ze dvou prostředků AI: 
    
   * Na levé straně aplikace nepřijímá požadavky, pokud je spuštěn Profiler. Zpráva vysvětluje, že nahrávání bylo zrušeno z důvodu žádné aktivity. 

   * Na pravé straně Profiler zahájil a odeslal vlastní události, když zjistili, jaké požadavky nastaly během běhu profileru. Pokud se zobrazí vlastní událost ServiceProfilerSample, znamená to, že Profiler připojil trasování k žádosti a vy můžete zobrazit trasování v podokně **výkon Application Insights** .

     Pokud se nezobrazí žádná telemetrie, Profiler neběží. Informace o řešení problémů najdete v částech věnovaném řešení potíží pro konkrétní typ aplikace dále v tomto článku.  

     ![Prohledat telemetrii profileru][profiler-search-telemetry]

1. Pokud byly během běhu profileru nějaké žádosti, ujistěte se, že požadavky jsou zpracovávány součástí vaší aplikace, která má povolený Profiler. I když se aplikace někdy skládají z několika součástí, je profiler povolen pouze pro některé součásti. V podokně **konfigurace Application Insights Profiler** se zobrazují součásti, které odeslaly trasování.

### <a name="other-things-to-check"></a>Další věci ke kontrole
* Ujistěte se, že je vaše aplikace spuštěná na .NET Framework 4,6.
* Pokud je vaše webová aplikace ASP.NET Core aplikace, musí běžet aspoň ASP.NET Core 2,0.
* Pokud se data, která se snažíte zobrazit, starší než několik týdnů, zkuste omezit filtr času a zkusit to znovu. Trasování se odstraní po sedmi dnech.
* Ujistěte se, že proxy nebo brána firewall neblokovaly přístup k https://gateway.azureserviceprofiler.net .
* Profiler není podporován na plánech Free nebo Shared App Service. Pokud používáte některý z těchto plánů, zkuste škálovat na jeden ze základních plánů a Profiler by měl začít pracovat.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dvojité počítání v paralelních vláknech

V některých případech je celková časová metrika v prohlížeči zásobníku delší než doba trvání žádosti.

K této situaci může dojít, když jsou k žádosti přidruženy dva nebo více vláken a pracují paralelně. V takovém případě je celkový čas vlákna větší než uplynulý čas. Jedno vlákno může čekat na dokončení. Prohlížeč se pokusí zjistit tuto situaci a vynechá nevýznamové čekání. V takovém případě se synchronizací na straně zobrazení příliš velkého množství informací, nikoli z toho, co by mohlo být důležité informace.

Když vidíte paralelní vlákna ve svých trasováních, určete, která vlákna čekají, abyste mohli zjistit kritickou cestu pro požadavek. Vlákno, které rychle směřuje do stavu čekání, obvykle čeká na ostatní vlákna. Soustřeďte se na ostatní vlákna a ignorujte čas ve čekajících vláknech.

### <a name="error-report-in-the-profile-viewer"></a>Zpráva o chybách v prohlížeči profilů
Odešlete lístek podpory na portálu. Nezapomeňte do chybové zprávy zahrnout ID korelace.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Řešení potíží s profilerem v Azure App Service
Pro správné fungování profileru postupujte takto:
* Váš plán služby Web App Service musí být na úrovni Basic nebo vyšší.
* Vaše webová aplikace musí mít povolený Application Insights.
* Vaše webová aplikace musí mít následující nastavení aplikace:

    |Nastavení aplikace    | Hodnota    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro prostředek Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |


* Webová úloha **ApplicationInsightsProfiler3** musí být spuštěná. Postup kontroly webové úlohy:
   1. Přejít na [Kudu](/archive/blogs/cdndevs/the-kudu-debug-console-azure-websites-best-kept-secret).
   1. V nabídce **nástroje** vyberte možnost **řídicí panel WebJobs**.  
      Otevře se podokno webové **úlohy** . 
   
      ![Snímek obrazovky zobrazuje podokno webové úlohy, ve kterém se zobrazuje název, stav a čas posledního spuštění úloh.][profiler-webjob]   
   
   1. Chcete-li zobrazit podrobnosti webové úlohy, včetně protokolu, vyberte odkaz **ApplicationInsightsProfiler3** .  
     Otevře se podokno **Podrobnosti nepřetržité úlohy WebJob** .

      ![Snímek obrazovky se zobrazí v podokně podrobností nepřetržité úlohy WebJob.][profiler-webjob-log]

Pokud nemůžete zjistit, proč Profiler nefunguje za vás, můžete si ho stáhnout a poslat mu v našem týmu, kde získáte pomoc serviceprofilerhelp@microsoft.com . 
    
### <a name="manual-installation"></a>Ruční instalace

Při konfiguraci profileru se aktualizace provedou v nastavení webové aplikace. Pokud to vaše prostředí vyžaduje, můžete aktualizace nainstalovat ručně. Příkladem může být, že aplikace běží v prostředí Web Apps pro PowerApps. Ruční použití aktualizací:

1. V podokně **ovládací prvek webové aplikace** otevřete položku **Nastavení**.

1. Nastavte **.NET Framework verze na verzi** **4.6**.

1. Nastavte **vždycky zapnuto** na **zapnuto**.
1. Vytvořit tato nastavení aplikace:

    |Nastavení aplikace    | Hodnota    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro prostředek Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~ 3 |

### <a name="too-many-active-profiling-sessions"></a>Příliš mnoho aktivních relací profilování

V současné době můžete profiler povolit na maximálně čtyři webové aplikace Azure a sloty nasazení, které běží ve stejném plánu služeb. Pokud máte více než čtyři webové aplikace spuštěné v jednom plánu služby App Service, může Profiler vyvolat *Microsoft. ServiceProfiler. Exceptions. TooManyETWSessionException*. Profiler se spouští samostatně pro každou webovou aplikaci a pokusy o spuštění relace trasování událostí pro Windows (ETW) pro každou aplikaci. V jednom okamžiku může být aktivní jenom omezený počet relací ETW. Pokud webová úloha profileru hlásí příliš mnoho aktivních relací profilování, přesuňte některé webové aplikace na jiný plán služby.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Chyba nasazení: adresář není prázdný. d: \\ Domovská stránka \\ \\ wwwroot \\ App_Data \\ úlohy

Pokud znovu nasazujete webovou aplikaci do prostředku Web Apps s povoleným profilerem, může se zobrazit následující zpráva:

*Adresář není prázdný. d: \\ Domovská stránka \\ \\ wwwroot \\ App_Data \\ úlohy*

K této chybě dojde, pokud spouštíte Nasazení webu ze skriptů nebo z kanálu nasazení Azure DevOps. Řešením je přidání následujících dalších parametrů nasazení do úlohy Nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstraní složku, kterou používá Application Insights Profiler a odblokuje proces opětovného nasazení. Neovlivňují aktuálně spuštěnou instanci profileru.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Návody určit, jestli je Application Insights Profiler spuštěná?

Profiler běží jako Průběžná webová úloha ve webové aplikaci. Prostředek webové aplikace můžete otevřít v [Azure Portal](https://portal.azure.com). V podokně **WebJobs** (webové úlohy) se podívejte na stav **ApplicationInsightsProfiler**. Pokud není spuštěný, otevřete **protokoly** a získejte další informace.

## <a name="troubleshoot-vms-and-cloud-services"></a>Řešení potíží s virtuálními počítači a Cloud Services

>**Byla opravena chyba v profileru, která se dodává v WAD pro Cloud Services.** Nejnovější verze WAD (1.12.2.0) pro Cloud Services funguje se všemi nejnovějšími verzemi sady App Insights SDK. Hostitelé cloudové služby budou upgradovat WAD automaticky, ale nejsou okamžité. K vynucení upgradu můžete znovu nasadit službu nebo restartovat uzel.

Pokud chcete zjistit, jestli je profiler správně nakonfigurovaný pomocí Azure Diagnostics, udělejte následující tři věci: 
1. Nejprve zkontrolujte, zda obsah Azure Diagnostics konfigurace, která byla nasazena, je to, co očekáváte. 

1. Potom se ujistěte, že Azure Diagnostics předá do příkazového řádku profileru správné iKey. 

1. Třetí, zkontrolujte soubor protokolu profileru a zjistěte, jestli Profiler běžel, ale vrátil chybu. 

Chcete-li kontrolovat nastavení, která byla použita pro konfiguraci Azure Diagnostics:

1. Přihlaste se k virtuálnímu počítači a otevřete soubor protokolu v tomto umístění. Verze modulu plug-in na vašem počítači může být novější.
    
    Pro virtuální počítače:
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```
    
    Pro Cloud Services:
    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```

1. V souboru můžete vyhledat řetězec **WadCfg** a vyhledat tak nastavení, která byla PŘEdána virtuálnímu počítači ke konfiguraci Azure Diagnostics. Můžete zjistit, zda je iKey používaný jímkou profileru správný.

1. Podívejte se do příkazového řádku, který se používá ke spuštění profileru. Argumenty, které se používají ke spuštění profileru, jsou v následujícím souboru. (Jednotka může být c: nebo d: a adresář může být skrytý.)

    Pro virtuální počítače:
    ```
    C:\ProgramData\ApplicationInsightsProfiler\config.json
    ```
    
    pro Cloud Services:
    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ujistěte se, že je iKey na příkazovém řádku profileru správný. 

1. Pomocí cesty, která se nachází v předchozí *config.js* souboru, vyhledejte soubor protokolu profileru s názvem **BootstrapN. log**. Zobrazí informace o ladění, které určují nastavení používané profilerem. Zobrazuje taky stavové a chybové zprávy z profileru.  

    V případě virtuálních počítačů je tento soubor obvykle:
    ```
    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Pro Cloud Services:
    ```
    C:\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.17.0.6\ApplicationInsightsProfiler
    ```

    Pokud je v době, kdy aplikace přijímá požadavky, spuštěn Profiler, zobrazí se následující zpráva: *aktivita byla zjištěna z Ikey*. 

    Při nahrávání trasování se zobrazí následující zpráva: *Spustit trasování*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Upravit síťová proxy nebo pravidla brány firewall

Pokud se vaše aplikace připojuje k Internetu prostřednictvím proxy serveru nebo brány firewall, možná budete muset upravit pravidla, aby aplikace mohla komunikovat se službou Application Insights Profiler. IP adresy, které používá Application Insights Profiler, jsou součástí značky služby Azure Monitor.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png
