---
title: Spojte Data řešení ve službě Azure Monitor | Dokumentace Microsoftu
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
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: d295a5a7eae2bdc7983e7271aa11bce1840b92dd
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882068"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Řešení Wire Data 2.0 (Preview) ve službě Azure Monitor

![Symbol Wire Data](media/wire-data/wire-data2-symbol.png)

Při přenosu dat se konsolidované síti a výkonu data shromážděná z počítače připojeného k Windows a Linux připojené pomocí agenta Log Analytics, včetně těch, které monitorovány nástrojem Operations Manager ve vašem prostředí. Kvůli pomoci při korelaci dat se síťová data kombinují s jinými daty protokolu.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Kromě agenta Log Analytics řešení Wire Data používá Microsoft Agents závislostí, který nainstalujete na počítačích ve vaší infrastruktuře IT. Závislí agenti monitorují síťová data odesílaná do a z počítačů na úrovních sítě 2–3 v [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), a to včetně různých použitých protokolů a portů. Data se pak posílají do Azure monitorování pomocí agentů.  

>[!NOTE]
>Pokud jste už nasadili řešení Service Map, nebo zvažuje Service Map nebo [monitorování Azure pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md), je nové připojení metriky sady dat shromažďovat a ukládat ve službě Azure Monitor, která poskytuje stejné informace s Wire Data.

Ve výchozím nastavení Azure Monitor protokoly dat týkající se využití procesoru, paměti, disku a sítě výkonnostní data z čítače integrovaný do Windows a Linuxem, jakož i další čítače, které můžete zadat. Shromažďování síťových a jiných dat probíhá u každého agenta v reálném čase včetně podsítí a protokolů na úrovni aplikace, které počítač používá.  Řešení Wire Data sleduje síťová data na úrovni aplikace, nikoli v přenosové vrstvě TCP.  Toto řešení nesleduje individuální signály ACK a SYN.  Po dokončení metody handshake se připojení považuje za aktivní a označí se jako Připojeno. Toto připojení zůstává aktivní, dokud obě strany souhlasí, že je soket otevřený, a data mohou proudit tam a zpět.  Po obou stranách zavře připojení, je označena jako odpojeno.  Proto se počítá jen šířka pásma úspěšně dokončených paketů a neoznamují se opětovně odeslané nebo neúspěšné pakety.

Pokud používáte [sFlow](http://www.sflow.org/) nebo jiný software s [protokolem NetFlow společnosti Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), budou vám statistiky a údaje dat kabelové sítě povědomé.

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

Protože si ale prohlížíte metadata, nemusí být užitečná při řešení problémů, kdy musíte jít do hloubky. Při přenosu dat ve službě Azure Monitor není úplné sběru dat v síti.  Nejsou určena k hloubkovému řešení problémů na úrovni paketů. Výhodou používání agenta ve srovnání s jinými metodami kolekce, je, že není nutné instalovat zařízení, opětovně konfigurovat síťové přepínače nebo proveďte komplikované konfigurace. Data kabelové sítě jednoduše vycházejí z agenta, kterého nainstalujete do počítače a který bude monitorovat svůj vlastní síťový provoz. Další výhodu poznáte, když potřebujete monitorovat úlohy běžící u poskytovatele cloudu, poskytovatele hostování služeb nebo v Microsoft Azure, kdy uživatel nevlastní vrstvu prostředků infrastruktury.

## <a name="connected-sources"></a>Připojené zdroje

Řešení Wire Data získává data ze závislého agenta Microsoft. Agent závislostí závisí na agenta Log Analytics pro jeho připojení k Azure Monitor. To znamená, že server musí mít nainstalovaný a nakonfigurovaný pomocí agenta závislostí agenta Log Analytics. Následující tabulka popisuje připojené zdroje, které řešení Wire Data podporuje.

| **Připojený zdroj** | **Podporováno** | **Popis** |
| --- | --- | --- |
| Agenti systému Windows | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Windows. <br><br> Kromě [agenta Log Analytics pro Windows](../../azure-monitor/platform/agent-windows.md), agenti Windows vyžadují Agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems). |
| Agenti systému Linux | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Linuxu.<br><br> Kromě [agenta Log Analytics pro Linux](../../azure-monitor/learn/quick-collect-linux-computer.md), agenty Linux vyžadují Agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Řešení Wire Data analyzuje a shromažďuje data z agentů systému Windows a Linux v připojené [skupině pro správu nástroje System Center Operations Manager](../../azure-monitor/platform/om-agents.md). <br><br> Přímé připojení z počítače agenta System Center Operations Manageru do Azure monitoru je povinný. |
| Účet služby Azure Storage | Ne | Řešení Wire Data shromažďuje data z počítačů s agenty, takže neobsahuje žádná data shromažďovaná z Azure Storage. |

Na Windows Microsoft Monitoring Agent (MMA) používá System Center Operations Manageru a Azure Monitor k shromažďuje a odesílá data. V závislosti na kontextu se nazývá agenta System Center Operations Manager Agent, agenta Log Analytics, MMA nebo přímý Agent. System Center Operations Manageru a Azure Monitor poskytuje trošku odlišné verze agenta MMA. Tyto verze můžete každou sestavu na System Center Operations Manager, Azure Monitor nebo obojí.

V Linuxu agenta Log Analytics pro Linux shromažďuje a odesílá data do Azure monitoru. Při přenosu dat můžete použít na servery s agenty, které jsou přímo připojené k Azure Monitor nebo na serverech, které se připojují k monitorování Azure prostřednictvím skupin pro správu System Center Operations Manager.

Závislý agent nepřenáší sám o sobě žádná data a nevyžaduje žádné změny bran firewall nebo portů. Data ve Wire Data přenášena agenta Log Analytics do Azure monitoru, vždy, buď přímo nebo přes bránu Log Analytics.

![Diagram agenta](./media/wire-data/agents.png)

Pokud jste uživatel System Center Operations Manager s skupinu pro správu připojené k Azure Monitor:

- Není nutná žádná další konfigurace, kdy agentů System Center Operations Manager můžete přístup k Internetu pro připojení k Azure Monitor.
- Budete muset nakonfigurovat bránu Log Analytics pro práci s nástrojem System Center Operations Manager při agenty System Center Operations Manager nemá přístup k Azure Monitor přes internet.

Pokud počítače Windows nebo Linuxem nemůžete připojit přímo ke službě, budete muset nakonfigurovat agenta Log Analytics pro připojení k Azure monitorování pomocí Log Analytics brány. Můžete stáhnout ze brána Log Analytics [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Požadavky

- Vyžaduje se nabídka řešení [Přehledy a analýzy](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Pokud používáte předchozí verzi řešení Wire Data, musíte ho nejprve odebrat. Všechna data zachycená původním řešením Wire Data jsou ale pořád dostupná ve verzi Wire Data 2.0 a při prohledávání protokolu.
- K instalaci a odinstalaci závislého agenta se vyžadují oprávnění správce.
- Závislý agent musí být nainstalovaný na počítači s 64bitovým operačním systémem.

### <a name="operating-systems"></a>Operační systémy

V následujících částech najdete seznam operačních systémů pro závislého agenta. Řešení Wire Data nepodporuje 32bitovou architekturu žádného operačního systému.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Plocha Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux
Podporované operační systémy pro agenta závislostí v Linuxu v následujících částech.  

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s vydání řetězec "2.6.16.21-0.8-xen" není podporován.
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádra 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
| 12 SP2 | 4.4. * |
| 12 SP3 | 4.4. * |

### <a name="dependency-agent-downloads"></a>Soubory ke stažení agenta závislostí

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfigurace

Při konfiguraci řešení Wire Data pro vaše pracovní prostory použijte následující postup.

1. Povolení řešení Activity Log Analytics z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) nebo pomocí procesu popsaného v [přidat řešení z Galerie řešení monitorování](../../azure-monitor/insights/solutions.md).
2. Nainstalujte závislého agenta na každý počítač, ze kterého chcete získat data. Závislý agent dokáže monitorovat připojení k bezprostředním sousedům, takže nepotřebujete mít agenta na každém počítači.

> [!NOTE]
> Předchozí verze řešení Wire Data se nedá přidat do nových pracovních prostorů. Pokud máte povolené původní řešení Wire Data, můžete ho dál používat. Pokud ale chcete použít Wire Data 2.0, musíte původní verzi nejprve odebrat.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instalace závislého agenta ve Windows

K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Závislý agent se instaluje do počítačů s Windows prostřednictvím souboru InstallDependencyAgent-Windows.exe. Pokud tento spustitelný soubor spustíte bez jakýchkoli parametrů, spustí se průvodce interaktivní instalací.

Následujícím postupem nainstalujte závislého agenta na jednotlivé počítače s Windows:

1. Instalace agenta Log Analytics, proveďte kroky v [shromažďovat data z počítačů s Windows hostovaných ve vašem prostředí](../../azure-monitor/platform/agent-windows.md).
2. Stáhnout agenta závislosti Windows pomocí odkazu v předchozí části a potom ji spustit pomocí následujícího příkazu: `InstallDependencyAgent-Windows.exe`
3. Pomocí průvodce agenta nainstalujte.
4. Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. U agentů pro Windows se adresář protokolu nachází zde: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Příkazový řádek Windows

K instalaci z příkazového řádku použijte parametry z následující tabulky. Spuštěním instalačního programu s následujícím parametrem /? zobrazíte seznam parametrů instalace.

InstallDependencyAgent-Windows.exe /?

| **Příznak** | **Popis** |
| --- | --- |
| <code>/?</code> | Získá seznam parametrů příkazového řádku. |
| <code>/S</code> | Provede tichou instalaci bez zobrazení výzev uživateli. |

Soubory závislého agenta pro Windows jsou standardně umístěné zde: C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace závislého agenta v Linuxu

K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.

Závislý agent se do linuxových počítačů instaluje příkazem InstallDependencyAgent-Linux64.bin, což je skript prostředí se samorozbalovacím binárním souborem. Tento soubor můžete spustit pomocí příkazu _sh_ nebo tak, že k samotnému souboru přidáte oprávnění ke spuštění.

Následujícím postupem nainstalujte závislého agenta na jednotlivé počítače s Linuxem:

1. Instalace agenta Log Analytics, proveďte kroky v [shromažďovat data z počítačů s Linuxem hostovaných ve vašem prostředí](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Stáhněte závislého agenta pro Linux pomocí odkazu v předchozí části a pak ho nainstalujte jako uživatel root následujícím příkazem: sh InstallDependencyAgent-Linux64.bin
3. Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. U agentů pro Linux se adresář protokolu nachází zde: /var/opt/microsoft/dependency-agent/log.

Spuštěním instalačního programu s následujícím parametrem `-help` zobrazíte seznam parametrů instalace.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Příznak** | **Popis** |
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

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

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

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

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

Když se řešení Wire Data aktivuje v pracovním prostoru služby Log Analytics, odešle se do všech serverů Windows v tomto pracovním prostoru sada Management Pack o velikosti 300 kB. Pokud používáte agenty nástroje System Center Operations Manager v [připojené skupině pro správu](../platform/om-agents.md), nasadí se sada Management Pack monitoru závislostí z nástroje System Center Operations Manager. Pokud byli přímo připojení agenti, Azure Monitor nabízí sadu management pack.

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Uloží se sem: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Sada Management Pack používá tento zdroj dat: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Použití řešení

K instalaci a konfiguraci řešení můžete použít následující informace.

- Řešení Wire Data získává data z počítačů s operačními systémy Windows Server 2012 R2, Windows 8.1 a novějšími.
- Na počítačích, ze kterých chcete data kabelové sítě získávat, se vyžaduje rozhraní Microsoft .NET 4.0 nebo novější.
- Přidat řešení Wire Data do pracovního prostoru Log Analytics pomocí postupu popsaného v [přidat řešení z Galerie řešení monitorování](solutions.md). Není nutná žádná další konfigurace.
- Pokud si chcete prohlédnout data kabelové sítě konkrétního řešení, musí být toto řešení už přidané do vašeho pracovního prostoru.

Po instalaci agentů a tohoto řešení se ve vašem pracovním prostoru objeví dlaždice Wire Data 2.0.

![Dlaždice Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Použití řešení Wire Data 2.0

Na stránce **Přehled** pracovního prostoru služby Log Analytics na Azure Portalu otevřete kliknutím na **Wire Data 2.0** řídicí panel Wire Data. Tento řídicí panel obsahuje okna popsaná v následující tabulce. V každém okně je seznam až 10 položek, které vyhovují kritériím oboru a časového rozsahu daného okna. Kliknutím na **Zobrazit vše** v dolní části okna nebo na záhlaví okna můžete spustit hledání v protokolu, které vrátí všechny záznamy.

| **Okno** | **Popis** |
| --- | --- |
| Agenti zachytávající síťový přenos | Zobrazuje počet agentů, kteří zachytávají síťový provoz, a seznam 10 hlavních počítačů, které zachytávají provoz. Kliknutím na počet můžete v protokolu vyhledat <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kliknutím na počítač v seznamu vyhledáte v protokolu celkový počet zachycených bajtů. |
| Místní podsítě | Zobrazuje počet místních podsítí zjištěných agenty.  Kliknutím na počet vyhledáte v protokolu <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> a zobrazíte seznam všech podsítí s počtem bajtů odeslaných přes každou z nich. Kliknutím na podsíť v seznamu vyhledáte v protokolu celkový počet bajtů odeslaných přes tuto podsíť. |
| Protokoly na úrovni aplikace | Zobrazuje počet používaných protokolů na úrovni aplikace zjištěných agenty. Kliknutím na počet můžete v protokolu vyhledat <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kliknutím na protokol vyhledáte v protokolu celkový počet bajtů odeslaných pomocí tohoto protokolu. |

![Řídicí panel Wire Data](./media/wire-data/wire-data-dash.png)

V okně **Agenti zachytávající síťový přenos** můžete zjistit, kolik šířky pásma sítě spotřebovávají jednotlivé počítače. Toto okno vám pomůže snadno najít počítač s _největší komunikací_ ve vašem prostředí. Takové počítače mohou být přetížené, chovat se abnormálně nebo používat více síťových prostředků než normálně.

![Příklad prohledávání protokolu](./media/wire-data/log-search-example01.png)

V okně **Místní podsítě** můžete podobně zjistit, jak velký síťový provoz probíhá přes vaše podsítě. Uživatelé často definují podsítě pro kritické oblasti svých aplikací. Toto okno vám umožní tyto oblasti zobrazit.

![Příklad prohledávání protokolu](./media/wire-data/log-search-example02.png)

V okně **Protokoly na úrovni aplikace** můžete zjistit, jaké protokoly se používají. Můžete například očekávat, že se ve vašem síťovém prostředí nepoužívá protokol SSH. Prohlédnutím informací dostupných v tomto okně můžete tento předpoklad rychle potvrdit nebo vyvrátit.

![Příklad prohledávání protokolu](./media/wire-data/log-search-example03.png)

Je také užitečné vědět, jestli se provoz přes protokol časem zvyšuje nebo snižuje. Pokud se například množství dat přenášených nějakou aplikací zvyšuje, může se jednat o něco, o čem byste měli vědět nebo co může stát za povšimnutí.

## <a name="input-data"></a>Vstupní data

Řešení Wire Data shromažďuje metadata o síťovém provozu pomocí agentů, které jste povolili. Každý agent odesílá data zhruba po 15 sekundách.

## <a name="output-data"></a>Výstupní data

Pro každý typ vstupních dat se vytvoří záznam typu _WireData_. Záznamy WireData mají vlastnosti uvedené v následující tabulce:

| Vlastnost | Popis |
|---|---|
| Computer | Název počítače, na kterém byla data shromážděna |
| TimeGenerated | Čas záznamu |
| LocalIP | IP adresa místního počítače |
| SessionState | Připojeno nebo Odpojeno |
| ReceivedBytes | Množství přijatých bajtů |
| ProtocolName | Název použitého síťového protokolu |
| IPVersion | Verze protokolu IP |
| Direction | Příchozí nebo odchozí |
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

- Podrobné záznamy hledání dat kabelové sítě zobrazíte [prohledáním protokolů](../../azure-monitor/log-query/log-query-overview.md).
