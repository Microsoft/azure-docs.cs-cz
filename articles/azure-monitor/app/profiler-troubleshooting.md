---
title: Poradce při potížích s profilerem Přehledů aplikací Azure
description: Tento článek obsahuje postupy řešení potíží a informace, které vývojářům, kteří mají potíže s povolením nebo používáním profileru Application Insights, pomáhají vývojářům, kteří mají potíže s povolením nebo používáním aplikace.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 55bc4ff05b650884ef17e0de10d7156cbf458a9c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640946"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Poradce při potížích s povolením nebo zobrazením profileru Application Insights

## <a name="active-issues"></a>Aktivní problémy

* Profilování pro aplikace ASP.NET Core 3.x je teď podporované ve službě Azure App Services.

## <a name="general-troubleshooting"></a><a id="troubleshooting"></a>Obecné řešení potíží

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Profily se nahrávají pouze v případě, že existují požadavky na vaši aplikaci, zatímco profiler běží

Azure Application Insights Profiler shromažďuje data profilování po dobu dvou minut každou hodinu. Shromažďuje také data, když vyberete tlačítko **Profil nyní** v podokně **Konfigurovat profilování přehledů aplikací.** Ale profilování data se nahraje pouze v případě, že lze připojit k požadavku, ke kterému došlo, zatímco Profiler byl spuštěn. 

Profiler zapisuje trasovací zprávy a vlastní události do prostředku Application Insights. Tyto události můžete použít k zobrazení, jak profiler běží. Pokud si myslíte, že profiler by měl být spuštěn a zachytává trasování, ale nejsou zobrazeny v podokně **Výkon,** můžete zkontrolovat, jak profiler běží:

1. Vyhledejte trasovací zprávy a vlastní události odeslané profilerem do prostředku Application Insights. Pomocí tohoto vyhledávacího řetězce můžete najít příslušná data:

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    Následující obrázek zobrazuje dva příklady vyhledávání ze dvou zdrojů AI: 
    
   * Na levé straně aplikace není příjem požadavků, zatímco Profiler je spuštěn. Zpráva vysvětluje, že nahrávání bylo zrušeno z důvodu žádné aktivity. 

   * Vpravo profiler spustil a odeslal vlastní události, když zjistil požadavky, ke kterým došlo při spuštění profileru. Pokud serviceprofilersample vlastní událost se zobrazí, znamená to, že Profiler připojen trasování k požadavku a můžete zobrazit trasování v podokně **Výkon přehledy aplikací.**

     Pokud se nezobrazí žádná telemetrie, profiler není spuštěn. Informace o řešení potíží najdete v části řešení potíží pro konkrétní typ aplikace dále v tomto článku.  

     ![Hledat telemetrii Profiler][profiler-search-telemetry]

1. Pokud byly požadavky při profileru spuštěn, ujistěte se, že požadavky jsou zpracovány část aplikace, která má Profiler povolena. Přestože aplikace někdy sestávají z více součástí, Profiler je povolen pouze pro některé součásti. V podokně **Konfigurovat profilování přehledů aplikací** se zobrazí součásti, které nahrály trasování.

### <a name="other-things-to-check"></a>Další věci ke kontrole
* Ujistěte se, že vaše aplikace běží na rozhraní .NET Framework 4.6.
* Pokud je vaše webová aplikace ASP.NET základní aplikaci, musí být spuštěna alespoň ASP.NET Core 2.0.
* Pokud jsou data, která se pokoušíte zobrazit, starší než několik týdnů, zkuste omezit časový filtr a akci opakujte. Stopy se po sedmi dnech odstraní.
* Ujistěte se, že servery proxy nebo https://gateway.azureserviceprofiler.netbrána firewall nezablokovaly přístup k aplikaci .
* Profiler není podporován v plánech bezplatných nebo sdílených služeb aplikace. Pokud používáte jeden z těchto plánů, zkuste škálování až na jeden ze základních plánů a Profiler by měl začít pracovat.

### <a name="double-counting-in-parallel-threads"></a><a id="double-counting"></a>Dvojité počítání v paralelních závitech

V některých případech je metrika celkové doby v prohlížeči zásobníku delší než doba trvání požadavku.

K této situaci může dojít, pokud jsou k požadavku přidružena dvě nebo více vláken a pracují paralelně. V takovém případě je celkový čas vlákna větší než uplynulý čas. Jedno vlákno může čekat na druhé dokončení. Divák se pokusí tuto situaci zjistit a vyneche nezajímavé čekání. Přitom chybovat na straně zobrazení příliš mnoho informací, spíše než vynechat to, co by mohlo být kritické informace.

Když se zobrazí paralelní vlákna v trasování, zjistěte, která vlákna čekají, abyste mohli zjistit kritickou cestu pro požadavek. Obvykle vlákno, které rychle přejde do stavu čekání, jednoduše čeká na ostatní vlákna. Soustřeďte se na ostatní vlákna a ignorujte čas v čekajících vláknech.

### <a name="error-report-in-the-profile-viewer"></a>Zpráva o chybách v prohlížeči profilů
Odešlete lístek podpory na portálu. Nezapomeňte zahrnout ID korelace z chybové zprávy.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Poradce při potížích s profilerem ve službě Azure App Service
Pro profiler pracovat správně:
* Váš plán služby webové aplikace musí být základní úroveň nebo vyšší.
* Vaše webová aplikace musí mít povolené Application Insights.
* Vaše webová aplikace musí mít následující nastavení aplikace:

    |Nastavení aplikace    | Hodnota    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro váš prostředek Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* Webová úloha **ApplicationInsightsProfiler3** musí být spuštěna. Kontrola webové úlohy:
   1. Přejít na [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. V nabídce **Nástroje** vyberte **řídicí panel webových úloh**.  
      Otevře se podokno **WebJobs.** 
   
      ![úloha profiler-web]   
   
   1. Chcete-li zobrazit podrobnosti o webjob, včetně protokolu, vyberte **ApplicationInsightsProfiler3** odkaz.  
     Otevře se podokno **Průběžné podrobnosti o webové úloze.**

      ![profiler-webjob-log]

Pokud nemůžete zjistit, proč profiler nepracuje pro vás, můžete si stáhnout log a poslat serviceprofilerhelp@microsoft.comjej do našeho týmu o pomoc, . 
    
### <a name="manual-installation"></a>Ruční instalace

Při konfiguraci profileru se aktualizují nastavení webové aplikace. Pokud to vaše prostředí vyžaduje, můžete aktualizace použít ručně. Příkladem může být, že vaše aplikace běží v prostředí webových aplikací pro PowerApps. Ruční použití aktualizací:

1. V podokně **Ovládací prvek webové aplikace** otevřete **nastavení**.

1. Nastavte **verzi rozhraní .NET Framework** na verzi **4.6**.

1. Nastavit **vždy na** **zapnuto**.
1. Vytvořte tato nastavení aplikace:

    |Nastavení aplikace    | Hodnota    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pro váš prostředek Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Příliš mnoho aktivních relací profilování

V současné době můžete povolit Profiler na maximálně čtyři webové aplikace Azure a sloty nasazení, které běží ve stejném plánu služeb. Pokud máte více než čtyři webové aplikace spuštěné v jednom plánu služby aplikace, profiler může vyvolat *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler běží samostatně pro každou webovou aplikaci a pokusí se spustit relaci trasování událostí pro Windows (ETW) pro každou aplikaci. Ale omezený počet relací ETW může být aktivní najednou. Pokud webová úloha Profiler hlásí příliš mnoho aktivních relací profilování, přesuňte některé webové aplikace do jiného plánu služeb.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Chyba nasazení: Adresář není\\prázdný\\\\'D: domovská stránka wwwroot\\App_Data\\úlohy'

Pokud znovu nasazujete webovou aplikaci do prostředku webových aplikací s povoleným profilerem, může se zobrazit následující zpráva:

*Adresář není prázdný\\'D: domovská\\stránka\\wwwroot\\App_Data\\pracovních míst'*

K této chybě dochází, pokud spustíte nasazení webu ze skriptů nebo z kanálu nasazení Azure DevOps. Řešením je přidat následující další parametry nasazení do úlohy nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstraní složku, která je používána Profilerem Application Insights, a odblokují proces opětovného nasazení. Nemají vliv na instanci Profiler, která je aktuálně spuštěna.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak zjistím, jestli je spuštěný profiler application insights?

Profiler běží jako souvislé webové úlohy ve webové aplikaci. Prostředek webové aplikace můžete otevřít na [webu Azure Portal](https://portal.azure.com). V podokně **WebJobs** zkontrolujte stav **ApplicationInsightsProfiler**. Pokud není spuštěn, otevřete **protokoly** získat další informace.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Poradce při potížích s profilerem a diagnostikou Azure

>**Chyba v profileru, který je dodáván v WAD pro cloudové služby byla opravena.** Nejnovější verze wadu (1.12.2.0) pro cloudové služby funguje se všemi nejnovějšími verzemi sady App Insights SDK. Hostitelé cloudových služeb upgradují wad automaticky, ale není okamžitý. Chcete-li vynutit upgrade, můžete znovu nasadit službu nebo restartovat uzel.

Chcete-li zjistit, zda profiler je správně nakonfigurován pomocí Diagnostika Azure, proveďte následující tři věci: 
1. Nejprve zkontrolujte, jestli obsah konfigurace Diagnostika Azure, které jsou nasazené jsou co očekáváte. 

1. Za druhé, ujistěte se, že Diagnostika Azure předá správné iKey na příkazovém řádku Profiler. 

1. Za třetí zkontrolujte soubor protokolu Profiler a zjistěte, zda profiler běžel, ale vrátil chybu. 

Kontrola nastavení, která byla použita ke konfiguraci diagnostiky Azure:

1. Přihlaste se k virtuálnímu počítači a otevřete soubor protokolu v tomto umístění. (Disk by mohl být c: nebo d: a plugin verze by mohla být odlišná.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    – nebo –
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. V souboru můžete vyhledat řetězec **WadCfg** najít nastavení, které byly předány do virtuálního počítače ke konfiguraci Diagnostika Azure. Můžete zkontrolovat, zda iKey používá jímka profileru je správné.

1. Zkontrolujte příkazový řádek, který se používá ke spuštění profileru. Argumenty, které se používají ke spuštění Profiler jsou v následujícím souboru. (Pohon může být c: nebo d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Ujistěte se, že iKey na příkazovém řádku Profileru je správný. 

1. Pomocí cesty nalezené v předchozím souboru *config.json* zkontrolujte soubor protokolu Profiler. Zobrazí informace o ladění, které označuje nastavení, které profiler používá. Zobrazuje také stavové a chybové zprávy z profileru.  

    Pokud profiler je spuštěn, zatímco vaše aplikace přijímá požadavky, zobrazí se následující zpráva: *Aktivita zjištěná z iKey*. 

    Při nahrávání trasování se zobrazí následující zpráva: *Začněte nahrávat trasování*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Úprava pravidel síťového serveru proxy nebo brány firewall

Pokud se vaše aplikace připojuje k Internetu přes proxy server nebo bránu firewall, bude pravděpodobně nutné upravit pravidla, aby aplikace mohla komunikovat se službou Profiler application Insights. Ip adresy používané profilerem application insights jsou součástí značky služby Azure Monitor.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[úloha profiler-web]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








