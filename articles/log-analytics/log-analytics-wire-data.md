---
title: Řešení Wire Data ve službě Log Analytics | Microsoft Docs
description: Při přenosu dat se konsolidované síti a výkonu data z počítačů s agenty Log Analytics. Kvůli pomoci při korelaci dat se síťová data kombinují s daty protokolu.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: ae34cc869dfb286a5a60f59fdab8733f611a6ec7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712156"
---
# <a name="wire-data-20-preview-solution-in-log-analytics"></a>Řešení Wire Data 2.0 (Preview) ve službě Log Analytics

![Symbol Wire Data](./media/log-analytics-wire-data/wire-data2-symbol.png)

Při přenosu dat se konsolidované síti a výkonu data shromážděná z počítače připojeného k Windows a Linux připojené pomocí agenta Log Analytics, včetně těch, které monitorovány nástrojem Operations Manager ve vašem prostředí. Kvůli pomoci při korelaci dat se síťová data kombinují s jinými daty protokolu.

Kromě agenta Log Analytics řešení Wire Data používá Microsoft Agents závislostí, který nainstalujete na počítačích ve vaší infrastruktuře IT. Závislí agenti monitorují síťová data odesílaná do a z počítačů na úrovních sítě 2–3 v [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), a to včetně různých použitých protokolů a portů. Data se pak do služby Log Analytics posílají pomocí agentů.  

>[!NOTE]
>Pokud jste už nasadili řešení Service Map, nebo zvažuje Service Map nebo [monitorování Azure pro virtuální počítače](../azure-monitor/insights/vminsights-overview.md), je nové připojení metriky sady dat shromažďovat a ukládat ve službě Log Analytics, která poskytuje stejné informace s Wire Data.

Služba Log Analytics standardně protokoluje výkonnostní data procesoru, paměti, disku a sítě z čítačů integrovaných do Windows a Linuxu, stejně jako z jiných čítačů výkonu, které určíte. Shromažďování síťových a jiných dat probíhá u každého agenta v reálném čase včetně podsítí a protokolů na úrovni aplikace, které počítač používá.  Řešení Wire Data sleduje síťová data na úrovni aplikace, nikoli v přenosové vrstvě TCP.  Toto řešení nesleduje individuální signály ACK a SYN.  Po dokončení metody handshake se připojení považuje za aktivní a označí se jako Připojeno. Toto připojení zůstává aktivní, dokud obě strany souhlasí, že je soket otevřený, a data mohou proudit tam a zpět.  Jakmile některá stana připojení zavře, označí se jako Odpojeno.  Proto se počítá jen šířka pásma úspěšně dokončených paketů a neoznamují se opětovně odeslané nebo neúspěšné pakety.

Pokud používáte [sFlow](http://www.sflow.org/) nebo jiný software s [protokolem NetFlow společnosti Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), budou vám statistiky a údaje dat kabelové sítě povědomé.

Mezi předdefinované typy dotazů pro prohledávání protokolu patří:

- Agenti poskytující data kabelové sítě
- IP adresa agentů poskytujících data kabelové sítě
- Odchozí komunikace podle IP adres
- Počet odeslaných bajtů podle aplikačních protokolů
- Počet odeslaných bajtů podle aplikační služby
- Přijaté bajty podle různých protokolů
- Celkový počet odeslaných a přijatých bajtů podle verze protokolu IP
- Průměrná latence připojení, která byla spolehlivě změřena
- Procesy počítače, které iniciovaly nebo přijaly síťový provoz
- Objem síťového provozu procesu

Při hledání pomocí dat kabelové sítě můžete filtrováním a seskupením dat zobrazit informace o hlavních agentech a protokolech. Můžete také zjistit, kdy určité počítače (IP adresy / adresy MAC) vzájemně komunikovaly, jak dlouho a kolik dat bylo odesláno – vidíte vlastně metadata o síťovém provozu, která se dají prohledávat.

Protože si ale prohlížíte metadata, nemusí být užitečná při řešení problémů, kdy musíte jít do hloubky. Data kabelové sítě ve službě Log Analytics nepředstavují kompletní záznam síťových dat.  Nejsou určena k hloubkovému řešení problémů na úrovni paketů. V porovnání s jinými metodami shromažďování má použití agentů výhodu v tom, že nemusíte instalovat zařízení, měnit konfiguraci síťových přepínačů nebo provádět komplikované konfigurace. Data kabelové sítě jednoduše vycházejí z agenta, kterého nainstalujete do počítače a který bude monitorovat svůj vlastní síťový provoz. Další výhodu poznáte, když potřebujete monitorovat úlohy běžící u poskytovatele cloudu, poskytovatele hostování služeb nebo v Microsoft Azure, kdy uživatel nevlastní vrstvu prostředků infrastruktury.

## <a name="connected-sources"></a>Připojené zdroje

Řešení Wire Data získává data ze závislého agenta Microsoft. Agent závislostí závisí na agenta Log Analytics pro připojení ke službě Log Analytics. To znamená, že server musí mít nainstalovaný a nakonfigurovaný pomocí agenta závislostí agenta Log Analytics. Následující tabulka popisuje připojené zdroje, které řešení Wire Data podporuje.

| **Připojený zdroj** | **Podporuje se** | **Popis** |
| --- | --- | --- |
| Agenti systému Windows | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Windows. <br><br> Kromě [agenta Log Analytics pro Windows](log-analytics-agent-windows.md), agenti Windows vyžadují Agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../monitoring/monitoring-service-map-configure.md#supported-windows-operating-systems). |
| Agenti systému Linux | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Linuxu.<br><br> Kromě [agenta Log Analytics pro Linux](log-analytics-quick-collect-linux-computer.md), agenty Linux vyžadují Agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../monitoring/monitoring-service-map-configure.md#supported-linux-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Řešení Wire Data analyzuje a shromažďuje data z agentů systému Windows a Linux v připojené [skupině pro správu nástroje System Center Operations Manager](log-analytics-om-agents.md). <br><br> Vyžaduje se přímé připojení z počítače s agentem nástroje System Center Operations Manager ke službě Log Analytics. |
| Účet služby Azure Storage | Ne | Řešení Wire Data shromažďuje data z počítačů s agenty, takže neobsahuje žádná data shromažďovaná z Azure Storage. |

Ve Windows se Microsoft Monitoring Agent (MMA) používá nástrojem System Center Operations Manager i službou Log Analytics ke shromažďování a odesílání dat. V závislosti na kontextu se nazývá agenta System Center Operations Manager Agent, agenta Log Analytics, MMA nebo přímý Agent. Nástroj System Center Operations Manager a služba Log Analytics poskytují mírně odlišné verze agenta MMA. Tyto verze dokážou podávat hlášení nástroji System Center Operations Manager, službě Log Analytics nebo oběma.

V Linuxu agenta Log Analytics pro Linux shromažďuje a odesílá data do Log Analytics. Při přenosu dat můžete použít na servery s agenty, které jsou připojeny přímo k Log Analytics nebo na serverech, které se připojujete ke službě Log Analytics prostřednictvím skupin pro správu System Center Operations Manager.

Závislý agent nepřenáší sám o sobě žádná data a nevyžaduje žádné změny bran firewall nebo portů. Data ve Wire Data se vždy přenášených v rámci agenta Log Analytics ke službě Log Analytics, buď přímo nebo přes bránu Log Analytics.

![Diagram agenta](./media/log-analytics-wire-data/agents.png)

Pokud System Center Operations Manager používáte se skupinou pro správu připojenou ke službě Log Analytics:

- Nevyžaduje se žádná další konfigurace, pokud mají agenti nástroje System Center Operations Manager přístup k internetu a mohou se připojit ke službě Log Analytics.
- Budete muset nakonfigurovat bránu Log Analytics pro práci s nástrojem System Center Operations Manager při agenty System Center Operations Manager nemůže přistupovat k Log Analytics přes Internet.

Pokud počítače Windows nebo Linuxem nemůžete připojit přímo ke službě, budete muset nakonfigurovat agenta Log Analytics pro připojení ke službě Log Analytics pomocí brány Log Analytics. Můžete stáhnout ze brána Log Analytics [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Požadavky

- Vyžaduje se nabídka řešení [Přehledy a analýzy](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Pokud používáte předchozí verzi řešení Wire Data, musíte ho nejprve odebrat. Všechna data zachycená původním řešením Wire Data jsou ale pořád dostupná ve verzi Wire Data 2.0 a při prohledávání protokolu.
- K instalaci a odinstalaci závislého agenta se vyžadují oprávnění správce.
- Závislý agent musí být nainstalovaný na počítači s 64bitovým operačním systémem.

### <a name="operating-systems"></a>Operační systémy

V následujících částech najdete seznam operačních systémů pro závislého agenta. Řešení Wire Data nepodporuje 32bitovou architekturu žádného operačního systému.

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

#### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux a Oracle Linux (s jádrem RHEL)

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s řetězcem verze _2.6.16.21-0.8-xen_ není podporovaný.
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.
- Jádro CentOSPlus není podporované.
- Jádro Oracle Unbreakable Enterprise (UEK) je popsané v další části tohoto článku.

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

#### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux s jádrem Unbreakable Enterprise Kernel

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

#### <a name="dependency-agent-downloads"></a>Soubory závislého agenta ke stažení

| **File** | **OS** | **Verze** | **SHA-256** |
| --- | --- | --- | --- |
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.0.5 | 73B3F6A2A76A08D58F72A550947FF839B588591C48E6EDDD6DDF73AA3FD82B43 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.0.5 | A1BAD0B36EBF79F2B69113A07FCF48C68D90BD169C722689F9C83C69FC032371 |



## <a name="configuration"></a>Konfigurace

Při konfiguraci řešení Wire Data pro vaše pracovní prostory použijte následující postup.

1. Povolte řešení Activity Log Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) nebo pomocí postupu popsaného v článku [Přidání řešení Log Analytics z galerie řešení](../azure-monitor/insights/solutions.md).
2. Nainstalujte závislého agenta na každý počítač, ze kterého chcete získat data. Závislý agent dokáže monitorovat připojení k bezprostředním sousedům, takže nepotřebujete mít agenta na každém počítači.

> [!NOTE]
> Předchozí verze řešení Wire Data se nedá přidat do nových pracovních prostorů. Pokud máte povolené původní řešení Wire Data, můžete ho dál používat. Pokud ale chcete použít Wire Data 2.0, musíte původní verzi nejprve odebrat.
> 
### <a name="install-the-dependency-agent-on-windows"></a>Instalace závislého agenta ve Windows

K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Závislý agent se instaluje do počítačů s Windows prostřednictvím souboru InstallDependencyAgent-Windows.exe. Pokud tento spustitelný soubor spustíte bez jakýchkoli parametrů, spustí se průvodce interaktivní instalací.

Následujícím postupem nainstalujte závislého agenta na jednotlivé počítače s Windows:

1. Instalace agenta Log Analytics, proveďte kroky v [shromažďovat data z počítačů s Windows hostovaných ve vašem prostředí](log-analytics-agent-windows.md).
2. Stáhněte závislého agenta pro Windows pomocí odkazu v předchozí části a pak ho spusťte následujícím příkazem: `InstallDependencyAgent-Windows.exe`
3. Pomocí průvodce agenta nainstalujte.
4. Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. U agentů pro Windows se adresář protokolu nachází zde: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Příkazový řádek Windows

K instalaci z příkazového řádku použijte parametry z následující tabulky. Spuštěním instalačního programu s následujícím parametrem /? zobrazíte seznam parametrů instalace.

InstallDependencyAgent-Windows.exe /?

| **Parametr** | **Popis** |
| --- | --- |
| <code>/?</code> | Získá seznam parametrů příkazového řádku. |
| <code>/S</code> | Provede tichou instalaci bez zobrazení výzev uživateli. |

Soubory závislého agenta pro Windows jsou standardně umístěné zde: C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace závislého agenta v Linuxu

K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.

Závislý agent se do linuxových počítačů instaluje příkazem InstallDependencyAgent-Linux64.bin, což je skript prostředí se samorozbalovacím binárním souborem. Tento soubor můžete spustit pomocí příkazu _sh_ nebo tak, že k samotnému souboru přidáte oprávnění ke spuštění.

Následujícím postupem nainstalujte závislého agenta na jednotlivé počítače s Linuxem:

1. Instalace agenta Log Analytics, proveďte kroky v [shromažďovat data z počítačů s Linuxem hostovaných ve vašem prostředí](log-analytics-quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Stáhněte závislého agenta pro Linux pomocí odkazu v předchozí části a pak ho nainstalujte jako uživatel root následujícím příkazem: sh InstallDependencyAgent-Linux64.bin
3. Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. U agentů pro Linux se adresář protokolu nachází zde: /var/opt/microsoft/dependency-agent/log.

Spuštěním instalačního programu s následujícím parametrem `-help` zobrazíte seznam parametrů instalace.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Parametr** | **Popis** |
| --- | --- |
| <code>-help</code> | Získá seznam parametrů příkazového řádku. |
| <code>-s</code> | Provede tichou instalaci bez zobrazení výzev uživateli. |
| <code>--check</code> | Zkontroluje oprávnění a operační systém bez instalace agenta. |

Soubory závislého agenta se nacházejí v následujících adresářích:

| **Soubory** | **Umístění** |
| --- | --- |
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Příklady instalačního skriptu

Se snadným nasazením závislého agenta na mnoho serverů najednou vám pomůže skript. Následující příklady skriptů můžete použít ke stažení a instalaci závislého agenta do Windows nebo Linuxu.

#### <a name="powershell-script-for-windows"></a>Skript PowerShellu pro Windows

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

K nasazení závislého agenta prostřednictvím konfigurace požadovaného stavu můžete použít modul xPSDesiredStateConfiguration a několik následujících řádků kódu:

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
### <a name="uninstall-the-dependency-agent"></a>Odinstalace závislého agenta

Následující části vám pomůžou s odebráním závislého agenta.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalace závislého agenta ve Windows

Správce může závislého agenta pro Windows odinstalovat přes Ovládací panely.

Správce může závislého agenta odinstalovat také spuštěním souboru %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalace závislého agenta v Linuxu

Pokud chcete závislého agenta úplně odinstalovat z Linuxu, musíte odebrat samotného agenta a konektor, který se instaluje automaticky s tímto agentem. Obojí najednou odinstalujete následujícím jediným příkazem:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Sady Management Pack

Když se řešení Wire Data aktivuje v pracovním prostoru Log Analytics, odešle se do všech serverů Windows v tomto pracovním prostoru sada Management Pack o velikosti 300 kB. Pokud používáte agenty nástroje System Center Operations Manager v [připojené skupině pro správu](log-analytics-om-agents.md), nasadí se sada Management Pack monitoru závislostí z nástroje System Center Operations Manager. Pokud jsou agenti připojení přímo, doručí sadu Management Pack služba Log Analytics.

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Uloží se sem: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Sada Management Pack používá tento zdroj dat: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Použití řešení

**Instalace a konfigurace řešení**

K instalaci a konfiguraci řešení můžete použít následující informace.

- Řešení Wire Data získává data z počítačů s operačními systémy Windows Server 2012 R2, Windows 8.1 a novějšími.
- Na počítačích, ze kterých chcete data kabelové sítě získávat, se vyžaduje rozhraní Microsoft .NET 4.0 nebo novější.
- Řešení Wire Data přidáte do pracovního prostoru Log Analytics pomocí postupu popsaného v článku [Přidání řešení Log Analytics z galerie řešení](../azure-monitor/insights/solutions.md). Není nutná žádná další konfigurace.
- Pokud si chcete prohlédnout data kabelové sítě konkrétního řešení, musí být toto řešení už přidané do vašeho pracovního prostoru.

Po instalaci agentů a tohoto řešení se ve vašem pracovním prostoru objeví dlaždice Wire Data 2.0.

![Dlaždice Wire Data](./media/log-analytics-wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Použití řešení Wire Data 2.0

Na stránce **Přehled** pracovního prostoru Log Analytics na Azure Portalu otevřete kliknutím na **Wire Data 2.0** řídicí panel Wire Data. Tento řídicí panel obsahuje okna popsaná v následující tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Kliknutím na **Zobrazit vše** v dolní části okna nebo na záhlaví okna můžete spustit hledání v protokolu, které vrátí všechny záznamy.

| **Okno** | **Popis** |
| --- | --- |
| Agenti zachytávající síťový přenos | Zobrazuje počet agentů, kteří zachytávají síťový provoz, a seznam 10 hlavních počítačů, které zachytávají provoz. Kliknutím na počet můžete v protokolu vyhledat <code>Type:WireData &#124; measure Sum(TotalBytes) by Computer &#124; top 500000</code>. Kliknutím na počítač v seznamu vyhledáte v protokolu celkový počet zachycených bajtů. |
| Místní podsítě | Zobrazuje počet místních podsítí zjištěných agenty.  Kliknutím na počet vyhledáte v protokolu <code>Type:WireData &#124; Measure Sum(TotalBytes) by LocalSubnet</code> a zobrazíte seznam všech podsítí s počtem bajtů odeslaných přes každou z nich. Kliknutím na podsíť v seznamu vyhledáte v protokolu celkový počet bajtů odeslaných přes tuto podsíť. |
| Protokoly na úrovni aplikace | Zobrazuje počet používaných protokolů na úrovni aplikace zjištěných agenty. Kliknutím na počet můžete v protokolu vyhledat <code>Type:WireData &#124; Measure Sum(TotalBytes) by ApplicationProtocol</code>. Kliknutím na protokol vyhledáte v protokolu celkový počet bajtů odeslaných pomocí tohoto protokolu. |

![Řídicí panel Wire Data](./media/log-analytics-wire-data/wire-data-dash.png)

V okně **Agenti zachytávající síťový přenos** můžete zjistit, kolik šířky pásma sítě spotřebovávají jednotlivé počítače. Toto okno vám pomůže snadno najít počítač s _největší komunikací_ ve vašem prostředí. Takové počítače mohou být přetížené, chovat se abnormálně nebo používat více síťových prostředků než normálně.

![Příklad prohledávání protokolu](./media/log-analytics-wire-data/log-search-example01.png)

V okně **Místní podsítě** můžete podobně zjistit, jak velký síťový provoz probíhá přes vaše podsítě. Uživatelé často definují podsítě pro kritické oblasti svých aplikací. Toto okno vám umožní tyto oblasti zobrazit.

![Příklad prohledávání protokolu](./media/log-analytics-wire-data/log-search-example02.png)

V okně **Protokoly na úrovni aplikace** můžete zjistit, jaké protokoly se používají. Můžete například očekávat, že se ve vašem síťovém prostředí nepoužívá protokol SSH. Prohlédnutím informací dostupných v tomto okně můžete tento předpoklad rychle potvrdit nebo vyvrátit.

![Příklad prohledávání protokolu](./media/log-analytics-wire-data/log-search-example03.png)

V tomto příkladu můžete přechodem na podrobnosti SSH zjistit, které počítače používají SSH, a spoustu dalších detailů o komunikaci.

![Výsledky hledání protokolu SSH](./media/log-analytics-wire-data/ssh-details.png)

Je také užitečné vědět, jestli se provoz přes protokol časem zvyšuje nebo snižuje. Pokud se například množství dat přenášených nějakou aplikací zvyšuje, může se jednat o něco, o čem byste měli vědět nebo co může stát za povšimnutí.

## <a name="input-data"></a>Vstupní data

Řešení Wire Data shromažďuje metadata o síťovém provozu pomocí agentů, které jste povolili. Každý agent odesílá data zhruba po 15 sekundách.

## <a name="output-data"></a>Výstupní data

Pro každý typ vstupních dat se vytvoří záznam typu _WireData_. Záznamy WireData mají vlastnosti uvedené v následující tabulce:

| Vlastnost | Popis |
|---|---|
| Počítač | Název počítače, na kterém byla data shromážděna |
| TimeGenerated | Čas záznamu |
| LocalIP | IP adresa místního počítače |
| SessionState | Připojeno nebo Odpojeno |
| ReceivedBytes | Množství přijatých bajtů |
| ProtocolName | Název použitého síťového protokolu |
| IPVersion | Verze protokolu IP |
| Směr | Příchozí nebo odchozí |
| MaliciousIP | IP adresa známého škodlivého zdroje |
| Severity | Závažnost podezřelého malwaru |
| RemoteIPCountry | Země vzdálené IP adresy |
| ManagementGroupName | Název skupiny pro správu nástroje Operations Manager |
| SourceSystem | Zdroj, kde byla data shromážděna |
| SessionStartTime | Čas zahájení relace |
| SessionEndTime | Čas ukončení relace |
| LocalSubnet | Podsíť, kde byla data shromážděna |
| LocalPortNumber | Číslo místního portu |
| RemoteIP | Vzdálená IP adresa použitá vzdáleným počítačem |
| RemotePortNumber | Číslo portu použité vzdálenou IP adresou |
| SessionID | Jedinečná hodnota, která identifikuje komunikační relaci mezi dvěma IP adresami |
| SentBytes | Počet odeslaných bajtů |
| TotalBytes | Celkový počet bajtů odeslaných během relace |
| ApplicationProtocol | Typ použitého síťového protokolu   |
| ProcessID | ID procesu Windows |
| ProcessName | Cesta a název souboru procesu |
| RemoteIPLongitude | Zeměpisná délka IP adresy |
| RemoteIPLatitude | Zeměpisná šířka IP adresy |


## <a name="next-steps"></a>Další postup

- Podrobné záznamy hledání dat kabelové sítě zobrazíte [prohledáním protokolů](log-analytics-queries.md).
