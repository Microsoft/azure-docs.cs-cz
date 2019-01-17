---
title: Poradce při potížích s Azure Application Insights Profiler | Dokumentace Microsoftu
description: Tento článek představuje řešení problémů s kroky a informace, které pomůžou vývojářům, kteří jsou potíže s povolením nebo pomocí Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 404fe07b5e3c1f4322ec683f37b08942abfd6fcc
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358843"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Poradce při potížích, povolení nebo zobrazení Application Insights Profiler

## <a id="troubleshooting"></a>Obecné řešení potíží

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profily jsou odeslány pouze v případě, že je spuštěn Profiler, existují požadavky na aplikace

Azure Application Insights Profiler shromažďuje data profilace pro dvě minuty každou hodinu. Taky shromažďuje data při výběru **profilu nyní** tlačítko **nakonfigurovat Application Insights Profiler** podokně. Ale jenom v případě, že může být připojen k žádosti, která se stalo při běhu Profiler nahrání dat profilování. 

Profiler zapisuje zprávy trasování a vlastní události do prostředku Application Insights. Tyto události můžete vidět, jak je Profiler běží. Pokud si myslíte, že Profiler by měl být spuštěn a zachytávání trasování, ale nejsou zobrazeny v **výkonu** podokně můžete zkontrolovat, naleznete v tématu Jak Profiler běží:

1. Hledání zpráv trasování a vlastní události pro prostředek Application Insights odesílá jako Profiler. Tento řetězec hledání můžete najít relevantní data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Následující obrázek ukazuje dva příklady vyhledávání ze dvou zdrojů AI: 
    
    * Na levé straně aplikace nepřijímá požadavky, je spuštěn Profiler. Zpráva vysvětluje, že nahrávání byla zrušena z důvodu žádná aktivita. 

    * Na pravé straně Profiler spuštění a odeslání vlastních událostí při zjištění požadavků, které se stalo při běhu Profiler. Pokud se zobrazí ServiceProfilerSample vlastní událost, znamená to, že Profiler připojen trasování na žádost a trasování můžete zobrazit **Application Insights výkon** podokně.

    Pokud se zobrazí žádnou telemetrii, není spuštěn Profiler. Řešení potíží, najdete v části řešení potíží pro váš typ konkrétní aplikace později v tomto článku.  

     ![Hledat telemetrii Profiler][profiler-search-telemetry]

1. Pokud byly nějaké požadavky, zatímco Profiler spustili, ujistěte se, že součást aplikace, který má povolené Profiler zpracovává požadavky. I když aplikace v některých případech se skládají z několika součástí, Profiler je povoleno pouze pro některé součásti. **Nakonfigurovat Application Insights Profiler** podokno zobrazuje součásti, které jste nahráli trasování.

### <a name="other-things-to-check"></a>Další věci ke kontrole
* Ujistěte se, že vaše aplikace běží na rozhraní .NET Framework 4.6.
* Když je vaše aplikace webové aplikace ASP.NET Core, musí používat minimálně ASP.NET Core 2.0.
* Pokud jsou data, které se snažíte zobrazit starší než několik týdnů, zkuste omezení časový filtr a zkuste to znovu. Trasování se odstraní po sedm dní.
* Ujistěte se, že proxy nebo brány firewall nejsou zablokovaný přístup k https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Double, počítací v paralelních vláken

V některých případech se celková doba metriky v prohlížeči zásobníku je větší než doba trvání žádosti.

Tato situace může nastat, když dva nebo více vláken jsou spojené se žádostí a jsou prováděny současně. V takovém případě čas celkový počet vláken je větší než uplynulý čas. Jedno vlákno může čekat na dokončení druhé. V prohlížeči se pokusí tuto situaci detekovat a vynechá nezajímavé čekání. Přitom chybuje Toolbar zobrazení příliš mnoho informací o spíše vynechat, které by mohly důležitých informací.

Až se v trasování paralelních vláken, určete vlákna, které čekají na přiřazení tak, aby vám zjistit kritickou cestu pro žádost. Obvykle vlákna, která se rychle přejde do stavu čekání jednoduše čekání na jiných vláken. Soustředit se na ostatní vlákna a ignorovat časové čekajících vláken.

### <a name="error-report-in-the-profile-viewer"></a>Zprávu o chybách v prohlížeči profilu
Vyplňte lístek podpory na portálu. Nezapomeňte zahrnout ID korelace z chybové zprávy.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Řešení potíží s Profiler ve službě Azure App Service
Pro Profiler fungovala správně:
* Plán služby app service web musí být na úrovni Basic nebo vyšší.
* Webové aplikace musí mít povolené Application Insights.
* Webové aplikace musí mít **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení aplikace, které jsou nakonfigurované se stejným klíčem instrumentace, který používá sadu SDK Application Insights.
* Webové aplikace musí mít **APPINSIGHTS_PROFILERFEATURE_VERSION** nastavení aplikace, které definované a nastavená na 1.0.0.
* Webové aplikace musí mít **DiagnosticServices_EXTENSION_VERSION** nastavení aplikace, které jsou definovány a hodnotu nastavte na přibližně 3.
* **ApplicationInsightsProfiler3** webové úlohy musí být spuštěna. Chcete-li zkontrolovat webové úlohy:
   1. Přejděte na [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. V **nástroje** nabídce vyberte možnost **řídicím panelu webjobs nechat**.  
      **WebJobs** se otevře podokno. 
   
      ![profiler-webjob]   
   
   1. Chcete-li zobrazit podrobnosti o webové úlohy, včetně protokolu, vyberte **ApplicationInsightsProfiler2** odkaz.  
     **Průběžné webové úlohy podrobnosti** se otevře podokno.

      ![profiler-webjob-log]

Pokud nelze zjistit, proč se Profiler nefunguje pro vás, můžete stáhnout a odeslat na náš tým pro pomoc. 
    
### <a name="manual-installation"></a>Ruční instalace

Když konfigurujete Profiler, jsou provedeny aktualizace nastavení webové aplikace. Pokud vaše prostředí vyžaduje, můžete ručně aplikovat aktualizace. Příkladem může být, že vaše aplikace běží v prostředí s Web Apps pro PowerApps. Pro ruční instalace aktualizací, postupujte takto:

1. V **webový ovládací prvek aplikace** otevřeným podoknem **nastavení**.

1. Nastavte **rozhraní .net Framework verze** k **v4.6**.

1. Nastavte **Always On** k **na**.

1. Přidat **APPINSIGHTS_INSTRUMENTATIONKEY** aplikace nastavení a nastavte hodnotu na stejný klíč instrumentace, který se používá sada SDK.

1. Přidat **APPINSIGHTS_PROFILERFEATURE_VERSION** nastavení aplikace a nastavte hodnotu na 1.0.0.

1. Přidat **DiagnosticServices_EXTENSION_VERSION** nastavení aplikace a nastavte hodnotu na přibližně 3.

### <a name="too-many-active-profiling-sessions"></a>Příliš mnoho aktivní relace profilování

V současné době můžete povolit Profiler na maximálně čtyři Azure webové aplikace a sloty nasazení, které jsou spuštěny v rámci stejného plánu služby. Pokud máte více než čtyři webových aplikací běžících v plánu služby jednu aplikaci, Profiler může vyvolat *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler běží odděleně pro každou webovou aplikaci a pokusí se spustit relaci trasování událostí pro Windows (ETW) pro každou aplikaci. Ale omezený počet relací ETW může být aktivní v jednom okamžiku. Pokud Profiler webjob hlásí příliš mnoho aktivních relací profilování, přesuňte některé webové aplikace k plánu jiné služby.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Chyba nasazení: Adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.

Pokud jste opětovného nasazení vaší webové aplikace do prostředku webové aplikace s Profiler povolené, může se zobrazit následující zpráva:

*Adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.*

K této chybě dochází při spuštění nasazení webu pomocí skriptů nebo z kanálu nasazení Azure DevOps. Řešením je přidat následující parametry další nasazení do úlohy nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstranit složku, která používají Application Insights Profiler a odblokování procesu opětovného nasazení. Neovlivňují Profiler instanci, na kterém aktuálně běží.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak zjistím, zda je spuštěna Application Insights Profiler?

Profiler běží jako nepřetržitá webová úloha ve webové aplikaci. Můžete otevřít v prostředku webové aplikace [webu Azure portal](https://portal.azure.com). V **WebJobs** podokně zkontrolujte stav **ApplicationInsightsProfiler**. Pokud není spuštěná, Otevřít **protokoly** zobrazíte další informace.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Poradce při potížích s Profiler a diagnostice Azure

Chcete-li zjistit, zda je Profiler správně nakonfigurován pomocí Azure Diagnostics, proveďte následující tři věci: 
1. Nejprve zkontrolujte, zda jsou obsah konfigurace diagnostiky Azure, které jsou nasazeny, co očekáváte. 

1. Za druhé Ujistěte se, že Azure Diagnostics předá správné Instrumentační klíč v příkazovém řádku Profiler. 

1. Třetí zkontrolujte soubor protokolu Profiler a zjistit, zda Profiler spuštěn, ale vrátil chybu. 

Chcete-li zkontrolovat nastavení, které byly použity ke konfiguraci diagnostiky Azure:

1. Přihlaste se k virtuálnímu počítači (VM) a pak otevřete soubor protokolu v tomto umístění: 

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
    ```

1. V souboru, můžete vyhledat řetězec **WadCfg** najít nastavení, které byly předány do virtuálního počítače ke konfiguraci diagnostiky Azure. Můžete zkontrolovat, zda je správný Instrumentační klíč používaný Profiler jímky.

1. Zkontrolujte příkazový řádek, který se používá ke spuštění Profiler. Argumenty, které se používají ke spuštění Profiler jsou v následujícím souboru:

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ujistěte se, že Instrumentační klíč v příkazovém řádku Profiler je správná. 

1. Pomocí cesty nalezen v předchozím *config.json* soubor, zkontrolujte soubor protokolu Profiler. Zobrazí informace o ladění, která určuje nastavení, která používá Profiler. Také zobrazuje stav a chybové zprávy z Profiler.  

    Pokud Profiler je spuštěné, přestože aplikace přijímá požadavky, zobrazí se následující zpráva: *Detekována aktivita z Instrumentační klíč*. 

    Po nahrání trasování se zobrazí následující zpráva: *Bylo zahájeno nahrávání trasování*. 


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








