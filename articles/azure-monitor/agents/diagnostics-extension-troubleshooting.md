---
title: Řešení potíží s rozšířením Azure Diagnostics
description: Řešení potíží při použití diagnostiky Azure v Azure Virtual Machines, Service Fabric nebo Cloud Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 431b19595fbe2f5bc1f989e712c9c104af8e839b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711514"
---
# <a name="azure-diagnostics-troubleshooting"></a>Řešení potíží se službou Azure Diagnostics
Tento článek popisuje informace o odstraňování potíží, které souvisí s používáním Azure Diagnostics. Další informace o diagnostice Azure najdete v tématu [přehled Azure Diagnostics](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logické součásti
**Spouštěč modulu plug-in diagnostiky (DiagnosticsPluginLauncher.exe)**: spustí rozšíření Azure Diagnostics. Slouží jako proces vstupního bodu.

**Modul plug-in diagnostiky (DiagnosticsPlugin.exe)**: konfiguruje, spouští a spravuje dobu života agenta monitorování. Jedná se o hlavní proces, který spouští spouštěč.

**Agent monitorování ( \* procesy MonAgent. exe)**: monitoruje, shromažďuje a převádí diagnostická data.  

## <a name="logartifact-paths"></a>Cesty log/artefaktů
Níže jsou uvedeny cesty k některým důležitým protokolům a artefaktům. Na tyto informace odkazujeme během zbývající části dokumentu.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Cesta |
| --- | --- |
| **Konfigurační soubor Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Soubory protokolu** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Místní úložiště pro diagnostická data** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> .. DiagnosticStore\WAD0107\Tables |
| **Konfigurační soubor agenta monitorování** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Balíček rozšíření Azure Diagnostics** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Cesta k nástroji pro shromažďování protokolů** | %SystemDrive%\Packages\GuestAgent\ |
| **Soubor protokolu MonAgentHost** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> .. DiagnosticStore\WAD0107\Configuration\MonAgentHost. <seq_num>. log |

### <a name="virtual-machines"></a>Virtuální počítače
| Artefakt | Cesta |
| --- | --- |
| **Konfigurační soubor Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Soubory protokolu** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Místní úložiště pro diagnostická data** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tables |
| **Konfigurační soubor agenta monitorování** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Stavový soubor** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Balíček rozšíření Azure Diagnostics** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Cesta k nástroji pro shromažďování protokolů** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **Soubor protokolu MonAgentHost** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost. <seq_num>. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Data metrik se nezobrazují na webu Azure Portal
Azure Diagnostics poskytuje data metrik, která se dají zobrazit v Azure Portal. Pokud máte problémy se zobrazením dat na portálu, zkontrolujte v \* tabulce WADMetrics v účtu úložiště Azure Diagnostics, jestli jsou k dispozici odpovídající záznamy metriky, a ujistěte se, že je [poskytovatel prostředků](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft. Insights zaregistrovaný.

Tady je **PartitionKey** tabulky ID prostředku, virtuální počítač nebo sada škálování virtuálních počítačů. **RowKey** je název metriky (označuje se také jako název čítače výkonu).

Pokud je ID prostředku nesprávné, zkontrolujte metriky **Konfigurace** **diagnostiky**  >    >  **ResourceID** a zjistěte, jestli je ID prostředku správně nastavené.

Pokud nejsou k dispozici žádná data pro specifickou metriku, zkontrolujte **konfiguraci diagnostiky**  >  **PerformanceCounter** a zjistěte, jestli je zahrnutá metrika (čítač výkonu). Ve výchozím nastavení povolíme následující čítače:
- \Processor(_Total)\% Processor Time
- \Memory\Available Bytes
- \ ASP.NET aplikace (__celkem__) \ požadavky za sekundu
- \ ASP.NET aplikace (__celkem__) \Errors celkem/s
- \ASP.NET\Requests ve frontě
- \ASP.NET\Requests zamítnuto
- Čas procesoru \Processor (W3wp) \%
- \Process (W3wp) \Private bajty
- Čas procesoru \Process (WaIISHost) \%
- \Process (WaIISHost) \Private bajty
- Čas procesoru \Process (WaWorkerHost) \%
- \Process (WaWorkerHost) \Private bajty
- \Memory\Page chyby za sekundu
- \.ČISTÝ čas CLR paměti (_globální_) \% v GC
- \ Logický disk (C:) \ zapsané bajty/s
- \ Logický disk (C:) \ čtení v bajtech/s
- \ Logický disk (D:) \ zapsané bajty/s
- \ Logický disk (D:) \ čtení v bajtech/s

Pokud je konfigurace správně nastavená, ale stále nevidíte data metriky, použijte následující pokyny, které vám pomůžou při odstraňování potíží.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics nejde spustit
Informace o tom, proč se Azure Diagnostics nepovedlo spustit, najdete v souborech protokolu **DiagnosticsPluginLauncher. log** a **DiagnosticsPlugin. log** v umístění souborů protokolu, které jste zadali dříve.

Pokud tyto protokoly signalizují `Monitoring Agent not reporting success after launch` , znamená to, že při spuštění MonAgentHost.exe došlo k chybě. Podívejte se na protokoly v umístění, které je uvedené `MonAgentHost log file` v předchozí části.

Poslední řádek souborů protokolu obsahuje ukončovací kód.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Pokud najdete **záporný** ukončovací kód, přečtěte si [tabulku ukončovací kód](#azure-diagnostics-plugin-exit-codes) v [části REFERENCES](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostická data se neprotokolují do služby Azure Storage
Určí, zda se nezobrazí žádná data, nebo se zobrazí některá z dat.

### <a name="diagnostics-infrastructure-logs"></a>Protokoly diagnostické infrastruktury
Diagnostika protokoluje všechny chyby v protokolech diagnostické infrastruktury. Ujistěte se, že jste povolili [zachytávání protokolů diagnostické infrastruktury ve vaší konfiguraci](#how-to-check-diagnostics-extension-configuration). Pak můžete rychle vyhledat všechny relevantní chyby, které se zobrazí v `DiagnosticInfrastructureLogsTable` tabulce v nakonfigurovaném účtu úložiště.

### <a name="no-data-is-appearing"></a>Nezobrazují se žádná data.
Nejčastějším důvodem, proč se data události nezobrazí, je, že informace o účtu úložiště jsou nesprávně definovány.

Řešení: Opravte konfiguraci diagnostiky a přeinstalujte diagnostiku.

Pokud je účet úložiště správně nakonfigurovaný, vzdálený přístup k počítači a ověřte, jestli jsou spuštěné *DiagnosticsPlugin.exe* a *MonAgentCore.exe* . Pokud nejsou spuštěné, postupujte podle kroků v části [Azure Diagnostics nespouští se](#azure-diagnostics-is-not-starting).

Pokud jsou procesy spuštěné, přejděte na data, která [se zaznamenávají místně?](#is-data-getting-captured-locally) , a postupujte podle pokynů.

Pokud se tím problém nevyřeší, zkuste:

1. Odinstalace agenta
2. Odebrat adresář C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Nainstalovat agenta znovu


### <a name="part-of-the-data-is-missing"></a>Chybí část dat.
Pokud získáváte nějaká data, ale ne všechny, znamená to, že kanál shromažďování/přenosu dat je správně nastavený. Pokud chcete tento problém zúžit, postupujte podle částí v části.

#### <a name="is-the-collection-configured"></a>Je kolekce nakonfigurovaná?
Konfigurace diagnostiky obsahuje pokyny pro shromáždění konkrétního typu dat, která se mají shromáždit. [Zkontrolujte konfiguraci](#how-to-check-diagnostics-extension-configuration) a ověřte, zda jste hledali pouze data, která jste pro kolekci nakonfigurovali.

#### <a name="is-the-host-generating-data"></a>Je hostitel generuje data?
- **Čítače výkonu**: Spusťte nástroj perfmon a podívejte se na čítač.

- **Protokoly trasování**: vzdálený přístup k virtuálnímu počítači a přidání TextWriterTraceListener do konfiguračního souboru aplikace.  Další informace najdete v tématu https://msdn.microsoft.com/library/sk36c28t.aspx Nastavení naslouchacího procesu textu.  Ujistěte se, že `<trace>` element má `<trace autoflush="true">` .<br />
Pokud nevidíte vygenerované protokoly trasování, přečtěte si další informace o chybějících protokolech trasování.

- **Trasování událostí pro Windows**: vzdálený přístup k virtuálnímu počítači a instalace PerfView.  V PerfView spusťte příkaz **File**  >  **User**  >  ,**Poslouchejte etwprovder1**  >  **etwprovider2** a tak dále. Příkaz **Listen** rozlišuje velká a malá písmena a mezi čárkami oddělený seznam zprostředkovatelů trasování událostí pro Windows nemůže být mezera. Pokud se příkaz nepovede spustit, můžete vybrat tlačítko **protokolu** v pravém dolním rohu nástroje PerfView, abyste viděli, co se pokusilo spustit, a jaký byl výsledek.  Za předpokladu, že je vstup správný, otevře se nové okno. Během několika sekund začnete zobrazovat trasování ETW.

- **Protokoly událostí**: vzdálený přístup k virtuálnímu počítači. Otevřete `Event Viewer` a pak ověřte, zda existují události.

#### <a name="is-data-getting-captured-locally"></a>Jsou data zachycena lokálně?
Potom se ujistěte, že se data budou zaznamenávat místně.
Data se ukládají místně do `*.tsf` souborů v místním úložišti pro diagnostická data. Různé druhy protokolů se shromažďují v různých `.tsf` souborech. Názvy jsou podobné názvům tabulek v Azure Storage.

Příklad `Performance Counters` : Seznámení se zachytí v `PerformanceCountersTable.tsf` . Protokoly událostí se shromažďují v `WindowsEventLogsTable.tsf` . Pomocí pokynů v části [extrakce místního protokolu](#local-log-extraction) otevřete soubory místní kolekce a ověřte, že se na disku zobrazují shromážděná.

Pokud nevidíte protokoly shromážděné místně a již ověříte, že hostitel generuje data, pravděpodobně máte problém s konfigurací. Pečlivě si prohlédněte konfiguraci.

Zkontrolujte také konfiguraci, která byla vygenerována pro MonitoringAgent MaConfig.xml. Ověřte, zda existuje část popisující příslušný zdroj protokolu. Pak ověřte, že se neztratil při překladu mezi konfigurací diagnostiky a konfigurací agenta monitorování.

#### <a name="is-data-getting-transferred"></a>Probíhá přenos dat?
Pokud jste ověřili, že se data budou zaznamenávat lokálně, ale v účtu úložiště je stále nevidíte, proveďte následující kroky:

- Ověřte, že jste zadali správný účet úložiště a že jste nemuseli zavádět klíče pro daný účet úložiště. V případě Azure Cloud Services se někdy uvidí, že se lidé neaktualizují `useDevelopmentStorage=true` .

- Ověřte, že zadaný účet úložiště je správný. Ujistěte se, že nemáte omezení sítě, která zabraňují komponentám dosáhnout veřejných koncových bodů úložiště. To uděláte tak, že se k počítači přidělíte vzdáleným přístupem a pak se pokusíte něco napsat do stejného účtu úložiště sami.

- Nakonec se můžete podívat, jaké chyby agent monitorování hlásí. Agent monitorování zapíše svoje protokoly v `maeventtable.tsf` umístění, které se nachází v místním úložišti pro diagnostická data. Postupujte podle pokynů v části [extrakce místního protokolu](#local-log-extraction) pro otevření tohoto souboru. Pak se pokuste zjistit, jestli existují `errors` chyby při čtení do místního souboru zapisujících do úložiště.

### <a name="capturing-and-archiving-logs"></a>Zachytávání a archivace protokolů
Pokud si myslíte, že se obrátíte na podporu, první věc, kterou si můžete vyžádat, je shromažďovat protokoly z počítače. Můžete ušetřit čas tím, že to uděláte sami. Spusťte `CollectGuestLogs.exe` Nástroj v cestě k nástroji shromažďování protokolů. Vygeneruje soubor. zip se všemi relevantními protokoly Azure ve stejné složce.

## <a name="diagnostics-data-tables-not-found"></a>Tabulky diagnostických dat se nenašly
Tabulky v úložišti Azure, které uchovávají události ETW, se nazývají pomocí následujícího kódu:

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

Tady je příklad:

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
| Provider = "Prov1" &lt; událost ID = "1"/&gt; |WADEvent + MD5 ("Prov1") + "1" |
| Provider = "Prov1" &lt; událost ID = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "Prov1" &lt; DefaultEvents/&gt; |WADDefault + MD5 ("Prov1") |
| Provider = "prov2" &lt; DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Reference

### <a name="how-to-check-diagnostics-extension-configuration"></a>Postup kontroly konfigurace rozšíření diagnostiky
Nejjednodušší způsob, jak ověřit konfiguraci rozšíření, je přejít na [Azure Resource Explorer](https://resources.azure.com)a pak přejít na virtuální počítač nebo na cloudovou službu, kde je rozšíření Azure Diagnostics (IaaSDiagnostics/PaaDiagnostics).

Alternativně se do počítače zobrazí Vzdálená plocha a Prohlédněte si konfigurační soubor Azure Diagnostics, který je popsaný v části cesta artefaktů protokolů.

V obou případech vyhledejte **Microsoft. Azure. Diagnostics** a pak pro pole **xmlCfg** nebo **WadCfg** .

Pokud hledáte na virtuálním počítači a pole **WadCfg** je k dispozici, znamená to, že konfigurace je ve formátu JSON. Pokud je přítomné pole **xmlCfg** , znamená to, že konfigurace je v kódu XML a má kódování Base64. Je nutné [ho dekódovat](https://www.bing.com/search?q=base64+decoder) , aby se zobrazil kód XML, který byl načten diagnostikou.

Pokud v případě role cloudové služby zvolíte konfiguraci z disku, jsou data zakódovaná ve formátu base64, takže je budete muset [dekódovat](https://www.bing.com/search?q=base64+decoder) , aby se zobrazil kód XML, který byl načten diagnostikou.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Ukončovací kódy modulu plug-in Azure Diagnostics
Modul plug-in vrátí následující ukončovací kódy:

| Ukončovací kód | Popis |
| --- | --- |
| 0 |Úspěch. |
| -1 |Obecná chyba |
| -2 |Nepovedlo se načíst soubor RCF.<p>Tato vnitřní chyba by se měla nacházet pouze v případě, že je ve virtuálním počítači ručně vyvolán spouštěč spouštěče agenta hosta. |
| -3 |Nelze načíst konfigurační soubor diagnostiky.<p><p>Řešení: způsobené konfiguračním souborem neprošlým ověřením schématu. Řešením je poskytnout konfigurační soubor, který odpovídá schématu. |
| -4 |Jiná instance diagnostiky agenta monitorování již používá místní adresář prostředků.<p><p>Řešení: Zadejte jinou hodnotu pro **LocalResourceDirectory**. |
| -6 |Spouštěč agenta hostovaného modulu plug-in se pokusil spustit diagnostiku s neplatným příkazovým řádkem.<p><p>Tato vnitřní chyba by se měla nacházet pouze v případě, že je ve virtuálním počítači ručně vyvolán spouštěč spouštěče agenta hosta. |
| -10 |Modul plug-in diagnostiky se ukončil s neošetřenou výjimkou. |
| -11 |Agent hosta nemohl vytvořit proces, který je zodpovědný za spuštění a monitorování agenta monitorování.<p><p>Řešení: Ověřte, že jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů.<p> |
| -101 |Neplatné argumenty při volání diagnostického modulu plug-in.<p><p>Tato vnitřní chyba by se měla nacházet pouze v případě, že je ve virtuálním počítači ručně vyvolán spouštěč spouštěče agenta hosta. |
| -102 |Proces modulu plug-in se nemůže inicializovat sám sebe.<p><p>Řešení: Ověřte, že jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů. |
| -103 |Proces modulu plug-in se nemůže inicializovat sám sebe. Konkrétně není možné vytvořit objekt protokolovacího nástroje.<p><p>Řešení: Ověřte, že jsou k dispozici dostatečné systémové prostředky pro spuštění nových procesů. |
| -104 |Nepovedlo se načíst soubor RCF, který poskytuje Agent hosta.<p><p>Tato vnitřní chyba by se měla nacházet pouze v případě, že je ve virtuálním počítači ručně vyvolán spouštěč spouštěče agenta hosta. |
| -105 |Modul plug-in pro diagnostiku nemůže otevřít konfigurační soubor diagnostiky.<p><p>Tato vnitřní chyba by se měla nacházet jenom v případě, že se modul plug-in na virtuálním počítači ručně vyvolal nesprávně. |
| -106 |Konfigurační soubor diagnostiky nelze přečíst.<p><p>Příčinou je konfigurační soubor, který neprošel ověřením schématu. <br><br>Řešení: zadejte konfigurační soubor, který odpovídá schématu. Další informace najdete v tématu [postup kontroly konfigurace rozšíření diagnostiky](#how-to-check-diagnostics-extension-configuration). |
| -107 |Adresář prostředků předávaný agentovi monitorování je neplatný.<p><p>K této vnitřní chybě by mělo dojít pouze v případě, že je agent monitorování na virtuálním počítači ručně vyvolán nesprávně.</p> |
| -108 |Konfigurační soubor diagnostiky nelze převést na konfigurační soubor agenta monitorování.<p><p>Tato vnitřní chyba by se měla provést, jenom když se modul plug-in diagnostiky spustí ručně s neplatným konfiguračním souborem. |
| -110 |Obecná chyba konfigurace diagnostiky<p><p>Tato vnitřní chyba by se měla provést, jenom když se modul plug-in diagnostiky spustí ručně s neplatným konfiguračním souborem. |
| -111 |Nelze spustit agenta monitorování.<p><p>Řešení: Ověřte, že jsou k dispozici dostatečné systémové prostředky. |
| -112 |Obecná chyba |

### <a name="local-log-extraction"></a>Extrakce místního protokolu
Agent monitorování shromažďuje protokoly a artefakty jako `.tsf` soubory. `.tsf`Soubor nelze přečíst, ale lze jej převést na `.csv` následující:

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
Nový soubor s názvem `<relevantLogFile>.csv` se vytvoří ve stejné cestě jako odpovídající `.tsf` soubor.

>[!NOTE]
> Tento nástroj je nutné spustit pouze proti hlavnímu souboru. TSF (například PerformanceCountersTable. TSF). Doprovodné soubory (například PerformanceCountersTables_ \* \* 001. tsf, PerformanceCountersTables_ \* \* 002. TSF atd.) jsou automaticky zpracovány.

### <a name="more-about-missing-trace-logs"></a>Další informace o chybějících protokolech trasování

>[!NOTE]
> Následující informace platí hlavně pro Azure Cloud Services, pokud jste nenakonfigurovali DiagnosticsMonitorTraceListener na aplikaci, která běží na vašem VIRTUÁLNÍm počítači s IaaS.

- Ujistěte se, že je **DiagnosticMonitorTraceListener** nakonfigurovaný v web.config nebo app.config.  To je ve výchozím nastavení nakonfigurováno v projektech cloudové služby. Někteří zákazníci je však přihlásili, což způsobí, že příkazy TRACE nebudou shromažďovány diagnostikou.

- Pokud protokoly nenačítáte z metody **OnStart** nebo **Run** , ujistěte se, že je **DiagnosticMonitorTraceListener** v app.config.  Ve výchozím nastavení je to web.config, ale platí pouze pro kód spuštěný v rámci w3wp.exe. Takže ho budete potřebovat v app.config, abyste mohli zachytit trasování, která běží v WaIISHost.exe.

- Ujistěte se, že používáte **Diagnostics. Trace. TraceXXX** namísto **Diagnostics. Debug. WriteXXX.** Příkazy ladění jsou odebrány z buildu pro vydání.

- Ujistěte se, že zkompilovaný kód ve skutečnosti obsahuje **diagnostické a sledovací řádky** (k ověření použijte reflektor, Ildasm nebo ILSpy). Příkazy **Diagnostics. Trace** jsou odebrány ze zkompilovaného binárního souboru, pokud nepoužijete symbol trasování podmíněné kompilace. Toto je běžný problém, ke kterému dochází při použití nástroje MSBuild k sestavení projektu.   

## <a name="known-issues-and-mitigations"></a>Známé problémy a jejich zmírnění
Tady je seznam známých problémů se známými riziky:

**1. závislost .NET 4,5**

Rozšíření Windows Azure Diagnostics má závislost za běhu v rozhraní .NET 4,5 Framework nebo novějším. V době psaní musí být na všech počítačích, které jsou zřízené pro Azure Cloud Services, i na všech oficiálních imagí založených na virtuálních počítačích Azure nainstalované rozhraní .NET 4,5 nebo novější.

Stále se může stát, že se pokusíte spustit rozšíření Windows Azure Diagnostics na počítači, který nemá rozhraní .NET 4,5 nebo novější. K tomu dojde, když vytvoříte počítač ze starého obrázku nebo snímku nebo když přenesete vlastní disk.

Tato všeobecně manifestuje jako ukončovací kód **255** při spuštění **DiagnosticsPluginLauncher.exe.** K selhání dochází kvůli následující neošetřené výjimce:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Omezení rizik:** Nainstalujte na svém počítači .NET 4,5 nebo novější.

**2. data čítačů výkonu jsou k dispozici v úložišti, ale nezobrazují se na portálu.**

Prostředí portálu ve virtuálních počítačích zobrazuje určité čítače výkonu ve výchozím nastavení. Pokud nevidíte čítače výkonu a víte, že se data generují, protože jsou k dispozici v úložišti, zkontrolujte následující:

- Určuje, jestli data v úložišti mají názvy čítačů v angličtině. Pokud názvy čítačů nejsou v angličtině, graf metriky portálu ho nedokáže rozpoznat. **Omezení rizik**: změňte jazyk počítače na angličtinu pro systémové účty. Provedete to tak, že vyberete oblast **ovládacích panelů**  >    >    >  **nastavení kopírování** pro správu. Pak zrušte výběr **uvítací obrazovky a systémových účtů** , aby se vlastní jazyk nepoužíval na účet System.

- Pokud ve svém názvu čítače výkonu používáte zástupné znaky ( \* ), portál nebude moci korelovat nakonfigurovaný a shromážděný čítač, když jsou čítače výkonu odesílány do Azure Storage jímky. **Zmírnění**: abyste měli jistotu, že budete používat zástupné znaky a chcete, aby portál rozšířil ( \* ), směrujte čítače výkonu do Azure monitor jímky.
