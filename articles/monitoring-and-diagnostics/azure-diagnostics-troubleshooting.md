---
title: Řešení potíží s Azure Diagnostics rozšíření
description: Vyřešení problémů při používání Azure diagnostics v Azure Virtual Machines, Service Fabric nebo cloudové služby.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/12/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 8f41605114de296b626418d0a868e3ed778c0640
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263842"
---
# <a name="azure-diagnostics-troubleshooting"></a>Řešení potíží s Azure Diagnostics
Tento článek popisuje informace o odstraňování potíží, které se týkají pomocí Azure Diagnostics. Další informace o Azure diagnostics najdete v tématu [přehled Azure Diagnostics](azure-diagnostics.md).

## <a name="logical-components"></a>Logické součásti
**Modul plug-in Spouštěče diagnostiky (DiagnosticsPluginLauncher.exe)**: spustí rozšíření Azure Diagnostics. Slouží jako položka bodu procesu.

**Modul plug-in diagnostiky (DiagnosticsPlugin.exe)**: Konfiguruje, spustí a spravuje životnost agent sledování. Je hlavní proces, který je spuštěn spuštění.

**Monitoring Agent (MonAgent\*procesy .exe)**: monitorování, shromažďuje a přenosy dat diagnostiky.  

## <a name="logartifact-paths"></a>Protokol/artefaktů cesty
Toto jsou cesty k některé důležité protokoly a artefakty. Tyto informace ve zbývající části dokumentu označujeme.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefaktů | Cesta |
| --- | --- |
| **Azure Diagnostics konfiguračního souboru** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Soubory protokolu** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Místní úložiště pro data diagnostiky** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Agent konfiguračním souborem monitorování** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Balíček rozšíření Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Cesta kolekce nástroje protokolu** | %SystemDrive%\Packages\GuestAgent\ |
| **Soubor protokolu MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuální počítače
| Artefaktů | Cesta |
| --- | --- |
| **Azure Diagnostics konfiguračního souboru** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Soubory protokolu** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Místní úložiště pro data diagnostiky** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Agent konfiguračním souborem monitorování** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Stav souboru** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Balíček rozšíření Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Cesta kolekce nástroje protokolu** | C:\WindowsAzure\Packages |
| **Soubor protokolu MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Data metriky se nezobrazí na portálu Azure
Azure Diagnostics poskytuje metriky data, která lze zobrazit na portálu Azure. Pokud máte potíže s zobrazuje data v portálu, zkontrolujte WADMetrics\* tabulky v účtu úložiště Azure Diagnostics zobrazit, pokud existují odpovídající záznamy metriky. 

Zde **PartitionKey** tabulky je sada škálování ID, virtuální počítač nebo virtuální počítač prostředků. **RowKey** je název metriky (také označované jako název čítače výkonu).

Pokud je ID prostředku není v pořádku, zkontrolujte **diagnostiky** **konfigurace** > **metriky** > **ResourceId**Pokud ID prostředku nastavena správně.

Pokud nejsou žádná data pro konkrétní metriku, zkontrolujte **konfigurace diagnostiky** > **PerformanceCounter** jestli jsou zahrnuté metrika (čítače výkonu). Ve výchozím nastavení povolíme následující čítače:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ASP.NET aplikace (__celkový__) \Requests/Sec
- \ASP.NET aplikace (__celkový__) \Errors celkem/s
- \ASP.NET\Requests zařazených do fronty
- \ASP.NET\Requests odmítl
- \Processor(W3wp)\% času procesoru
- Bajty \Private \Process (w3wp)
- \Process(WaIISHost)\% času procesoru
- Bajty \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% času procesoru
- Bajty \Private \Process (WaWorkerHost)
- \Memory\Page chyby/s
- \.NET CLR paměti (_globální_)\% čas
- Zápis \Disk \LogicalDisk (C:) bajty/s
- \Disk \LogicalDisk (C:) přečtených bajtů/s
- Zápis \Disk \LogicalDisk (D:) bajty/s
- \Disk \LogicalDisk (D:) přečtených bajtů/s

Pokud je správně nastavena konfigurace ale stále nemůžete vidět data metriky, použijte následující pokyny k vyřešení.


## <a name="azure-diagnostics-isnt-starting"></a>Azure Diagnostics není spouštění.
Informace o Proč se nepodařilo spustit Azure Diagnostics najdete v tématu **DiagnosticsPluginLauncher.log** a **DiagnosticsPlugin.log** soubory v umístění souborů protokolu, který byl dříve zadán. 

Pokud tyto protokoly znamenat `Monitoring Agent not reporting success after launch`, znamená to, došlo k chybě spuštění MonAgentHost.exe. Prohlédněte si protokoly v umístění, které je uvedené pro `MonAgentHost log file` v předchozí části.

Poslední řádek souboru protokolu obsahuje ukončovací kód.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Pokud zjistíte, **záporné** ukončovací kód, najdete [ukončovací kód tabulky](#azure-diagnostics-plugin-exit-codes) v [odkazuje na části](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostická data protokolována do úložiště Azure.
Určete, jestli se zobrazuje žádná data nebo některá data se zobrazí.

### <a name="diagnostics-infrastructure-logs"></a>Diagnostické protokoly infrastruktury
Diagnostika zapisují do protokolů infrastruktury diagnostiky všechny chyby. Zajistěte, aby povolili jste [sběr dat diagnostiky infrastruktury protokoly ve vaší konfiguraci](#how-to-check-diagnostics-extension-configuration). Pak můžete rychle vyhledat všechny relevantní chyby, které se zobrazují v `DiagnosticInfrastructureLogsTable` tabulky ve vašem účtu úložiště.

### <a name="no-data-is-appearing"></a>Žádná data
Data události vůbec neobjeví nejběžnější důvodem je, že informace o účtu úložiště je definována nesprávně.

Řešení: Opravte konfiguraci diagnostiky a znovu nainstalujte diagnostiky.

Pokud účet úložiště je správně nakonfigurovaná, vzdálený přístup do počítače a ověřte, zda jsou spuštěny DiagnosticsPlugin.exe a MonAgentCore.exe. Pokud neběží, postupujte podle kroků v [Azure Diagnostics není od](#azure-diagnostics-is-not-starting). 

Pokud procesů, které běží, přejděte na [je data získávání zaznamenaná místně?](#is-data-getting-captured-locally) a postupujte podle pokynů existuje.

### <a name="part-of-the-data-is-missing"></a>Chybí část dat
Pokud se zobrazuje některá data, ale ne všechny, znamená to, jestli kanálu kolekce nebo přenos dat správně nastavené. Postupujte podle zde témata můžete zúžit problém.

#### <a name="is-the-collection-configured"></a>Je nakonfigurované kolekce? 
Konfigurace diagnostiky obsahuje pokyny pro konkrétní typ dat, které se mají shromažďovat. [Zkontrolujte konfiguraci](#how-to-check-diagnostics-extension-configuration) k ověření, že pouze hledáte data, která jste nakonfigurovali pro kolekci.

#### <a name="is-the-host-generating-data"></a>Je hostitel generování dat?
- **Čítače výkonu**: Otevřete perfmon a zkontrolujte čítač.

- **Protokoly trasování**: vzdálený přístup do virtuálního počítače a přidejte TextWriterTraceListener konfiguračního souboru aplikace.  V tématu http://msdn.microsoft.com/library/sk36c28t.aspx nastavit naslouchacího procesu text.  Zajistěte, aby `<trace>` má element `<trace autoflush="true">`.<br />
Pokud nevidíte protokoly trasování generován, přečtěte si téma [více informací o protokoly trasování chybí](#more-about-trace-logs-missing).

- **Trasování událostí pro Windows trasování**: vzdálený přístup do virtuálního počítače a instalaci nástroje PerfView.  Nástroje PerfView, spusťte **soubor** > **uživatele příkaz** > **naslouchání etwprovder1** > **etwprovider2**a tak dále. **Naslouchání** příkaz je malá a velká písmena a nesmí být mezery mezi čárkami oddělený seznam zprostředkovatelů trasování událostí pro Windows. Pokud příkaz se nezdaří, můžete vybrat **protokolu** tlačítko v pravém dolním rohu nástroj nástroje Perfview co pokusil o spuštění a jaké výsledek byl.  Za předpokladu, že vstup je správná, otevře nové okno. Během pár sekund začnete zobrazuje trasování ETW.

- **Protokoly událostí**: vzdálený přístup do virtuálního počítače. Otevřete `Event Viewer`a pak se ujistěte, že existují události.

#### <a name="is-data-getting-captured-locally"></a>Je získávání dat zaznamenat místně?
Dále se ujistěte, že získávání zachycení místně.
Jsou data uložená místně v `*.tsf` soubory v [místní úložiště pro data diagnostiky](#log-artifacts-path). Různé druhy protokoly získat shromážděné v různých `.tsf` soubory. Názvy jsou podobné názvy tabulek ve službě Azure Storage. 

Například `Performance Counters` získat shromážděných v `PerformanceCountersTable.tsf`. Protokoly událostí získat shromažďují v `WindowsEventLogsTable.tsf`. Postupujte podle pokynů v [místního protokolu extrakce](#local-log-extraction) oddílu otevřete místního shromažďování souborů a ověřte, že je vidíte získávání shromážděných na disku.

Pokud nevidíte získávání místně shromážděné protokoly a již ověříte, že je hostitel generování dat, máte pravděpodobně problém konfigurace. Pečlivě zkontrolujte konfiguraci. 

Také zkontrolujte konfiguraci, která byla vygenerována MonitoringAgent [MaConfig.xml](#log-artifacts-path). Ověřte, zda je oddíl, který popisuje zdroj relevantní protokolu. Pak ověřte, že nedojde ke ztrátě v překlad mezi konfiguraci diagnostiky a monitorování konfigurace agenta.

#### <a name="is-data-getting-transferred"></a>Získávání přenosu dat?
Pokud jste ověřili, že získávání zachycení místně, ale přesto ho nevidíte ve vašem účtu úložiště, proveďte následující kroky: 

- Ověřte, zda jste zadali účet úložiště správný a že nebyly vráceny nad klíči pro účet daného úložiště. Pro Azure Cloud Services, někdy vidíme, že lidé Neaktualizovat `useDevelopmentStorage=true`.

- Ověřte, že účet zadaný úložiště správné. Ujistěte se, že nemáte síťové omezení, která komponenty zabránit v dosažení koncové body veřejné úložiště. Jeden způsob, jak to udělat je vzdálený přístup do počítače a potom se pokusíte zapsat si něco do stejného účtu úložiště sami.

- Nakonec můžete si prohlédnout jaké selhání se hlášených Agent sledování. Agent monitorování protokoly zapisují `maeventtable.tsf`, který se nachází v [místní úložiště pro data diagnostiky](#log-artifacts-path). Postupujte podle pokynů [místního protokolu extrakce](#local-log-extraction) části pro otevření tohoto souboru. Potom se pokusíte určí, jestli jsou `errors` které označují selhání čtení k místním souborům zápis do úložiště.

### <a name="capturing-and-archiving-logs"></a>Zaznamenání a archivaci protokoly
Pokud přemýšlíte o obrátíte na podporu, je první věcí, kterou může vás vyzvou shromažďování protokolů z vašeho počítače. Díky této sami můžete ušetřit čas. Spustit `CollectGuestLogs.exe` nástroj na [cesta protokolu kolekce nástroje](#log-artifacts-path). Vygeneruje zip soubor s všechny relevantní Azure protokoly ve stejné složce.

## <a name="diagnostics-data-tables-not-found"></a>Nebyl nalezen tabulky dat diagnostiky
Tabulky v úložišti Azure, které hostují události trasování událostí pro Windows jsou pojmenované pomocí následujícího kódu:

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
| Zprostředkovatel = "prov1" &lt;událost s id = "1" nebo&gt; |WADEvent + MD5("prov1") + "1" |
| Zprostředkovatel = "prov1" &lt;událost s id = "2" eventDestination = "dest1" /&gt; |WADdest1 |
| Zprostředkovatel = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| Zprostředkovatel = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt; |WADdest2 |

## <a name="references"></a>Odkazy

### <a name="how-to-check-diagnostics-extension-configuration"></a>Jak zkontrolovat konfiguraci rozšíření diagnostiky
Nejjednodušší způsob, jak Zkontrolujte konfiguraci rozšíření je přejít na [Průzkumníka prostředků Azure](http://resources.azure.com), a potom přejděte na virtuální počítač nebo cloudové služby, kde rozšíření diagnostiky Azure (IaaSDiagnostics / PaaDiagnostics) je.

Alternativně použijte vzdálenou plochu do počítače a podívejte se na Azure Diagnostics konfiguračního souboru, který je popsán v [protokolu část cesty artefakty](#log-artifacts-path).

Vyhledejte v obou případech **Microsoft.Azure.Diagnostics**a potom **xmlCfg** nebo **WadCfg** pole. 

Při hledání na virtuálním počítači a **WadCfg** pole je k dispozici, což znamená, že konfigurace je ve formátu JSON. Pokud **xmlCfg** pole je k dispozici, což znamená, konfigurace je v kódu XML a je kódováním base64. Budete muset [dekódovat](http://www.bing.com/search?q=base64+decoder) zobrazíte XML, který byl načteny diagnostiky.

Cloudové služby rolí, pokud vyberete konfiguraci z disku, data jsou kódováním base64, proto musíte [dekódovat](http://www.bing.com/search?q=base64+decoder) zobrazíte XML, který byl načteny diagnostiky.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics modulu plug-in ukončovací kód
Tento modul plug-in vrátí následující ukončovací kód:

| Ukončovací kód | Popis |
| --- | --- |
| 0 |Úspěch |
| -1 |Obecná chyba. |
| -2 |Nelze načíst soubor rcf.<p>Tato interní chyba by měla pouze dojít, pokud spouštěč modulu plug-in agenta hosta je vyvolána ručně nesprávně ve virtuálním počítači. |
| -3 |Nelze načíst konfigurační soubor diagnostiky.<p><p>Řešení: Způsobené konfigurační soubor není předávání ověřování schématu. Řešení je poskytnout konfiguračního souboru, který je v souladu se schématem. |
| -4 |Jiná instance agenta monitorování diagnostiky už používá adresář místních prostředků.<p><p>Řešení: Zadat jinou hodnotu pro **LocalResourceDirectory**. |
| -6 |Spouštěč hostovaného agenta modulu plug-in došlo k pokusu o spouštění diagnostiky neplatný příkazového řádku.<p><p>Tato interní chyba by měla pouze dojít, pokud spouštěč modulu plug-in agenta hosta je vyvolána ručně nesprávně ve virtuálním počítači. |
| -10 |Modul plug-in diagnostiky byl ukončen se k neošetřené výjimce. |
| -11 |Agentovi hosta se nepodařilo vytvořit proces zodpovědný za spouštění a monitorování agent sledování.<p><p>Řešení: Ověřte, že jsou k dispozici nové procesy dostatek systémových prostředků.<p> |
| -101 |Neplatné argumenty při volání modulu plug-in diagnostiky.<p><p>Tato interní chyba by měla pouze dojít, pokud spouštěč modulu plug-in agenta hosta je vyvolána ručně nesprávně ve virtuálním počítači. |
| -102 |Proces modulu plug-in se nepodařilo inicializovat.<p><p>Řešení: Ověřte, že jsou k dispozici nové procesy dostatek systémových prostředků. |
| -103 |Proces modulu plug-in se nepodařilo inicializovat. Konkrétně se nepodařilo vytvořit objekt protokolovacího nástroje.<p><p>Řešení: Ověřte, že jsou k dispozici nové procesy dostatek systémových prostředků. |
| -104 |Nelze načíst soubor rcf poskytované agenta hosta.<p><p>Tato interní chyba by měla pouze dojít, pokud spouštěč modulu plug-in agenta hosta je vyvolána ručně nesprávně ve virtuálním počítači. |
| -105 |Modul plug-in Diagnostics nelze otevřít soubor konfigurace diagnostiky.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud je modul plug-in diagnostiky je vyvolána ručně nesprávně ve virtuálním počítači. |
| -106 |Diagnostika konfigurační soubor nelze přečíst.<p><p>Způsobené konfigurační soubor není předávání ověřování schématu. <br><br>Řešení: Poskytněte konfiguračního souboru, který je v souladu se schématem. Další informace najdete v tématu [jak zkontrolovat konfiguraci rozšíření diagnostiky](#how-to-check-diagnostics-extension-configuration). |
| -107 |Pass directory prostředků pro agenta monitorování je neplatný.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud agenta monitorování je vyvolána ručně nesprávně ve virtuálním počítači.</p> |
| -108 |Nebylo možné převést konfiguračního souboru diagnostiku do konfiguračního souboru monitorování agenta.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud je modul plug-in diagnostiky je vyvolána ručně se souborem neplatná konfigurace. |
| -110 |Konfigurace diagnostiky obecné došlo k chybě.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud je modul plug-in diagnostiky je vyvolána ručně se souborem neplatná konfigurace. |
| -111 |Nepodařilo se spustit agent sledování.<p><p>Řešení: Ověřte, že jsou k dispozici dostatek systémových prostředků. |
| -112 |Obecná chyba |

### <a name="local-log-extraction"></a>Extrakce místního protokolu
Agent monitorování shromažďuje protokoly a artefaktů jako `.tsf` soubory. `.tsf` Soubor není možné číst, ale můžete ji do převést `.csv` následujícím způsobem: 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nový soubor s názvem `<relevantLogFile>.csv` je vytvořen v stejnou cestu jako odpovídající `.tsf` souboru.

>[!NOTE] 
> Stačí ke spouštění tohoto nástroje na hlavní .tsf souboru (například PerformanceCountersTable.tsf). Doprovodné soubory (například PerformanceCountersTables_\*\*001.tsf PerformanceCountersTables_\*\*002.tsf a tak dále) se automaticky zpracovávají.

### <a name="more-about-missing-trace-logs"></a>Další informace o chybí protokoly trasování 

>[!NOTE]
> Následující informace platí především pro Azure Cloud Services, pokud jste nakonfigurovali DiagnosticsMonitorTraceListener na aplikaci, která běží na virtuální počítač IaaS. 

- Zajistěte, aby **DiagnosticMonitorTraceListener** je nakonfigurované v souboru web.config nebo app.config.  Ta se nakonfiguruje ve výchozím nastavení v projekty cloudových služeb. Někteří zákazníci ale komentář ho na víc systémů, což způsobí, že příkazy trasování, které nelze shromažďovat diagnostiky. 

- Pokud protokoly získávání nezapisují z **OnStart** nebo **spustit** metoda, zajistěte, aby **DiagnosticMonitorTraceListener** je v souboru app.config.  Ve výchozím nastavení je v souboru web.config, ale které se týkají pouze kód spuštěný v rámci w3wp.exe. Proto ho musíte v souboru app.config zaznamenat trasování, které jsou spuštěny v WaIISHost.exe.

- Ujistěte se, že používáte **Diagnostics.Trace.TraceXXX** místo **Diagnostics.Debug.WriteXXX.** Příkazy ladění se odeberou ze sestavení pro vydání.

- Zajistěte, aby zkompilovaný kód má ve skutečnosti **Diagnostics.Trace řádky** (použijte Reflector, ildasm nebo ILSpy ověření). **Diagnostics.Trace** příkazy se odeberou z kompilované binárního souboru, pokud nechcete použít symbol Podmíněná kompilace trasování. Tento problém je běžný, k níž dojde při použití nástroje msbuild pro vytvoření projektu.   

## <a name="known-issues-and-mitigations"></a>Známé problémy a jejich zmírnění
Tady je seznam známých problémů s známé způsoby zmírnění rizik:

**1. rozhraní .NET 4.5 závislostí**

Windows Azure Diagnostics rozšíření má závislost na modul runtime na rozhraní .NET 4.5 nebo novější. V době psaní, všechny počítače, které jsou zřízené pro Azure Cloud Services, a také všechny oficiální bitové kopie, které jsou založeny na virtuálních počítačích Azure máte rozhraní .NET 4.5 nebo novější. 

Je stále možné nastat situace, kdy zkusíte spustit Windows Azure Diagnostics rozšíření na počítači, který nemá .NET 4.5 nebo novější. To se stane, když vytvoříte svůj počítač z původní bitové kopie nebo snímek, nebo když přineste vlastní disk.

To obvykle manifesty jako ukončovací kód **255** při spuštění **DiagnosticsPluginLauncher.exe.** Selhání kvůli následující nezpracované výjimce se stane toto: 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Omezení rizik:** nainstalovat rozhraní .NET 4.5 nebo novější na váš počítač.

**2. Data čítače výkonu je k dispozici v úložiště, ale nezobrazují se na portálu**

Práce s portálem ve virtuálních počítačích ukazuje některé čítače výkonu ve výchozím nastavení. Pokud nevidíte čítače výkonu a víte, že je získávání dat vygenerovat, protože je k dispozici v úložišti, zkontrolujte následující:

- Jestli dat v úložišti má názvy čítačů v angličtině. Pokud nejsou názvy čítačů v angličtině, nebude ho rozpoznat portálu metriky grafu.

- Pokud používáte zástupné znaky (\*) v názvech čítače výkonu, nebude moct korelovat čítač nakonfigurované a shromáždění portálu.

**Zmírnění dopadů**: Změna počítače jazyka na angličtinu pro účty systému. Chcete-li to provést, vyberte **ovládací panely** > **oblast** > **správy** > **nastavení kopií**. V dalším kroku zrušte výběr **Vítejte obrazovky a systém účty** tak, aby vlastní jazyk neplatí pro účet system. Ujistěte se, že nepoužíváte zástupné znaky, pokud chcete na portálu jako prostředí primární spotřeba také.
