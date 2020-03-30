---
title: Řešení potíží s rozšířením Diagnostika Azure
description: Řešení problémů při používání diagnostiky Azure ve virtuálních počítačích Azure, Service Fabric nebo cloudových službách.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 043369bd6112c4cac36539bbd764393d889439c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274577"
---
# <a name="azure-diagnostics-troubleshooting"></a>Řešení potíží se službou Azure Diagnostics
Tento článek popisuje informace o řešení potíží, které jsou důležité pro používání Diagnostika Azure. Další informace o diagnostice Azure najdete v [tématu Přehled diagnostiky Azure](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logické součásti
**Diagnostika Plugin Launcher (DiagnosticsPluginLauncher.exe)**: Spustí rozšíření Diagnostika Azure. Slouží jako proces vstupního bodu.

**Modul plug-in diagnostiky (DiagnosticsPlugin.exe):** Nakonfiguruje, spouští a spravuje životnost agenta monitorování. Je to hlavní proces, který je zahájen launcher.

**Agent monitorování (monAgent\*.exe procesy):** Monitoruje, shromažďuje a přenáší diagnostická data.  

## <a name="logartifact-paths"></a>Cesty protokolů/artefaktů
Níže jsou uvedeny cesty k některé důležité protokoly a artefakty. Odkazujeme na tyto informace ve zbytku dokumentu.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Cesta |
| --- | --- |
| **Konfigurační soubor Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<verze>\Config.txt |
| **Soubory protokolu** | C:\Protokoly\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<verze>\ |
| **Místní úložiště pro diagnostická data** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Název_role>. DiagnosticStore\WAD0107\Tabulky |
| **Konfigurační soubor agenta monitorování** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Název_role>. DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Balíček rozšíření Diagnostika Azure** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<verze> |
| **Cesta nástroje kolekce protokolu** | %SystemDrive%\Packages\GuestAgent\ |
| **Soubor protokolu MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>. \<Název_role>. DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtual Machines
| Artefakt | Cesta |
| --- | --- |
| **Konfigurační soubor Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<verze>\RuntimeSettings |
| **Soubory protokolu** | C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Místní úložiště pro diagnostická data** | C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tabulky |
| **Konfigurační soubor agenta monitorování** | C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Stavový soubor** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<verze>\Stav |
| **Balíček rozšíření Diagnostika Azure** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Cesta nástroje kolekce protokolu** | C:\WindowsAzure\Protokoly\WaAppAgent.log |
| **Soubor protokolu MonAgentHost** | C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Metrická data se na webu Azure Portal nezobrazují
Azure Diagnostics poskytuje metrická data, která se můžou zobrazit na webu Azure Portal. Pokud máte problémy se zobrazením dat na\* portálu, zkontrolujte tabulku WADMetrics v účtu úložiště Diagnostika Azure, kde se zobrazí odpovídající záznamy metrik, a ujistěte se, že je [registrovaný poskytovatel prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) Microsoft.Insights.

Zde **partitionkey** tabulky je ID prostředku, virtuální počítač nebo škálovací sada virtuálního počítače. **RowKey** je název metriky (označovaný také jako název čítače výkonu).

Pokud je ID prostředku nesprávné, **zkontrolujte,** zda je ID **konfigurace** > **diagnostiky** > správně**nastaveno.**

Pokud neexistují žádná data pro konkrétní metriku, **zkontrolujte diagnostiku Konfigurace** > **PerformanceCounter** a zjistěte, zda je zahrnuta metrika (čítač výkonu). Ve výchozím nastavení povolujeme následující čítače:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET aplikace(__celkem__)\Požadavky/s
- \ASP.NET aplikace(__celkem__)\Celkem chyb/s
- \ASP.NET\Požadavky zařazené do fronty
- \ASP.NET\Odmítnuté požadavky
- \Procesor (w3wp)\% Čas procesoru
- \Process(w3wp)\Soukromé bajty
- \Process (WaIISHost)\% Čas procesoru
- \Process(WaIISHost)\Soukromé bajty
- \Process (WaWorkerHost)\% Čas procesoru
- \Process(WaWorkerHost)\Soukromé bajty
- \Paměť\Chyby stránky/s
- \.NET CLR Memory(\% _Globální)_ Čas v GC
- \LogicalDisk(C:)\Bajty zápisu na disku/s
- \LogicalDisk(C:)\Bajty čtení disku/s
- \LogicalDisk(D:)\Bajty zápisu na disku/s
- \LogicalDisk(D:)\Bajty čtení disku/s

Pokud je konfigurace nastavena správně, ale data metriky stále nevidíte, můžete řešit potíže pomocí následujících pokynů.


## <a name="azure-diagnostics-is-not-starting"></a>Diagnostika Azure se nespouští
Informace o tom, proč se nepodařilo spustit diagnostiku Azure, naleznete v souborech **DiagnosticsPluginLauncher.log** a **DiagnosticsPlugin.log** v umístění souborů protokolu, které bylo poskytnuto dříve.

Pokud tyto protokoly označují `Monitoring Agent not reporting success after launch`, znamená to, že došlo k selhání spuštění MonAgentHost.exe. Podívejte se na protokoly v umístění, `MonAgentHost log file` které je uvedeno v předchozí části.

Poslední řádek souborů protokolu obsahuje ukončovací kód.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Pokud najdete **záporný** ukončovací kód, podívejte se do [tabulky ukončovacího kódu](#azure-diagnostics-plugin-exit-codes) v části [References](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostická data se nezaznamenávají do úložiště Azure.
Zjistěte, zda se nezobrazují žádná data nebo zda se nezobrazují některá data.

### <a name="diagnostics-infrastructure-logs"></a>Protokoly diagnostické infrastruktury
Diagnostika protokoluje všechny chyby v protokolech infrastruktury diagnostiky. Ujistěte se, že jste povolili [zachycení protokolů infrastruktury diagnostiky v konfiguraci](#how-to-check-diagnostics-extension-configuration). Pak můžete rychle vyhledat všechny relevantní chyby, které se zobrazí v `DiagnosticInfrastructureLogsTable` tabulce ve vašem nakonfigurovaném účtu úložiště.

### <a name="no-data-is-appearing"></a>Nezobrazují se žádná data.
Nejčastějším důvodem, proč se data událostí vůbec nezobrazují, je, že informace o účtu úložiště jsou definovány nesprávně.

Řešení: Opravte konfiguraci diagnostiky a přeinstalujte diagnostiku.

Pokud je účet úložiště nakonfigurován správně, vzdálený přístup do počítače a ověřte, zda jsou *spuštěny diagnosticsPlugin.exe* a *MonAgentCore.exe.* Pokud nejsou spuštěné, postupujte podle kroků v [Azure Diagnostika se nespouští](#azure-diagnostics-is-not-starting).

Pokud jsou procesy spuštěny, přejděte na [je získávání dat zachycených místně?](#is-data-getting-captured-locally)

Pokud se tím problém nevyřeší, zkuste:

1. Odinstalace agenta
2. Odebrání adresáře C:\WindowsAzure\Protokoly\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Znovu nainstalovat agenta


### <a name="part-of-the-data-is-missing"></a>Část dat chybí
Pokud získáváte některá data, ale ne všechna, znamená to, že kanál shromažďování a přenosu dat je správně nastaven. Postupujte podle podsekcí zde zúžit problém.

#### <a name="is-the-collection-configured"></a>Je kolekce nakonfigurována?
Konfigurace Diagnostika obsahuje pokyny pro určitý typ dat, která mají být shromažďována. [Zkontrolujte konfiguraci](#how-to-check-diagnostics-extension-configuration) a ověřte, zda hledáte pouze data, která jste nakonfigurovali pro kolekci.

#### <a name="is-the-host-generating-data"></a>Generuje hostitel data?
- **Čítače výkonu**: Otevřete perfmon a zkontrolujte čítač.

- **Protokoly trasování**: Vzdálený přístup do virtuálního zařízení a přidání TextWriterTraceListener do konfiguračního souboru aplikace.  Viz https://msdn.microsoft.com/library/sk36c28t.aspx nastavení naslouchací proces textu.  Ujistěte `<trace>` se, `<trace autoflush="true">`že prvek má .<br />
Pokud nevidíte protokoly trasování, které se generují, přečtěte si další informace o chybějícíprotokoly trasování.

- **ETW trasování**: Vzdálený přístup do virtuálního mě a nainstalovat PerfView.  V PerfView spusťte **File** > **User Command Listen** > **etwprovder1** > **etwprovider2**a tak dále. Příkaz **Naslouchání** rozlišuje malá a velká písmena a mezi seznamem zprostředkovatelů ETW oddělených čárkami nelze mezery. Pokud se příkaz nepodaří spustit, můžete vybrat tlačítko **Log** v pravém dolním rohu nástroje Perfview a zjistit, co se pokusilo spustit a jaký byl výsledek.  Za předpokladu, že vstup je správný, objeví se nové okno. Během několika sekund začnete vidět ETW stopy.

- **Protokoly událostí**: Vzdálený přístup do virtuálního soudu. Otevřete `Event Viewer`a ujistěte se, že existují události.

#### <a name="is-data-getting-captured-locally"></a>Jsou data zachycena místně?
Dále se ujistěte, že data jsou zachycena místně.
Data jsou místně uložena v `*.tsf` souborech v místním úložišti pro diagnostická data. Různé druhy protokolů získat `.tsf` shromažďovány v různých souborech. Názvy jsou podobné názvům tabulek ve službě Azure Storage.

Například `Performance Counters` získat shromažďovány v `PerformanceCountersTable.tsf`. Protokoly událostí jsou `WindowsEventLogsTable.tsf`shromažďovány v . Pomocí pokynů v části [Místní extrakce protokolu](#local-log-extraction) otevřete soubory místní kolekce a ověřte, zda se shromažďují na disku.

Pokud nevidíte protokoly, které se shromažďují místně, a již jste ověřili, že hostitel generuje data, pravděpodobně máte problém s konfigurací. Pečlivě zkontrolujte konfiguraci.

Zkontrolujte také konfiguraci, která byla vygenerována pro soubor MonitoringAgent MaConfig.xml. Ověřte, zda existuje oddíl, který popisuje příslušný zdroj protokolu. Potom ověřte, zda není ztracena v překladu mezi konfigurací diagnostiky a konfigurací agenta monitorování.

#### <a name="is-data-getting-transferred"></a>Jsou data přenášena?
Pokud jste ověřili, že data jsou zachycena místně, ale stále je nevidíte v účtu úložiště, postupujte takto:

- Ověřte, že jste zadali správný účet úložiště a že jste nepřehrnuli klíče pro daný účet úložiště. U Cloudových služeb Azure někdy vidíme, `useDevelopmentStorage=true`že se uživatelé neaktualizují .

- Ověřte, zda je zadaný účet úložiště správný. Ujistěte se, že nemáte síťová omezení, která brání součástem v dosažení koncových bodů veřejného úložiště. Jedním ze způsobů, jak to udělat, je vzdálený přístup do počítače a zkuste něco napsat na stejný účet úložiště sami.

- Nakonec se můžete podívat na jaké chyby jsou hlášeny agentmonitorování. Agent monitorování zapisuje své protokoly v `maeventtable.tsf`, který je umístěn v místním úložišti pro diagnostická data. Podle pokynů v části [Místní extrakce protokolu](#local-log-extraction) pro otevření tohoto souboru. Pak se pokuste `errors` zjistit, zda existují, které označují selhání čtení do místních souborů zápisu do úložiště.

### <a name="capturing-and-archiving-logs"></a>Zachytávání a archivace protokolů
Pokud uvažujete o kontaktování podpory, první věc, kterou by vás mohli požádat, je shromažďovat protokoly z vašeho počítače. Můžete ušetřit čas tím, že dělá, že sami. Spusťte `CollectGuestLogs.exe` nástroj na cestě nástroje kolekce protokolu. Generuje soubor ZIP se všemi příslušnými protokoly Azure ve stejné složce.

## <a name="diagnostics-data-tables-not-found"></a>Tabulky diagnostických dat nebyly nalezeny.
Tabulky v úložišti Azure, které obsahují události ETW, se pojmenují pomocí následujícího kódu:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Zde naleznete příklad:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Tento kód generuje čtyři tabulky:

| Událost | Název tabulky |
| --- | --- |
| provider="prov1" &lt;Událost id="1" /&gt; |WADEvent+MD5("prov1")+"1" |
| provider="prov1" &lt;Event id="2" eventDestination="dest1" /&gt; |WADdest1 |
| provider="prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider="prov2" &lt;DefaultEvents eventDestination="dest2" /&gt; |WADdest2 |

## <a name="references"></a>Odkazy

### <a name="how-to-check-diagnostics-extension-configuration"></a>Jak zkontrolovat konfiguraci rozšíření Diagnostika
Nejjednodušší způsob, jak zkontrolovat konfiguraci rozšíření, je přejít na [Azure Resource Explorer](https://resources.azure.com)a pak přejít na virtuální počítač nebo cloudovou službu, kde je rozšíření Diagnostika Azure (IaaSDiagnostics / PaaDiagnostics).

Případně vzdálenou plochu do počítače a podívejte se na konfigurační soubor Diagnostika Azure, který je popsán v části cesta artefakty protokolu.

V obou případech vyhledejte **microsoft.Azure.Diagnostics**a potom **pole xmlCfg** nebo **WadCfg.**

Pokud hledáte na virtuálním počítači a **wadcfg** pole je k dispozici, znamená to, že konfigurace je ve formátu JSON. Pokud je pole **xmlCfg** k dispozici, znamená to, že konfigurace je ve formátu XML a je kódována na základně 64. Je třeba [dekódovat zobrazíte](https://www.bing.com/search?q=base64+decoder) XML, který byl načten diagnostikou.

Pro roli cloudové služby, pokud vyberete konfiguraci z disku, data jsou zakódována base64, takže je třeba [dekódovat,](https://www.bing.com/search?q=base64+decoder) abyste viděli XML, který byl načten diagnostikou.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Ukončovací kódy modulů plug-in Azure Diagnostics
Plugin vrátí následující ukončovací kódy:

| Ukončovací kód | Popis |
| --- | --- |
| 0 |Úspěch |
| -1 |Obecná chyba. |
| -2 |Soubor rcf nelze načíst.<p>K této vnitřní chybě by mělo dojít pouze v případě, že je ručně vyvolán spouštěč modulu plug-in agenta hosta na virtuálním počítači. |
| -3 |Konfigurační soubor Diagnostika nelze načíst.<p><p>Řešení: Způsobeno konfiguračním souborem, který nepředává ověření schématu. Řešením je poskytnout konfigurační soubor, který vyhovuje schématu. |
| -4 |Jiná instance diagnostiky agenta monitorování již používá adresář místních prostředků.<p><p>Řešení: Zadejte jinou hodnotu pro **LocalResourceDirectory**. |
| -6 |Spouštěč pluginů hosta se pokusil spustit diagnostiku s neplatným příkazovým řádkem.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je ručně vyvolán spouštěč modulu plug-in agenta hosta na virtuálním počítači. |
| -10 |Modul plug-in Diagnostika byl ukončen s neošetřenou výjimkou. |
| -11 |Agent hosta nemohl vytvořit proces zodpovědný za spuštění a monitorování agenta monitorování.<p><p>Řešení: Ověřte, zda jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů.<p> |
| -101 |Neplatné argumenty při volání modulu plug-in Diagnostika.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je ručně vyvolán spouštěč modulu plug-in agenta hosta na virtuálním počítači. |
| -102 |Proces pluginu není schopen inicializovat sám.<p><p>Řešení: Ověřte, zda jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů. |
| -103 |Proces pluginu není schopen inicializovat sám. Konkrétně není možné vytvořit objekt protokolovacího nástroje.<p><p>Řešení: Ověřte, zda jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů. |
| -104 |Nelze načíst soubor rcf poskytnutý agentem hosta.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je ručně vyvolán spouštěč modulu plug-in agenta hosta na virtuálním počítači. |
| -105 |Modul plug-in Diagnostika nemůže otevřít konfigurační soubor Diagnostika.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že modul plug-in diagnostiky je ručně vyvolána nesprávně na virtuálním počítači. |
| -106 |Nelze číst konfigurační soubor Diagnostika.<p><p>Příčinou je nepředání ověření schématu pomocí konfiguračního souboru. <br><br>Řešení: Zadejte konfigurační soubor, který je v souladu se schématem. Další informace naleznete v tématu [Jak zkontrolovat konfiguraci rozšíření diagnostiky](#how-to-check-diagnostics-extension-configuration). |
| -107 |Předání adresáře prostředků agentovi monitorování je neplatné.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že agent monitorování je ručně vyvolána nesprávně na virtuálním počítači.</p> |
| -108 |Konfigurační soubor Diagnostika nelze převést do konfiguračního souboru agenta monitorování.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je modul plug-in Diagnostika vyvolán ručně s neplatným konfiguračním souborem. |
| -110 |Chyba konfigurace obecné diagnostiky.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je modul plug-in Diagnostika vyvolán ručně s neplatným konfiguračním souborem. |
| -111 |Nelze spustit agenta monitorování.<p><p>Řešení: Ověřte, zda jsou k dispozici dostatečné systémové prostředky. |
| -112 |Obecná chyba |

### <a name="local-log-extraction"></a>Místní log extrakce
Agent monitorování shromažďuje protokoly a `.tsf` artefakty jako soubory. Soubor `.tsf` není čitelný, ale můžete jej `.csv` převést na následující:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nový svolaný `<relevantLogFile>.csv` soubor je vytvořen `.tsf` ve stejné cestě jako odpovídající soubor.

>[!NOTE]
> Tento nástroj je třeba spustit pouze proti hlavnímu souboru TSF (například PerformanceCountersTable.tsf). Doprovodné soubory (například\*\*PerformanceCountersTables_ 001.tsf, PerformanceCountersTables_\*\*002.tsf a tak dále) jsou automaticky zpracovány.

### <a name="more-about-missing-trace-logs"></a>Další informace o chybějících protokolech trasování

>[!NOTE]
> Následující informace platí hlavně pro Cloudové služby Azure, pokud jste nenakonfigurovali DiagnosticsMonitorTraceListener v aplikaci, která běží na vašem virtuálním počítači IaaS.

- Ujistěte se, že **DiagnosticMonitorTraceListener** je nakonfigurován v web.config nebo app.config.  To je ve výchozím nastavení nakonfigurován v projektech cloudových služeb. Někteří zákazníci však komentovat, což způsobí, že příkazy trasování nejsou shromažďovány diagnostiky.

- Pokud protokoly nejsou stále zapsány z **OnStart** nebo **Run** metoda, ujistěte se, že **DiagnosticMonitorTraceListener** je v app.config.  Ve výchozím nastavení je v souboru web.config, ale to platí pouze pro kód spuštěný v rámci programu w3wp.exe. Takže ji potřebujete v app.config zachytit stopy, které jsou spuštěny v WaIISHost.exe.

- Ujistěte se, že používáte **Diagnostics.Trace.TraceXXX** místo **Diagnostics.Debug.WriteXXX.** Ladicí příkazy jsou odebrány z sestavení verze.

- Ujistěte se, že zkompilovaný kód skutečně má **Diagnostics.Trace řádky** (použijte Reflektor, ildasm, nebo ILSpy ověřit). **Diagnostics.Trace** příkazy jsou odebrány z kompilovanébinární, pokud použijete symbol podmíněné kompilace TRACE. Toto je běžný problém, ke kterému dochází při použití msbuild k sestavení projektu.   

## <a name="known-issues-and-mitigations"></a>Známé problémy a skutečnosti snižující závažnost rizika
Zde je seznam známých problémů se známými skutečnosti snižující závažnost rizika:

**1. Závislost .NET 4.5**

Rozšíření diagnostiky Windows Azure má závislost za běhu na rozhraní .NET 4.5 nebo novější. V době psaní všech počítačů, které jsou zřízeny pro Cloudové služby Azure, stejně jako všechny oficiální image, které jsou založené na virtuálních počítačích Azure, mají .NET 4.5 nebo novější nainstalován.

Je stále možné dojít k situaci, kdy se pokusíte spustit rozšíření diagnostiky Windows Azure na počítači, který nemá .NET 4.5 nebo novější. K tomu dochází, když vytvoříte počítač ze starého obrázku nebo snímku nebo když přinesete vlastní disk.

To se obvykle projevuje jako ukončovací kód **255** při spuštění **DiagnosticsPluginLauncher.exe.** K selhání dochází z důvodu následující neošetřené výjimky:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Zmírnění:** Nainstalujte do počítače rozhraní .NET 4.5 nebo novější.

**2. Údaje o čítačích výkonu jsou k dispozici v úložišti, ale nezobrazují se na portálu**

Prostředí portálu ve virtuálních počítačích zobrazuje ve výchozím nastavení určité čítače výkonu. Pokud nevidíte čítače výkonu a víte, že data jsou stále generována, protože jsou k dispozici v úložišti, zkontrolujte následující:

- Zda data v úložišti má čítače názvy v angličtině. Pokud názvy čítačů nejsou v angličtině, graf metriky portálu jej nerozpozná. **Zmírnění :** Změna jazyka počítače na angličtinu pro systémové účty. Chcete-li to provést, vyberte položku Nastavení kopírování**pro správu** > **oblasti** >  **ovládacích** > **panelů**. Dále zrušte výběr **uvítací obrazovky a systémových účtů,** aby se vlastní jazyk nepoužil na systémový účet.

- Pokud používáte zástupné\*znaky ( ) v názvech čítačů výkonu, portál nebude moct korelovat nakonfigurované a shromážděné čítače při čítače výkonu jsou odeslány do jímky služby Azure Storage. **Zmírnění rizik:** Chcete-li se ujistit, že\*můžete použít zástupné znaky a mít portál rozbalit ( ), směrovat čítače výkonu do jímky Azure Monitor.

