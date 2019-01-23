---
title: Řešení potíží s rozšíření Azure Diagnostics
description: Řešení problémů při použití diagnostiky Azure v Azure Virtual Machines, Service Fabric a Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/12/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 305aa28127e453c01de9b55ab6cb0ff3471afad9
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473805"
---
# <a name="azure-diagnostics-troubleshooting"></a>Řešení potíží diagnostiky Azure
Tento článek popisuje informace o odstraňování potíží, které se týkají pomocí Azure Diagnostics. Další informace o diagnostice Azure najdete v tématu [přehled Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logické součásti
**Modul plug-in Spouštěč diagnostiky (DiagnosticsPluginLauncher.exe)**: Spustí rozšíření Azure Diagnostics. Slouží jako položku bodu procesu.

**Modul plug-in diagnostiky (DiagnosticsPlugin.exe)**: Nakonfiguruje, spouští a spravuje životnost agenta monitorování. Je hlavní proces, který je spouštěn spouštěč.

**Monitoring Agent (vypnutí Monagenta\*procesy .exe)**: Monitoruje, shromažďuje a přenese diagnostická data.  

## <a name="logartifact-paths"></a>Cesty protokolu/artefaktů
Následující části jsou cesty pro některé důležité protokoly a artefakty. Označujeme k těmto informacím v celé zbývající části dokumentu.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Cesta |
| --- | --- |
| **Azure diagnostického konfiguračního souboru** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **Soubory protokolu** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Místní úložiště pro diagnostická data** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **Konfigurační soubor agenta monitorování** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Balíček rozšíření Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Cesta nástroje protokolu kolekce** | %SystemDrive%\Packages\GuestAgent\ |
| **Soubor protokolu MonAgentHost** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>Virtuální počítače
| Artefakt | Cesta |
| --- | --- |
| **Azure diagnostického konfiguračního souboru** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **Soubory protokolu** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Místní úložiště pro diagnostická data** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **Konfigurační soubor agenta monitorování** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Stav souboru** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Balíček rozšíření Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Cesta nástroje protokolu kolekce** | C:\WindowsAzure\Packages |
| **Soubor protokolu MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Data metriky se už nebude na webu Azure portal
Diagnostika Azure poskytuje data metriky, které lze zobrazit na webu Azure Portal. Pokud máte problémy se zobrazováním dat na portálu, zkontrolujte, WADMetrics\* tabulky v účtu úložiště diagnostiky Azure, abyste zjistili, pokud odpovídající záznamy metriky jsou k dispozici.

Tady **PartitionKey** tabulky je prostředek ID, virtuálního počítače nebo virtuálního počítače škálovací sady. **RowKey** je název metriky (označované také jako název čítače výkonu).

Pokud ID prostředku je nesprávná, zkontrolujte **diagnostiky** **konfigurace** > **metriky** > **ResourceId**zobrazíte, pokud je ID prostředku nastavena správně.

Pokud nejsou žádná data pro konkrétní metrika, zkontrolujte **konfiguraci diagnostiky** > **PerformanceCounter** chcete zjistit, jestli metriku (čítače výkonu). Ve výchozím nastavení jsme povolili následující čítače:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- Aplikace \ASP.NET (__celkový__) \Requests/Sec
- Aplikace \ASP.NET (__celkový__) \Errors celkem/s
- \ASP.NET\Requests zařazených do fronty
- \ASP.NET\Requests zamítnuto
- \Processor(w3wp)\% Processor Time
- \Process(w3wp)\Private Bytes
- \Process(WaIISHost)\% času procesoru
- Bajty \Private \Process (WaIISHost)
- \Process(WaWorkerHost)\% času procesoru
- Bajty \Private \Process (WaWorkerHost)
- \Memory\Page chyby/s
- \.NET CLR paměti (_globální_)\% času v uvolňování paměti
- \LogicalDisk (C:) \Disk zapsané bajty/s
- \Disk \LogicalDisk (C:) přečtené bajty/s
- (D:) \LogicalDisk \Disk zapsané bajty/s
- \Disk \LogicalDisk (D:) přečtené bajty/s

Je-li konfigurace nastavena správně, ale stále nevidíte data metriky, použijte následující pokyny při řešení potíží s.


## <a name="azure-diagnostics-isnt-starting"></a>Nespouští diagnostiky Azure
Informace o tom, proč se nepodařilo spustit diagnostiku Azure, najdete v článku **DiagnosticsPluginLauncher.log** a **DiagnosticsPlugin.log** soubory v umístění souborů protokolu, který byl dříve.

Pokud tyto protokoly označení `Monitoring Agent not reporting success after launch`, to znamená, že došlo k chybě spuštění MonAgentHost.exe. Podívejte se na protokoly v umístění, které je uvedené pro `MonAgentHost log file` v předchozí části.

Poslední řádek souboru protokolu obsahuje ukončovací kód.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Pokud narazíte **záporné** ukončovací kód, přečtěte si [ukončovací kód tabulky](#azure-diagnostics-plugin-exit-codes) v [odkazuje oddíl](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostická data se protokolují do služby Azure Storage
Určete, jestli žádná data se povolí, nebude nebo některá data se zobrazuje.

### <a name="diagnostics-infrastructure-logs"></a>Protokoly infrastruktury diagnostiky
Diagnostické protokoly všechny chyby v diagnostických protokolů infrastruktury. Ujistěte se, že jste povolili [zachycení diagnostické infrastruktury protokolů v konfiguraci](#how-to-check-diagnostics-extension-configuration). Pak můžete rychle vyhledat všechny relevantní chyby, které se zobrazují v `DiagnosticInfrastructureLogsTable` tabulka ve vašem účtu úložiště.

### <a name="no-data-is-appearing"></a>Žádná data
Nejčastější příčinou, které vůbec neobjeví data událostí je, že informace o účtu úložiště je nesprávně definovaný.

Řešení: Opravit konfiguraci diagnostiky a znovu nainstalujte diagnostiky.

Pokud účet úložiště je správně nakonfigurované, vzdálený přístup k počítači a ověřte, zda jsou spuštěny DiagnosticsPlugin.exe a MonAgentCore.exe. Pokud neběží, postupujte podle kroků v [Azure Diagnostics nespouští](#azure-diagnostics-is-not-starting).

Pokud jsou spuštěné procesy, přejděte na [data získávání zachytávají v místním prostředí?](#is-data-getting-captured-locally) a postupujte podle pokynů.

### <a name="part-of-the-data-is-missing"></a>Chybí část dat
Pokud se některá data ale ne pro všechny, znamená to, že je správně nastavena datovým kanálem kolekce/přenos. Postupujte podle zde témata zúžit problém.

#### <a name="is-the-collection-configured"></a>Je nakonfigurovaný kolekci?
Konfigurace diagnostiky obsahuje pokyny pro určitý typ dat, které se mají shromažďovat. [Zkontrolujte konfiguraci](#how-to-check-diagnostics-extension-configuration) k ověření, že jenom hledáte data, která jste nakonfigurovali pro kolekci.

#### <a name="is-the-host-generating-data"></a>Hostitele generuje data?
- **Čítače výkonu**: Otevřete perfmon a čítač.

- **Protokoly trasování**:  Vzdálený přístup k virtuálnímu počítači a přidejte TextWriterTraceListener do konfiguračního souboru aplikace.  Zobrazit https://msdn.microsoft.com/library/sk36c28t.aspx nastavení naslouchacího procesu text.  Ujistěte se, `<trace>` element má `<trace autoflush="true">`.<br />
Pokud nevidíte protokoly trasování se generuje, přečtěte si téma [Další informace o protokolech trasování chybí](#more-about-trace-logs-missing).

- **Trasování ETW**: Vzdálený přístup k virtuálnímu počítači a nainstalujte nástroje PerfView.  V PerfView, spusťte **souboru** > **uživatelský příkaz** > **naslouchání etwprovder1** > **etwprovider2**, a tak dále. **Naslouchání** příkaz je velká a malá písmena a nesmí být mezery mezi čárkou oddělený seznam zprostředkovatelů trasování událostí pro Windows. Příkaz selže-li, můžete vybrat **protokolu** tlačítko v pravém dolním rohu nástroje Perfview naleznete v tématu co proběhl pokus o spuštění a jaké výsledkem bylo.  Za předpokladu, že je správný vstup, otevře nové okno. Během několika sekund můžete začít zobrazení trasování ETW.

- **Protokoly událostí**: Vzdálený přístup k virtuálnímu počítači. Otevřít `Event Viewer`a poté se ujistěte, že existují události.

#### <a name="is-data-getting-captured-locally"></a>Je dat získávání zachytávají v místním prostředí?
V dalším kroku zkontrolujte, zda že je získat data zachytávají v místním prostředí.
Jsou data uložená místně v `*.tsf` soubory [místní úložiště pro diagnostická data](#log-artifacts-path). Získat shromažďují různé druhy protokoly v různých `.tsf` soubory. Názvy se podobají názvy tabulek ve službě Azure Storage.

Například `Performance Counters` získat shromážděných v `PerformanceCountersTable.tsf`. Získání protokolů událostí shromažďovaných `WindowsEventLogsTable.tsf`. Postupujte podle pokynů v [místního protokolu extrakce](#local-log-extraction) oddílu otevřete soubory místního shromažďování dat a ověřte, jestli je získání shromažďují na disku.

Pokud nevidíte získávání místně shromažďovaných protokolů a už jste ověřili, že hostitel generuje data, máte pravděpodobně problém konfigurace. Pečlivě zkontrolujte konfiguraci.

Projděte si také konfigurace, který byl vygenerován pro MonitoringAgent [MaConfig.xml](#log-artifacts-path). Ověřte, zda je oddíl, který popisuje zdroj relevantní protokolu. Ověřte, že není ztraceno v překladu mezi konfiguraci diagnostiky a konfiguraci agenta monitorování.

#### <a name="is-data-getting-transferred"></a>Získávání přenosu dat?
Pokud jste ověřili, že data se získávání zachytávají v místním prostředí, ale stále nevidíte ji v účtu úložiště, proveďte následující kroky:

- Ověřte, že jste zadali účet úložiště správné a že nebyly převracet klíče pro daný účet úložiště. Pro Azure Cloud Services, někdy vidíme, že lidé Neaktualizovat `useDevelopmentStorage=true`.

- Ověřte, zda je účet úložiště zadaný správný. Ujistěte se, že nemáte omezení sítě, které součásti zabránit v dosažení koncové body veřejného úložiště. Jedním ze způsobů, který je pro vzdálený přístup k počítači a zkuste psát do stejného účtu úložiště sami.

- A konečně můžete si prohlédnout jaké chyby byly hlášeny pomocí agenta monitorování. Agent monitorování zapsalo protokolům `maeventtable.tsf`, který se nachází v [místní úložiště pro diagnostická data](#log-artifacts-path). Postupujte podle pokynů [místního protokolu extrakce](#local-log-extraction) části pro otevření tohoto souboru. Potom se pokuste určete, jestli jsou `errors` označující selhání pro čtení k místním souborům zápis do úložiště.

### <a name="capturing-and-archiving-logs"></a>Zaznamenávání a archivace protokoly
Pokud přemýšlíte o kontaktovat podporu, je první věcí, kterou můžete být požádáni shromažďování protokolů z vašeho počítače. Tímto způsobem, že sami, můžete ušetřit čas. Spustit `CollectGuestLogs.exe` nástroj na [cesta nástroje protokolu kolekce](#log-artifacts-path). Generuje soubor .zip soubor s všechny příslušné Azure protokoly ve stejné složce.

## <a name="diagnostics-data-tables-not-found"></a>Tabulky diagnostiky dat nebyl nalezen
Pomocí následujícího kódu se s názvem tabulky ve službě Azure storage, které obsahují události trasování událostí pro Windows:

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
Tento kód vytvoří čtyři tabulky:

| Událost | Název tabulky |
| --- | --- |
| Zprostředkovatel = "prov1" &lt;událost s id = "1" /&gt; |WADEvent + MD5("prov1") + "1" |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| Zprostředkovatel = "prov1" &lt;DefaultEvents /&gt; |WADDefault+MD5("prov1") |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>Odkazy

### <a name="how-to-check-diagnostics-extension-configuration"></a>Návod k ověření konfigurace diagnostického rozšíření
Nejjednodušší způsob, jak zkontrolovat konfiguraci rozšíření je přejít k [Azure Resource Exploreru](http://resources.azure.com), a potom přejděte na virtuální počítače nebo cloudové služby where rozšíření Azure Diagnostics (IaaSDiagnostics / PaaDiagnostics) je.

Alternativně použijte vzdálenou plochu do počítače a podívejte se, která je popsána v souboru konfigurace diagnostiky Azure [protokolu část cesty artefaktů](#log-artifacts-path).

V obou případech vyhledejte **Microsoft.Azure.Diagnostics**a potom **xmlCfg** nebo **WadCfg** pole.

Pokud hledáte na virtuálním počítači a **WadCfg** pole je k dispozici, znamená to konfigurace je ve formátu JSON. Pokud **xmlCfg** pole je k dispozici, znamená to konfigurace je ve formátu XML a je s kódováním base64. Je potřeba [dekódovat](https://www.bing.com/search?q=base64+decoder) zobrazíte XML, který byl načten pomocí diagnostiky.

Role cloudové služby, pokud vybrat konfigurace z disku, data je kódováním base64, proto musíte [dekódovat](https://www.bing.com/search?q=base64+decoder) zobrazíte XML, který byl načten pomocí diagnostiky.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure Diagnostics modulu plug-in ukončovací kódy
Modul plug-in vrátí následující kódy ukončení:

| Ukončovací kód | Popis |
| --- | --- |
| 0 |Úspěch |
| -1 |Obecná chyba. |
| -2 |Nepovedlo se načíst soubor rcf.<p>Tato interní chyba by měla pouze dojít, pokud spuštění modulu plug-in agenta hosta je vyvolána ručně nesprávně na virtuálním počítači. |
| -3 |Nelze načíst konfigurační soubor diagnostiky.<p><p>Řešení: Způsobené konfigurační soubor není předávání ověřování schématu. Řešením je poskytnout konfigurační soubor, který odpovídá schématu. |
| -4 |Adresář místního prostředku už používá jiná instance agenta monitorování diagnostiky.<p><p>Řešení: Zadejte jinou hodnotu pro **LocalResourceDirectory**. |
| -6 |Spouštěč hostovaného agenta modulu plug-in došlo k pokusu o spuštění diagnostiky se Neplatný příkazový řádek.<p><p>Tato interní chyba by měla pouze dojít, pokud spuštění modulu plug-in agenta hosta je vyvolána ručně nesprávně na virtuálním počítači. |
| -10 |Modul plug-in diagnostiky se ukončil s neošetřenou výjimku. |
| -11 |Agent hosta se nepodařilo vytvořit zodpovědný za spouštění a sledování agenta monitorování procesu.<p><p>Řešení: Ověřte, že dostatečné systémové prostředky jsou dostupné nové procesy.<p> |
| -101 |Neplatné argumenty při volání metody pluginu diagnostiky.<p><p>Tato interní chyba by měla pouze dojít, pokud spuštění modulu plug-in agenta hosta je vyvolána ručně nesprávně na virtuálním počítači. |
| -102 |Proces modulu plug-in se nepodařilo inicializovat.<p><p>Řešení: Ověřte, že dostatečné systémové prostředky jsou dostupné nové procesy. |
| -103 |Proces modulu plug-in se nepodařilo inicializovat. Konkrétně nelze vytvořit objekt protokolovacího nástroje.<p><p>Řešení: Ověřte, že dostatečné systémové prostředky jsou dostupné nové procesy. |
| -104 |Nepovedlo se načíst soubor rcf poskytované agenta hosta.<p><p>Tato interní chyba by měla pouze dojít, pokud spuštění modulu plug-in agenta hosta je vyvolána ručně nesprávně na virtuálním počítači. |
| -105 |Modul plug-in diagnostiky nelze otevřít konfigurační soubor diagnostiky.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud je modul plug-in Diagnostika je vyvolána ručně nesprávně na virtuálním počítači. |
| -106 |Nelze přečíst konfigurační soubor diagnostiky.<p><p>Způsobené konfigurační soubor není předávání ověřování schématu. <br><br>Řešení: Zadejte konfigurační soubor, který je v souladu se schématem. Další informace najdete v tématu [návod k ověření konfigurace diagnostického rozšíření](#how-to-check-diagnostics-extension-configuration). |
| -107 |Průchodu adresáře prostředků monitorovacích agentů je neplatný.<p><p>Tato interní chyba by mohlo dojít pouze v Pokud je agent monitorování je vyvolána ručně nesprávně na virtuálním počítači.</p> |
| -108 |Nepodařilo se převést Konfigurační soubor diagnostiky do monitorování konfigurační soubor agenta.<p><p>Tato interní chyba by měla pouze dojít, pokud modul plug-in diagnostiky ručně vyvolání neplatný konfigurační soubor. |
| -110 |Konfigurace obecného diagnostiky došlo k chybě.<p><p>Tato interní chyba by měla pouze dojít, pokud modul plug-in diagnostiky ručně vyvolání neplatný konfigurační soubor. |
| -111 |Nepovedlo se spustit agenta monitorování.<p><p>Řešení: Ověřte, zda jsou k dispozici dostatečné systémové prostředky. |
| -112 |Obecná chyba |

### <a name="local-log-extraction"></a>Extrakce místního protokolu
Agent monitorování shromažďuje protokoly a artefakty jako `.tsf` soubory. `.tsf` Souboru není čitelný, ale může převést na `.csv` následujícím způsobem:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nový soubor s názvem `<relevantLogFile>.csv` se vytvoří ve stejnou cestu jako odpovídající `.tsf` souboru.

>[!NOTE]
> Stačí spustit tento nástroj .tsf hlavní soubor (například PerformanceCountersTable.tsf). Přiložené soubory (například PerformanceCountersTables_\*\*001.tsf PerformanceCountersTables_\*\*002.tsf a tak dále) jsou automaticky zpracovány.

### <a name="more-about-missing-trace-logs"></a>Další informace o chybějících protokoly trasování

>[!NOTE]
> Následující informace platí především pro Azure Cloud Services, pokud jste nakonfigurovali DiagnosticsMonitorTraceListener na aplikaci, která běží na vašem virtuálním počítači IaaS.

- Ujistěte se, **DiagnosticMonitorTraceListener** je nakonfigurovaný v souboru web.config nebo app.config.  Nakonfiguruje se ve výchozím nastavení projekty cloudových služeb. Nicméně, někteří zákazníci komentář na více instancí, což způsobí, že příkazy trasování, které nebudou shromažďovat diagnostické nástroje.

- Pokud nejsou získávání zapsány protokoly z **OnStart** nebo **spustit** metoda, ujistěte se, že **DiagnosticMonitorTraceListener** v app.config.  Ve výchozím nastavení je v souboru web.config, ale které se týkají pouze pro kód spuštěný v rámci w3wp.exe. Proto ho musíte v souboru app.config trasování, které jsou spuštěny v WaIISHost.exe.

- Ujistěte se, že používáte **Diagnostics.Trace.TraceXXX** místo **Diagnostics.Debug.WriteXXX.** Příkazy ladění se odeberou ze sestavení pro vydání.

- Ujistěte se, že ve skutečnosti obsahuje zkompilovaný kód **Diagnostics.Trace řádky** (použijte Reflector, ildasm nebo ILSpy ověření). **Diagnostics.Trace** příkazy jsou odebrány z kompilované binární soubor, pokud nechcete použít symbol podmíněné kompilace trasování. Toto je běžný problém, ke které dojde při použití nástroje msbuild k sestavení projektu.   

## <a name="known-issues-and-mitigations"></a>Známé problémy a jejich zmírnění
Tady je seznam známých problémů pomocí známých omezení rizik:

**1. .NET 4.5 závislostí**

Rozšíření Windows Azure Diagnostics má závislost na modulu runtime v rozhraní .NET 4.5 nebo novější. V době psaní, všech počítačů, které jsou zřízené pro Azure Cloud Services, stejně jako všechny oficiálních imagích, které jsou založeny na virtuálních počítačích Azure máte .NET 4.5 nebo novější.

Je stále možné nastat situace, kdy pokusu o spuštění rozšíření Windows Azure Diagnostics na počítači, který nemá rozhraní .NET 4.5 nebo novější. K tomu dochází při vytváření vašeho počítače z původní obrázek nebo snapshot, nebo když použijete vlastní disku.

To obvykle manifesty jako ukončovací kód **255** při spuštění **DiagnosticsPluginLauncher.exe.** Dojde k selhání z důvodu následující neošetřené výjimky:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Omezení rizik:** Instalace rozhraní .NET 4.5 nebo novější na váš počítač.

**2. Data čítačů výkonu je k dispozici v úložišti, ale nezobrazují se na portálu**

Prostředí portálu na virtuálních počítačích ukazuje některé čítače výkonu ve výchozím nastavení. Pokud nevidíte čítače výkonu a víte, že se získávání dat vygenerovat, protože je k dispozici v úložišti, zkontrolujte následující:

- Určuje, zda data ve službě storage mají názvy čítačů v angličtině. Pokud nejste názvy čítačů v angličtině, nebude moci rozpoznat portálu grafu metriky. **Zmírnění dopadů**: Změňte strojového jazyka na angličtinu pro systémové účty. Chcete-li to provést, vyberte **ovládací panely** > **oblasti** > **správy** > **Kopírovat nastavení**. V dalším kroku zrušte výběr **úvodní obrazovka a systémové účty** tak, aby vlastní jazyka neplatí pro systémový účet.

- Pokud použijete zástupné znaky (\*) v názvech čítače výkonu, nebude možnost provést korelaci nakonfigurované a shromáždění čítačů při čítače výkonu se odesílají do jímky Azure Storage na portálu. **Zmírnění dopadů**: Abyste měli jistotu, můžete použít zástupné znaky a mít na portálu rozbalte (\*), čítače výkonu chcete směrovat [jímka "Azure monitoru"](diagnostics-extension-schema.md#diagnostics-extension-111).

