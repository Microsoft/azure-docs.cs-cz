---
title: Propojit řešení dat v Log Analytics | Microsoft Docs
description: Data kabelové sítě je konsolidované sítě a výkon data z počítačů s agenty OMS, včetně nástroje Operations Manager a agenti připojená k systému Windows. Data sítě spolu s daty protokolu ke korelaci data.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: d824272f5b5569971eddcf0a43bd5ba97f60f506
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Řešení přenosu dat 2.0 (Preview) v analýzy protokolů

![Přenosová datový symbol](./media/log-analytics-wire-data/wire-data2-symbol.png)

Data kabelové sítě je konsolidované sítě a výkon data shromážděná z připojená k systému Windows a Linux připojené počítače s agentem OMS, včetně těch, které sledovaných nástrojem Operations Manager ve vašem prostředí. Data sítě spolu s další data protokolu ke korelaci data.

Kromě agenta OMS řešení přenosu dat používá Microsoft agenti závislost, kterou si nainstalujete do počítače ve vaší infrastruktuře IT. Závislost agenti monitorují síťová data odesílaná do a z vašich počítačů pro síť úrovně 2 – 3 [OSI model](https://en.wikipedia.org/wiki/OSI_model), včetně různých protokoly a porty používané. Data se pak posílají do analýzy protokolů pomocí agentů.  

> [!NOTE]
> V předchozí verzi řešení přenosu dat nelze přidat do nové pracovní prostory. Pokud máte v původním řešení Data kabelové sítě povolené, můžete ji použít. Však použít přenosu dat 2.0, je nutné nejprve odebrat původní verze.

Ve výchozím nastavení analýzy protokolů protokoluje data pro využití procesoru, paměti, disku a údaje o výkonu sítě z čítače, které jsou součástí Windows a Linux, jakož i další čítače výkonu, které můžete zadat. Sítě a jiných shromažďování dat se provádí v v reálném čase pro každého agenta, včetně podsítě a úrovni aplikace protokoly používá pro počítač.  Data kabelové sítě vypadá na síťová data na úrovni aplikace, není dolů v přenosové vrstvě TCP.  Řešení není podívejte se na jednotlivé ACK a požadavky SYN.  Metoda handshake po ukončení, je považován za dynamické připojení a označena jako připojeno. Že zůstane připojení za provozu, dokud na obou stranách souhlas soketu je otevřený a dat můžete předat a zpět.  Po obou stranách uzavře připojení, je označena jako odpojeno.  Proto počítá jenom šířku pásma úspěšně dokončila paketů, se neoznamuje na znovu odešle nebo se nezdařilo paketů.

Pokud jste použili [sFlow](http://www.sflow.org/) nebo jiný software s [společnosti Cisco NetFlow protokol](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), pak bude povědomé statistiky a data se zobrazí z data kabelové sítě.

Některé typy předdefinované dotazy vyhledávání protokolu patří:

- Agenti poskytující data kabelové sítě
- IP adresy agentů poskytujících data kabelové sítě
- Odchozí komunikace podle IP adresy
- Počet bajtů odeslaných protokoly aplikací
- Počet bajtů odeslaných služba aplikace
- Počet přijatých bajtů podle různé protokoly
- Celkový počet bajtů odeslaných a přijatých podle verze protokolu IP
- Průměrná latence pro připojení, které byly spolehlivě
- Počítač, aby iniciovaly nebo přijaly síťový provoz zpracovává
- Objem síťového přenosu pro zpracování

Při hledání pomocí data kabelové sítě, můžete filtrovat a data skupiny k zobrazení informací o nejvyšší agentů a horní protokoly. Nebo můžete zobrazit, kdy některé počítače (IP adresy MAC adresy) přenášená mezi sebou, jak dlouho a kolik data byla odeslána – v zásadě platí, zobrazit metadata o síťovém provozu, který je na základě hledání.

Ale vzhledem k tomu, že zobrazíte metadata, není nutně užitečné pro odstraňování potíží. Data kabelové sítě v Log Analytics není úplná sběru dat v síti.  Rozhraní není určeno pro řešení potíží s hloubky úrovni paketů. Výhodou použití agenta ve srovnání s jinými metodami kolekci, je, že nemusíte instalovat zařízení, konfigurovat síťové přepínače nebo preform složitá konfigurace. Data kabelové sítě je jednoduše založené na agentovi – nainstalujte agenta na počítači a monitorovat vlastní síťový provoz. Další výhodou je, když chcete monitorovat úlohy běžící v cloudu poskytovatelů nebo poskytovatel hostitelských služeb nebo Microsoft Azure, kde uživatel nemá vlastní vrstvě prostředků infrastruktury.

## <a name="connected-sources"></a>Připojené zdroje

Data kabelové sítě získává data od agenta nástroje Microsoft závislostí. Agent závislostí závisí na agenta OMS pro připojení k analýze protokolů. To znamená, že server musí mít agenta OMS nainstalovaný a nakonfigurovaný nejprve a pak nainstalujte agenta závislostí. Následující tabulka popisuje připojených zdrojů, které podporuje řešení Data kabelové sítě.

| **Připojené zdroje** | **Podporuje se** | **Popis** |
| --- | --- | --- |
| Agenti systému Windows | Ano | Data kabelové sítě analyzuje a shromažďuje data z počítače se systémem Windows agenta. <br><br> Kromě [agenta OMS](log-analytics-windows-agent.md), Agent služby Microsoft Dependency vyžadují agentů v systému Windows. Najdete v článku [podporované operační systémy](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) úplný seznam verzí operačního systému. |
| Agenti systému Linux | Ano | Data kabelové sítě analyzuje a shromažďuje data z počítače se systémem Linux agent.<br><br> Kromě [agenta OMS](log-analytics-quick-collect-linux-computer.md), agenty Linux vyžadují Microsoft Agent závislostí. Najdete v článku [podporované operační systémy](../monitoring/monitoring-service-map-configure.md#supported-operating-systems) úplný seznam verzí operačního systému. |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Analyzuje Data kabelové sítě a shromažďuje data z agentů systému Windows a Linux v připojeného [skupiny pro správu System Center Operations Manager](log-analytics-om-agents.md). <br><br> Je nutné přímé připojení z počítače agenta System Center Operations Manager k analýze protokolů. K analýze protokolů se předají data ze skupiny pro správu. |
| Účet služby Azure Storage | Ne | Data kabelové sítě shromažďuje data z počítačů agentů, takže není žádná data z něj shromažďovat ze služby Azure Storage. |

V systému Windows Microsoft Monitoring Agent (MMA) použít System Center Operations Manager a analýzy protokolů shromažďovat a odesílat data. V závislosti na kontextu se nazývá agenta agenta System Center Operations Manager, OMS Agent, Agent analýzy protokolů, MMA nebo přímé agenta. System Center Operations Manager a analýzy protokolů poskytují mírně různé verze MMA. Tyto verze lze každou sestavu System Center Operations Manager, analýzy protokolů nebo do obou.

V systému Linux OMS agenta pro Linux shromažďuje a odesílá data k analýze protokolů. Data kabelové sítě můžete použít na serverech s agenty přímé OMS nebo na servery, které jsou připojené k analýze protokolů prostřednictvím skupin pro správu System Center Operations Manager.

V tomto článku, odkazuje na všechny agenty, zda Linux nebo Windows, ať už připojené ke skupině pro správu System Center Operations Manager nebo přímo k Log Analytics se říká _agenta OMS_. Název konkrétní nasazení agenta použijeme jenom v případě, že je potřeba pro kontext.

Agent závislostí nepřenáší samotná data a nevyžaduje žádné změny brány firewall nebo porty. Data v Data kabelové sítě vždy přenášená agentem OMS k analýze protokolů, buď přímo nebo pomocí brány OMS.

![diagram agenta](./media/log-analytics-wire-data/agents.png)

Pokud jste uživatele System Center Operations Manager, který má skupinu pro správu připojené k analýze protokolů:

- Pokud agenty nástroje System Center Operations Manager můžete přístup k Internetu, aby se připojení k analýze protokolů, není nutná žádná další konfigurace.
- Budete muset nakonfigurovat bránu OMS fungovat s nástrojem System Center Operations Manager, když agenty nástroje System Center Operations Manager nelze získat přístup k analýze protokolů přes Internet.

Pokud používáte přímé Agent, musíte nakonfigurovat agenta OMS připojit se k analýze protokolů nebo k bráně OMS. Si můžete stáhnout z brány OMS [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Požadavky

- Vyžaduje [přehledy a analýzy](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) nabídka řešení.
- Pokud používáte předchozí verzi řešení Data kabelové sítě, je nutné ji odebrat. Všechna data zaznamenaná v původní Data kabelové sítě řešení je ale stále k dispozici v přenosu dat 2.0 a hledání protokolů.
- Pro instalaci nebo odinstalaci agenta závislosti jsou vyžadována oprávnění správce.
- Závislost agenta musí být nainstalován na počítači s 64bitový operační systém.

### <a name="operating-systems"></a>Operační systémy

Následující části uvádějí podporované operační systémy pro agenta závislostí. Data kabelové sítě nepodporuje 32bitové architektury pro všechny operační systémy.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Plocha Windows

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux a Oracle Linux (s RHEL jádra)

- Podporovány jsou pouze výchozí a verze SMP Linux jádra.
- Nestandardní jádra uvolní, například PAE a Xen, nejsou podporovány pro všechny distribuci systému Linux. Například systém s řetězec verze _2.6.16.21-0.8-xen_ není podporován.
- Vlastní jádra, včetně opakovaných kompilací standardní jádra, nejsou podporovány.
- CentOSPlus jádra není podporována.
- Oracle nedělitelné Enterprise jádra (UEK) je popsaná v další části tohoto článku.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398 <br> 2.6.18-400 <br>2.6.18-402 <br>2.6.18-404 <br>2.6.18-406 <br> 2.6.18-407 <br> 2.6.18-408 <br> 2.6.18-409 <br> 2.6.18-410 <br> 2.6.18-411 <br> 2.6.18-412 <br> 2.6.18-416 <br> 2.6.18-417 <br> 2.6.18-419 |

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Linux Enterprise s nedělitelné Enterprise jádra

#### <a name="oracle-linux-6"></a>Oracle Linux 6

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10

| **Verze operačního systému** | **Verze jádra** |
| --- | --- |
| 10 SP4 | 2.6.16.60 |

#### <a name="dependency-agent-downloads"></a>Agent služby Dependency soubory ke stažení

| **File** | **OS** | **Verze** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfigurace

Proveďte následující postup pro konfiguraci řešení Data kabelové sítě pro vaše pracovní prostory.

1. Povolit řešení analýzy protokolů aktivity z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Nainstalujte agenta závislost na každém počítači, ve které chcete načíst data. Závislost agenta můžete monitorovat připojení k okamžité Sousedé BGP, proto musíte nemusí agenta na každý počítač.

### <a name="install-the-dependency-agent-on-windows"></a>Nainstalujte agenta závislostí v systému Windows

Pro instalaci nebo odinstalaci agenta jsou vyžadována oprávnění správce.

Je závislost Agent nainstalován v počítačích se systémem Windows prostřednictvím InstallDependencyAgent Windows.exe. Pokud spustíte tento spustitelný soubor, bez jakékoli možnosti, spustí průvodce, který vám pomůžou při interaktivní instalaci.

Použijte následující kroky pro instalaci agenta závislost na každém počítači se systémem Windows:

1. Nainstalovat agenta OMS kroků v [shromažďovat data z počítače se systémem Windows hostované ve vašem prostředí](log-analytics-windows-agent.md).
2. Stáhnout agenta pro Windows závislostí pomocí odkazu v předchozí části a spusťte jej pomocí následujícího příkazu: `InstallDependencyAgent-Windows.exe`
3. Postupujte podle pokynů průvodce k instalaci agenta.
4. Pokud Agent služby Dependency se nepodaří spustit, zkontrolujte protokoly podrobné informace o chybě. Pro agenty se systémem Windows k adresáři protokolu není %Programfiles%\Microsoft Agent\logs závislostí.

#### <a name="windows-command-line"></a>Příkazový řádek systému Windows

Nainstalujte z příkazového řádku pomocí možnosti z v následující tabulce. Pokud chcete zobrazit seznam příznaky instalace, spusťte instalační program pomocí /? Příznak následujícím způsobem.

InstallDependencyAgent Windows.exe /?

| **Příznak** | **Popis** |
| --- | --- |
| <code>/?</code> | Získejte seznam možností příkazového řádku. |
| <code>/S</code> | Proveďte bezobslužnou instalaci s žádné uživatelské výzvy. |

Soubory pro Windows Agent závislosti jsou umístěny v Agent služby Dependency C:\Program Files\Microsoft ve výchozím nastavení.

### <a name="install-the-dependency-agent-on-linux"></a>Nainstalujte agenta závislostí v systému Linux

Kořenový přístup je nutný k instalaci nebo konfiguraci agenta.

Agent závislostí je nainstalován na počítače se systémem Linux prostřednictvím InstallDependencyAgent-Linux64.bin, skript prostředí s samorozbalující binární. Soubor můžete spustit pomocí _dílet_ nebo přidejte oprávnění ke samotném souboru.

Použijte následující kroky pro instalaci agenta závislost na každý počítač se systémem Linux:

1. Nainstalovat agenta OMS kroků v [shromažďovat data z počítače se systémem Linux hostované ve vašem prostředí](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Stáhnout agenta závislostí Linux pomocí odkazu v předchozí části a potom ji nainstalovat jako kořenového adresáře pomocí následujícího příkazu: dílet InstallDependencyAgent Linux64.bin
3. Pokud Agent služby Dependency se nepodaří spustit, zkontrolujte protokoly podrobné informace o chybě. V agentech Linux, k adresáři protokolu není: /var/opt/microsoft/dependency-agent/log.

Pokud chcete zobrazit seznam příznaky instalace, spusťte instalační program s `-help` příznak následujícím způsobem.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Příznak** | **Popis** |
| --- | --- |
| <code>-help</code> | Získejte seznam možností příkazového řádku. |
| <code>-s</code> | Proveďte bezobslužnou instalaci s žádné uživatelské výzvy. |
| <code>--check</code> | Zkontrolujte oprávnění a operační systém, ale není nainstalovaný agent. |

Soubory pro agenta závislosti jsou umístěny v adresáři pro následující:

| **Soubory** | **Umístění** |
| --- | --- |
| Soubory jádra | /OPT/Microsoft/Dependency-Agent |
| Soubory protokolu | /var/OPT/Microsoft/Dependency-Agent/log |
| Konfigurační soubory | /ETC/OPT/Microsoft/Dependency-Agent/config |
| Služby spustitelné soubory | /OPT/Microsoft/Dependency-Agent/Bin/Microsoft-Dependency-Agent<br><br>/OPT/Microsoft/Dependency-Agent/Bin/Microsoft-Dependency-Agent-Manager |
| Úložiště binární soubory | /var/OPT/Microsoft/Dependency-Agent/Storage |

### <a name="installation-script-examples"></a>Příklady skriptů instalace

Chcete-li snadno nasadit agenta závislosti na počtu serverů najednou, je dobré pomocí skriptu. Následující příklady skriptu můžete použít ke stažení a instalaci závislostí agenta v systému Windows nebo Linux.

#### <a name="powershell-script-for-windows"></a>Skript prostředí PowerShell pro systém Windows

```PowerShell

Invoke-WebRequest &quot;https://aka.ms/dependencyagentwindows&quot; -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Skript prostředí pro Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Konfigurace požadovaného stavu

Nasazení agenta nástroje závislostí prostřednictvím konfigurace požadovaného stavu, můžete použít modul xPSDesiredStateConfiguration a bit kódu takto:

```
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = &quot;C:\InstallDependencyAgent-Windows.exe&quot;



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = &quot;https://aka.ms/dependencyagentwindows&quot;

        DestinationPath = $DAPackageLocalPath

        DependsOn = &quot;[Package]OI&quot;

    }

    xPackage DA

    {

        Ensure=&quot;Present&quot;

        Name = &quot;Dependency Agent&quot;

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = &quot;&quot;

        InstalledCheckRegKey = &quot;HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent&quot;

        InstalledCheckRegValueName = &quot;DisplayName&quot;

        InstalledCheckRegValueData = &quot;Dependency Agent&quot;

    }

}

```
### <a name="uninstall-the-dependency-agent"></a>Odinstalujte agenta závislostí

Pomocí následujících částí můžete odebrat agenta závislostí.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalujte agenta závislostí v systému Windows

Správce můžete odinstalovat závislostí agenta pro Windows pomocí ovládacích panelů.

Správce můžete také spouštět %Programfiles%\Microsoft závislostí Agent\Uninstall.exe odinstalace agenta závislostí.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalujte agenta závislostí v systému Linux

Úplně odinstalujte agenta závislostí ze systému Linux, je nutné odebrat vlastní agent a konektor, který je automaticky nainstalován s agentem. Můžete odinstalovat i pomocí následujících jeden příkaz:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Sady Management Pack

Po aktivaci Data kabelové sítě v pracovním prostoru analýzy protokolů 300 KB management pack je odeslány na všechny servery Windows v něm. Pokud používáte System Center Operations Manager agentů v [připojené skupiny pro správu](log-analytics-om-agents.md), z System Center Operations Manager je nasazena sada management pack monitorování závislostí. Pokud jsou připojeny přímo agentů, analýzy protokolů přináší sadu management pack.

Sada management pack je s názvem Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Je zapsán do: %Programfiles%\Microsoft monitorování Agent\Agent\Health služby State\Management balíčky. Zdroj dat, který používá sada management pack je: % Program files%\Microsoft monitorování Agent\Agent\Health služby State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Použití řešení

**Instalace a konfigurace řešení**

Použijte následující informace k instalaci a konfiguraci řešení.

- Řešení Data kabelové sítě operace čtení dat z počítačů se systémem Windows Server 2012 R2, Windows 8.1 a novější operační systémy.
- Na počítačích, ve které chcete získat data kabelové sítě z se vyžaduje rozhraní Microsoft .NET Framework 4.0 nebo novější.
- Přidat řešení přenosu dat do pracovního prostoru analýzy protokolů pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md). Není nutná žádná další konfigurace.
- Pokud chcete zobrazit data kabelové sítě pro konkrétní řešení, budete muset mít řešení již přidán do pracovního prostoru.

Po mají nainstalovat agenti a nainstalovat řešení, dlaždice 2.0 přenosu dat se zobrazí v pracovním prostoru.

![Dlaždice Data kabelové sítě](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Pomocí řešení přenosu dat 2.0

Na portálu OMS, klikněte **přenosu dat 2.0** dlaždici otevřete řídicí panel Data kabelové sítě. Řídicí panel obsahuje okna v následující tabulce. Každý okno uvádí až 10 položky odpovídající kritériím tohoto okna pro zadaný obor a časový rozsah. Můžete spustit vyhledávání protokolu, který vrátí všechny záznamy kliknutím **zobrazit všechny** v dolní části okna, nebo kliknutím na záhlaví okna.

| **Blade** | **Popis** |
| --- | --- |
| Agenti zachytávající síťový přenos | Zobrazuje počet agentů, kteří jsou zachytávání síťového provozu a uvádí top 10 počítačů, které jsou zachycení provozu. Klikněte na číslo ke spuštění protokolu vyhledejte <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Klikněte na počítač, v seznamu ke spuštění vyhledávání protokolu vrátí celkový počet bajtů zaznamenat. |
| Místní podsítě | Zobrazuje počet místní podsítě, které byly zjištěny agenty.  Klikněte na číslo ke spuštění protokolu vyhledejte <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> , obsahuje seznam všech podsítí s počet bajtů odeslaných přes každé z nich. Klikněte na podsíť v seznamu ke spuštění vyhledávání protokolu vrátí celkový počet bajtů odeslaných přes podsíť. |
| Protokoly na úrovni aplikace | Zobrazuje počet protokoly na úrovni aplikace používána, při zjištění agenty. Klikněte na číslo ke spuštění protokolu vyhledejte <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Klikněte na protokol spuštění vyhledávání protokolu vrátí celkový počet bajtů odeslaných pomocí protokolu. |

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Řídicí panel přenosu dat](./media/log-analytics-wire-data/wire-data-dash.png)

Můžete použít **agenti zachytávající síťový přenos** okno a určit, jaký poměr šířky pásma sítě je spotřebovávanou počítače. Toto okno vám může pomoci snadno najít _chattiest_ počítač ve vašem prostředí. Tyto počítače může být přetížený, funguje neobvyklým způsobem, nebo pomocí více síťových prostředků, než normální.

![Příklad protokolu vyhledávání](./media/log-analytics-wire-data/log-search-example01.png)

Podobně můžete použít **místní podsítě** okno a zjistit, kolik síťový provoz přesunutí prostřednictvím podsítě. Uživatelé jsou často definovat podsítě kolem důležité oblasti pro své aplikace. Toto okno nabízí zobrazení do těchto oblastí.

![Příklad protokolu vyhledávání](./media/log-analytics-wire-data/log-search-example02.png)

**Protokoly na úrovni aplikace** okno je užitečné, protože je užitečné vědět, co protokoly jsou používány. Například by se dalo očekávat SSH, zda se nepoužívá v prostředí vaší sítě. Zobrazení informací, které jsou k dispozici v okně můžete rychle potvrďte nebo disprove vaše očekávání.

![Příklad protokolu vyhledávání](./media/log-analytics-wire-data/log-search-example03.png)

V tomto příkladu může přejít k podrobnostem podrobností SSH a zjistit, které počítače používají SSH a mnoho dalších podrobností o komunikaci.

![Zo výsledky hledání](./media/log-analytics-wire-data/ssh-details.png)

Je také užitečné vědět, pokud je provozu protokolu zvýšením nebo snížením v čase. Například pokud roste množství dat přenášených aplikací, který může být něco, co byste měli vědět, nebo že můžete zjistit pozoruhodné.

## <a name="input-data"></a>Vstupní data

Data kabelové sítě shromažďuje metadata o síťovém provozu pomocí agentů, které jste povolili. Každý agent odesílá data o každých 15 sekund.

## <a name="output-data"></a>Výstupní data

Záznam s typem _WireData_ se vytvoří pro každý typ vstupní data. WireData záznamy mají vlastnosti zobrazené v následující tabulce:

| Vlastnost | Popis |
|---|---|
| Počítač | Název počítače, kde nebyla shromážděna data |
| TimeGenerated | Čas záznamu |
| LocalIP | IP adresa místního počítače |
| SessionState | Připojení nebo odpojení |
| ReceivedBytes | Počet přijatých bajtů |
| ProtocolName | Název sítě protokol použitý |
| Parametr IPVersion | Verze protokolu IP |
| Směr | Příchozí nebo odchozí |
| MaliciousIP | IP adresa známé škodlivé zdroje |
| Závažnost | Závažnost možného malwaru |
| RemoteIPCountry | Země vzdálené IP adresy |
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager |
| SourceSystem | Zdroj, kde nebyla shromážděna data |
| SessionStartTime | Čas spuštění relace |
| SessionEndTime | Čas ukončení relace |
| LocalSubnet | Podsíť, kde nebyla shromážděna data |
| LocalPortNumber | Číslem místního portu |
| Vzdálená adresa IP | Vzdálené IP adresy používané vzdáleného počítače |
| RemotePortNumber | Číslo portu použité podle vzdálené IP adresy |
| ID relace | Jednoznačná hodnota, která identifikuje relace komunikace mezi dvě IP adresy |
| SentBytes | Počet bajtů odeslaných |
| TotalBytes | Celkový počet bajtů odeslaných během relace |
| ApplicationProtocol | Typ protokolu sítě používá   |
| ID procesu | ID procesu systému Windows |
| Název_procesu | Cesta a název souboru procesu |
| RemoteIPLongitude | Zeměpisná délka IP |
| RemoteIPLatitude | Zeměpisná šířka IP |


## <a name="next-steps"></a>Další postup

- [V protokolech Hledat](log-analytics-log-searches.md) zobrazíte podrobné přenosu dat vyhledávání záznamů.
