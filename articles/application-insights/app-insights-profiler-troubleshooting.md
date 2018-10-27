---
title: Poradce při potížích s Azure Application Insights Profiler | Dokumentace Microsoftu
description: Tato stránka obsahuje řešení problémů s kroky a informace, které pomůžou vývojářům, kteří jsou potíže s povolením nebo pomocí Application Insights profileru.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 28de0f8bdcaa730c5beea0c630d4e86e15642809
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142371"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Poradce při potížích, povolení nebo zobrazení Application Insights Profiler

## <a id="troubleshooting"></a>Obecné řešení potíží

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Profily jsou odeslány, pouze pokud je spuštěn profiler, existují požadavky na aplikace
Application Insights Profiler shromažďuje data profileru pro dvě minuty každou hodinu nebo když [ **nyní profilu** ](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json) stisknutí tlačítka **nakonfigurovat Application Insights Profiler**stránky. Ale když může být připojen k žádosti, která se stalo při běhu profileru pouze nahrání dat profilování. 

Profiler zapisuje zprávy trasování a vlastní události do vašeho prostředku application insights. Tyto události můžete vidět, jak je profiler běží. Pokud se domníváte, že se profiler by měla být spuštěná a zachytávání trasování, ale se nezobrazují na stránce výkon, můžete zkontrolovat, jak profiler běží:

1. Hledání zpráv trasování a vlastní události odeslané do prostředku Application Insights profiler. Tento řetězec hledání můžete najít relevantní data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Tady je příklad z dvou vyhledávání z dvou různých prostředků AI na snímku obrazovky níže. 
    
    * Na levé straně je z aplikace, která není získávání požadavky, je spuštěn profiler. Zobrazí se zpráva, že nahrávání byla zrušena z důvodu žádná aktivita. 

    * V příkladu na pravé straně uvidíte, že profiler spuštěná a odeslání vlastních událostí při zjištění požadavků, které se stalo při běhu profilování. Pokud se zobrazí ServiceProfilerSample vlastní událost, znamená to profiler připojen trasování na žádost a můžete zobrazit trasování ze stránky výkon Application Insights.

    * Pokud nevidíte žádnou telemetrii vůbec, není spuštěn profiler. Budete muset přečíst části řešení potíží pro váš typ konkrétní aplikace v tomto dokumentu níže.  

     ![Hledat Telemetrii Profiler][profiler-search-telemetry]

1. Pokud existují požadavky během časového období spuštění profileru, ujistěte se, že jsou požadavky zpracovávány podle části aplikace, který má povolené profileru. Někdy se aplikace skládají z několika součástí, ale Profiler je povolená jenom pro některé, ne všechny součásti. Na stránce konfigurace Application Insights Profiler se zobrazí součásti, které jste nahráli trasování.

### <a name="net-core-21-bug"></a>**.Net Core 2.1 chyb** 
Agent profileru, který zabrání odesílání trasování z aplikací běžících na ASP.NET Core 2.1 je chyba. Jsme pracují na opravě a bude mít je připraven brzy. Oprava této chyby se nasadí do konce října.

### <a name="other-things-to-check"></a>**Chcete-li zkontrolovat další věci:**
* Vaše aplikace běží na rozhraní .NET Framework 4.6.
* Když je vaše aplikace webové aplikace ASP.NET Core, musí používat minimálně ASP.NET Core 2.0.
* Pokud data, která se pokoušíte zobrazit, je starší než několik týdnů, zkuste omezení časový filtr a zkuste to znovu. Trasování se odstraní po sedm dní.
* Ujistěte se, že proxy nebo brány firewall nejsou zablokoval přístup k https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>**Double, počítací v paralelních vláken**

V některých případech se celková doba metriky v prohlížeči zásobníku je větší než doba trvání žádosti.

Tato situace může nastat, když dva nebo více vláken jsou spojené se žádostí a jsou prováděny současně. V takovém případě čas celkový počet vláken je větší než uplynulý čas. Jedno vlákno může čekat na dokončení druhé. V prohlížeči se pokusí tuto situaci detekovat a vynechá nezajímavé čekání, ale chybuje Toolbar zobrazení příliš mnoho informací o spíše vynechat, které mohou být důležitým informacím vždy.

Při paralelních vláken se zobrazí v trasování, určete, která vlákna čekající tak můžete zjistit kritickou cestu pro žádost. Ve většině případů vlákno, které rychle přejde do stavu čekání jednoduše čeká na ostatní vlákna. Soustředit se na ostatní vlákna a ignorovat časové čekajících vláken.

### <a name="error-report-in-the-profiling-viewer"></a>**Zprávu o chybách v prohlížeči profilování**
Vyplňte lístek podpory na portálu. Nezapomeňte zahrnout ID korelace z chybové zprávy.

## <a name="troubleshooting-profiler-on-app-services"></a>Řešení potíží s Profiler v App Service
### <a name="for-the-profiler-to-work-properly"></a>**Pro profiler fungovala správně:**
* Plán služby app service web musí být na úrovni Basic nebo vyšší.
* Webové aplikace musí mít rozšíření Application Insights pro aplikaci služby (2.6.5) nainstalovaný.
* Webové aplikace musí mít **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení aplikace, které jsou nakonfigurované se stejným klíčem instrumentace, který používá sadu SDK Application Insights.
* Webové aplikace musí mít **APPINSIGHTS_PROFILERFEATURE_VERSION** nastavení aplikace, které definované a nastavená na 1.0.0.
* **ApplicationInsightsProfiler2** webové úlohy musí být spuštěna. Webovou úlohu můžete zkontrolovat tak, že přejdete do [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/)a otevřete **řídicím panelu webjobs nechat** do nabídky Nástroje. Jak je vidět na snímcích obrazovky níže, kliknutím na odkaz ApplicationInsightsProfiler2, zobrazí se podrobnosti o webové úlohy, včetně protokolu.

    Tady je odkaz, který budete muset kliknout a podívat se webová úloha. Podrobnosti: 

    ![Profiler webové úlohy]

    Tady je stránka, která zobrazuje podrobnosti. Můžete stáhnout a odeslat na náš tým, pokud nelze zjistit, proč není profiler práci za vás.
    
    ![Profiler-webová úloha log]

### <a name="manual-installation"></a>**Ruční instalace**

Když konfigurujete Profiler, jsou provedeny aktualizace nastavení webové aplikace. Pokud vaše prostředí vyžaduje ji můžete ručně aplikovat aktualizace. Příkladem může být, že vaše aplikace běží v prostředí s Web Apps pro PowerApps.

1. V **webový ovládací prvek aplikace** otevřeným podoknem **nastavení**.
1. Nastavte **rozhraní .net Framework verze** k **v4.6**.
1. Nastavte **Always On** k **na**.
1. Přidat **APPINSIGHTS_INSTRUMENTATIONKEY** aplikace nastavení a nastavte hodnotu na stejný klíč instrumentace, který se používá sada SDK.
1. Přidat **APPINSIGHTS_PROFILERFEATURE_VERSION** nastavení aplikace a nastavte hodnotu na 1.0.0.
1. Otevřít **Rozšířené nástroje**.
1. Vyberte **Přejít** otevřete web Kudu.
1. Na webu Kudu, vyberte **rozšířením webu**.
1. Nainstalujte **Application Insights** z Galerie Azure Web Apps.
1. Restartujte webovou aplikaci.

### <a name="too-many-active-profiling-sessions"></a>**Příliš mnoho aktivní relace profilování**

V současné době můžete povolit Profiler na maximálně čtyři Azure webové aplikace a sloty nasazení, které jsou spuštěny v rámci stejného plánu služby. Pokud Profiler webové úlohy je příliš mnoho aktivních relací profilování, přesuňte některé webové aplikace k plánu jiné služby.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Chyba nasazení: adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.

Pokud nasazujete webovou aplikaci do prostředku webové aplikace s Profiler povolené, může se zobrazit následující zpráva:

*Adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.*

K této chybě dochází při spuštění nasazení webu pomocí skriptů nebo z kanálu nasazení Azure DevOps. Řešením je přidat následující parametry další nasazení do úlohy nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstranit složku, která používají Application Insights Profiler a odblokování procesu opětovného nasazení. Neovlivňují Profiler instanci, na kterém aktuálně běží.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak zjistím, zda je spuštěna Application Insights Profiler?

Profiler běží jako průběžné webové úlohy ve webové aplikaci. Můžete otevřít v prostředku webové aplikace [webu Azure portal](https://portal.azure.com). V **WebJobs** podokně zkontrolujte stav **ApplicationInsightsProfiler**. Pokud není spuštěná, Otevřít **protokoly** zobrazíte další informace.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Poradce při potížích se Profiler a využitím WAD

Zkontrolujte, jestli je profiler správně nakonfigurovaný pomocí WAD tři věci. Je třeba nejprve, zkontrolujte, že obsah WAD konfigurace, které jsou nasazeny odpovídají vašemu očekávání. Za druhé je potřeba zkontrolovat, že WAD předává správné Instrumentační klíč na příkazového řádku profileru. Třetí můžete zkontrolovat soubor protokolu profileru zobrazíte Pokud profiler spuštěn, ale je stále k chybě. 

Pokud chcete zkontrolovat nastavení, která se používá ke konfiguraci WAD, budete muset přihlásit k virtuálnímu počítači a otevřít soubor protokolu v tomto umístění: 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
V tomto souboru můžete vyhledat řetězec "WadCfg" a nastavení, které byly předány do virtuálního počítače ke konfiguraci WAD zjistíte. Můžete zkontrolovat správnost tohoto Instrumentační klíč používaný profiler jímky.

Za druhé můžete zkontrolovat příkazového řádku, který se používá ke spuštění profileru. Tento soubor obsahuje argumenty pro spuštění profileru.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Zkontrolujte, že Instrumentační klíč do příkazového řádku profileru, je správná. 

Třetí pomocí cesty nacházejí v souboru config.json výše, zkontrolujte soubor protokolu profileru. Zobrazí informace o ladění uvedením nastavení, která používá profiler a stavové a chybové zprávy z profileru. Pokud profiler je spuštěné, přestože aplikace přijímá požadavky, zobrazí se vám tato zpráva: zjistí aktivita z Instrumentační klíč. Při nahrání trasování, zobrazí se vám tato zpráva: bylo zahájeno nahrávání trasování. 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[Profiler webové úlohy]:./media/app-insights-profiler/Profiler-webjob.png
[Profiler-webová úloha log]:./media/app-insights-profiler/Profiler-webjob-log.png








