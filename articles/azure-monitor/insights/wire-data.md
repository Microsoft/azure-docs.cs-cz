---
title: Řešení pro přenos dat v Azure Monitor | Microsoft Docs
description: Data o kabelech jsou konsolidovaná síťová a výkonná data z počítačů s agenty Log Analytics. Kvůli pomoci při korelaci dat se síťová data kombinují s daty protokolu.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/29/2020
ms.openlocfilehash: 5981a5f136d613ffcedda86797d807d2eecfab0d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713622"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Řešení Wire Data 2.0 (Preview) v Azure Monitor

![Symbol Wire Data](media/wire-data/wire-data2-symbol.png)

Data o kabelech jsou konsolidovaná data o síti a výkonu shromážděná z počítačů připojených k Windows a Linuxem připojeným pomocí agenta Log Analytics, včetně těch, které monitoruje Operations Manager ve vašem prostředí. Kvůli pomoci při korelaci dat se síťová data kombinují s jinými daty protokolu.

Kromě agenta Log Analytics používá řešení pro přenos dat také agenty závislostí od Microsoftu, které nainstalujete do počítačů v infrastruktuře IT. Závislí agenti monitorují síťová data odesílaná do a z počítačů na úrovních sítě 2–3 v [modelu OSI](https://en.wikipedia.org/wiki/OSI_model), a to včetně různých použitých protokolů a portů. Data se pak odesílají Azure Monitor pomocí agentů.  

>[!NOTE]
>Řešení pro přenos dat bylo nahrazeno [Service MAPM řešením](../vm/service-map.md).  Pomocí agenta Log Analytics a agenta závislostí se shromažďují data síťového připojení do Azure Monitor. 
> 
>Stávající zákazníci, kteří používají řešení pro přenos dat, ho můžou dál používat. Pokyny pro časovou osu migrace pro přesun na Service Map budeme publikovat.
>
>Noví zákazníci by měli nainstalovat [Service map řešení](../vm/service-map.md) nebo [přehledy virtuálních počítačů](../vm/vminsights-overview.md).  Sada dat Service Map je srovnatelná s daty o kabelech.  Funkce VM Insights zahrnuje sadu dat Service Map s dalšími údaji o výkonu a funkcemi pro analýzu. 


Ve výchozím nastavení Azure Monitor protokoluje data o výkonu procesoru, paměti, disku a sítě z čítačů integrovaných do systémů Windows a Linux a také dalších čítačů výkonu, které lze zadat. Shromažďování síťových a jiných dat probíhá u každého agenta v reálném čase včetně podsítí a protokolů na úrovni aplikace, které počítač používá.  Řešení Wire Data sleduje síťová data na úrovni aplikace, nikoli v přenosové vrstvě TCP.  Toto řešení nesleduje individuální signály ACK a SYN.  Po dokončení metody handshake se připojení považuje za aktivní a označí se jako Připojeno. Toto připojení zůstává aktivní, dokud obě strany souhlasí, že je soket otevřený, a data mohou proudit tam a zpět.  Jakmile se připojení ukončí, bude označeno jako odpojeno.  Proto se počítá jen šířka pásma úspěšně dokončených paketů a neoznamují se opětovně odeslané nebo neúspěšné pakety.

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

Protože si ale prohlížíte metadata, nemusí být užitečná při řešení problémů, kdy musíte jít do hloubky. Data o přenosech v Azure Monitor nejsou úplným zachytáváním síťových dat.  Nejsou určena k hloubkovému řešení problémů na úrovni paketů. Výhodou použití agenta ve srovnání s jinými metodami shromažďování je, že nemusíte instalovat zařízení, překonfigurovat síťové přepínače nebo provádět složité konfigurace. Data kabelové sítě jednoduše vycházejí z agenta, kterého nainstalujete do počítače a který bude monitorovat svůj vlastní síťový provoz. Další výhodu poznáte, když potřebujete monitorovat úlohy běžící u poskytovatele cloudu, poskytovatele hostování služeb nebo v Microsoft Azure, kdy uživatel nevlastní vrstvu prostředků infrastruktury.

## <a name="connected-sources"></a>Připojené zdroje

Řešení Wire Data získává data ze závislého agenta Microsoft. Dependency Agent závisí na agentu Log Analytics pro připojení k Azure Monitor. To znamená, že server musí mít nainstalovaného a nakonfigurovaného agenta závislostí agenta Log Analytics. Následující tabulka popisuje připojené zdroje, které řešení Wire Data podporuje.

| **Připojený zdroj** | **Podporováno** | **Popis** |
| --- | --- | --- |
| Agenti systému Windows | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Windows. <br><br> Kromě [agenta Log Analytics pro systém Windows](../agents/agent-windows.md)vyžadují agenti systému Windows agenta závislostí od Microsoftu. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../vm/vminsights-enable-overview.md#supported-operating-systems). |
| Agenti systému Linux | Ano | Řešení Wire Data analyzuje a shromažďuje data z počítačů s agenty Linuxu.<br><br> Kromě agentů [Log Analytics pro Linux](../vm/quick-collect-linux-computer.md)musí mít agenti pro Linux od společnosti Microsoft agenta závislostí od Microsoftu. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](../vm/vminsights-enable-overview.md#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Řešení Wire Data analyzuje a shromažďuje data z agentů systému Windows a Linux v připojené [skupině pro správu nástroje System Center Operations Manager](../agents/om-agents.md). <br><br> Je požadováno přímé připojení z System Center Operations Manager počítače agenta k Azure Monitor. |
| Účet služby Azure Storage | Ne | Řešení Wire Data shromažďuje data z počítačů s agenty, takže neobsahuje žádná data shromažďovaná z Azure Storage. |

V systému Windows se Microsoft Monitoring Agent (MMA) používá v System Center Operations Manager a Azure Monitor ke shromažďování a posílání dat. V závislosti na kontextu se agent nazývá Agent System Center Operations Manager, Log Analytics agent, MMA nebo Direct agent. System Center Operations Manager a Azure Monitor poskytují mírně odlišnou verzi MMA. Tyto verze mohou jednotlivé sestavy System Center Operations Manager, Azure Monitor nebo obojí.

V systému Linux agent Log Analytics pro Linux shromažďuje a odesílá data do Azure Monitor. Můžete použít data na serverech s agenty přímo připojenými k Azure Monitor nebo na serverech, které se připojují k Azure Monitor prostřednictvím System Center Operations Manager skupin pro správu.

Agent závislostí neodesílá žádná data a nevyžaduje žádné změny bran firewall nebo portů. Data v datových přenosech jsou vždy přenášena agentem Log Analytics do Azure Monitor, a to buď přímo, nebo prostřednictvím brány Log Analytics.

![Diagram agenta](./media/wire-data/agents.png)

Pokud jste System Center Operations Manager uživatel se skupinou pro správu připojenou k Azure Monitor:

- Pokud agenti System Center Operations Manager mají přístup k Internetu, aby se mohli připojit k Azure Monitor, nevyžaduje se žádná další konfigurace.
- Pokud System Center Operations Manager agenti získat přístup k Azure Monitor přes Internet, musíte nakonfigurovat bránu Log Analytics pro práci s System Center Operations Manager.

Pokud se počítače se systémem Windows nebo Linux nemohou přímo připojit ke službě, je nutné nakonfigurovat agenta Log Analytics pro připojení k Azure Monitor pomocí brány Log Analytics. Bránu Log Analytics můžete stáhnout z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Požadavky

- Vyžaduje se nabídka řešení [Přehledy a analýzy](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing).
- Pokud používáte předchozí verzi řešení Wire Data, musíte ho nejprve odebrat. Všechna data zachycená původním řešením Wire Data jsou ale pořád dostupná ve verzi Wire Data 2.0 a při prohledávání protokolu.
- K instalaci nebo odinstalaci agenta závislostí jsou nutná oprávnění správce.
- Agent závislostí musí být nainstalován v počítači s 64 operačním systémem.

### <a name="operating-systems"></a>Operační systémy

V následujících částech najdete seznam podporovaných operačních systémů pro agenta závislostí. Řešení Wire Data nepodporuje 32bitovou architekturu žádného operačního systému.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Plocha Windows

- Systém Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Podporované operační systémy Linux
V následujících částech najdete seznam podporovaných operačních systémů pro agenta závislostí v systému Linux.  

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s řetězcem vydání "2.6.16.21-0,8-Xen" není podporován.
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7,4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32 – 696 |
| 6,10 | 2.6.32 – 754 |

##### <a name="centosplus"></a>CentOSPlus
| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6,10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádro 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | jádro 4,15. * |
| 16,04 | 4,4.\*<br>4,8.\*<br>4,10.\*<br>4,11.\*<br>4,13.\* |
| 14,04 | 3,13.\*<br>4,4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
| 12 SP2 | 4,4. * |
| 12 SP3 | 4,4. * |

### <a name="dependency-agent-downloads"></a>Soubory ke stažení agenta závislostí

| Soubor | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Konfigurace

Při konfiguraci řešení Wire Data pro vaše pracovní prostory použijte následující postup.

1. Povolte řešení Activity Log Analytics z [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) nebo pomocí postupu popsaného v tématu [Přidání řešení monitorování z galerie řešení](../insights/solutions.md).
2. Nainstalujte agenta závislostí do každého počítače, kde chcete získat data. Agent závislostí může monitorovat připojení k přímým sousedům, takže nebudete muset mít v každém počítači agenta.

> [!NOTE]
> Předchozí verze řešení Wire Data se nedá přidat do nových pracovních prostorů. Pokud máte povolené původní řešení Wire Data, můžete ho dál používat. Pokud ale chcete použít Wire Data 2.0, musíte původní verzi nejprve odebrat.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Instalace agenta závislostí ve Windows

K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Agent závislostí je nainstalován na počítačích se systémem Windows prostřednictvím InstallDependencyAgent-Windows.exe. Pokud tento spustitelný soubor spustíte bez jakýchkoli parametrů, spustí se průvodce interaktivní instalací.

K instalaci agenta závislostí do každého počítače se systémem Windows použijte následující postup:

1. Nainstalujte agenta Log Analytics podle kroků v části [shromáždění dat z počítačů s Windows hostovaných ve vašem prostředí](../agents/agent-windows.md).
2. Pomocí odkazu v předchozí části Stáhněte agenta závislostí systému Windows a spusťte jej pomocí následujícího příkazu: `InstallDependencyAgent-Windows.exe`
3. Pomocí průvodce agenta nainstalujte.
4. Pokud se nepovede spustit agenta závislostí, podrobnější informace o chybě najdete v protokolech. U agentů pro Windows se adresář protokolu nachází zde: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Příkazový řádek Windows

K instalaci z příkazového řádku použijte parametry z následující tabulky. Spuštěním instalačního programu s následujícím parametrem /? zobrazíte seznam parametrů instalace.

InstallDependencyAgent-Windows.exe /?

| **Příznak** | **Popis** |
| --- | --- |
| <code>/?</code> | Získá seznam parametrů příkazového řádku. |
| <code>/S</code> | Provede tichou instalaci bez zobrazení výzev uživateli. |

Ve výchozím nastavení jsou soubory pro agenta závislostí systému Windows umístěny ve složce C:\Program Files\Microsoft Dependency agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí na Linux

K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.

Agent závislostí se instaluje do počítačů se systémem Linux prostřednictvím InstallDependencyAgent-Linux64. bin, skriptu prostředí s samorozbalovacím binárním souborem. Tento soubor můžete spustit pomocí příkazu _sh_ nebo tak, že k samotnému souboru přidáte oprávnění ke spuštění.

K instalaci agenta závislostí na každý počítač se systémem Linux použijte následující postup:

1. Nainstalujte agenta Log Analytics podle kroků v části [shromáždění dat z počítačů se systémem Linux hostovaných ve vašem prostředí](../vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Pomocí odkazu v předchozí části Stáhněte agenta závislostí pro Linux a pak ho nainstalujte jako kořenový adresář pomocí následujícího příkazu: SH InstallDependencyAgent-Linux64. bin
3. Pokud se nepovede spustit agenta závislostí, podrobnější informace o chybě najdete v protokolech. U agentů pro Linux se adresář protokolu nachází zde: /var/opt/microsoft/dependency-agent/log.

Spuštěním instalačního programu s následujícím parametrem `-help` zobrazíte seznam parametrů instalace.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Příznak** | **Popis** |
| --- | --- |
| <code>-help</code> | Získá seznam parametrů příkazového řádku. |
| <code>-s</code> | Provede tichou instalaci bez zobrazení výzev uživateli. |
| <code>--check</code> | Zkontroluje oprávnění a operační systém bez instalace agenta. |

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| **Soubory** | **Umístění** |
| --- | --- |
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Příklady instalačního skriptu

Chcete-li snadno nasadit agenta závislostí na mnoho serverů najednou, pomůže vám použít skript. Pomocí následujících ukázkových skriptů můžete stáhnout a nainstalovat agenta závislostí v systému Windows nebo Linux.

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

### <a name="desired-state-configuration"></a>Desired State Configuration

Chcete-li nasadit agenta závislostí přes konfiguraci požadovaného stavu, můžete použít modul xPSDesiredStateConfiguration a bitovou část kódu, například následující:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

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

### <a name="uninstall-the-dependency-agent"></a>Odinstalace agenta závislostí

Následující části vám pomůžou odebrat agenta závislostí.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalace agenta závislostí v systému Windows

Správce může odinstalovat agenta závislostí pro systém Windows prostřednictvím ovládacích panelů.

Správce může také spustit závislost%Programfiles%\Microsoft Agent\Uninstall.exe pro odinstalaci agenta závislostí.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalace agenta závislostí v systému Linux

Chcete-li úplně odinstalovat agenta závislostí ze systému Linux, je nutné odebrat samotného agenta a konektor, který je nainstalován automaticky s agentem. Obojí najednou odinstalujete následujícím jediným příkazem:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Sady Management Pack

Když se řešení Wire Data aktivuje v pracovním prostoru služby Log Analytics, odešle se do všech serverů Windows v tomto pracovním prostoru sada Management Pack o velikosti 300 kB. Pokud používáte agenty nástroje System Center Operations Manager v [připojené skupině pro správu](../agents/om-agents.md), nasadí se sada Management Pack monitoru závislostí z nástroje System Center Operations Manager. Pokud jsou agenti přímo připojeni, Azure Monitor doručí Management Pack.

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Uloží se sem: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Sada Management Pack používá tento zdroj dat: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Použití řešení

K instalaci a konfiguraci řešení můžete použít následující informace.

- Řešení Wire Data získává data z počítačů s operačními systémy Windows Server 2012 R2, Windows 8.1 a novějšími.
- Na počítačích, ze kterých chcete data kabelové sítě získávat, se vyžaduje rozhraní Microsoft .NET 4.0 nebo novější.
- Přidejte řešení přenos dat do svého pracovního prostoru Log Analytics pomocí procesu popsaného v tématu [Přidání řešení monitorování z galerie řešení](../insights/solutions.md). Není nutná žádná další konfigurace.
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

![Snímek obrazovky okna agenti zachytávající síťový provoz na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná jednotlivými počítači.](./media/wire-data/log-search-example01.png)

V okně **Místní podsítě** můžete podobně zjistit, jak velký síťový provoz probíhá přes vaše podsítě. Uživatelé často definují podsítě pro kritické oblasti svých aplikací. Toto okno vám umožní tyto oblasti zobrazit.

![Snímek obrazovky okna místní podsítě na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná jednotlivými LocalSubnet.](./media/wire-data/log-search-example02.png)

V okně **Protokoly na úrovni aplikace** můžete zjistit, jaké protokoly se používají. Můžete například očekávat, že se ve vašem síťovém prostředí nepoužívá protokol SSH. Prohlédnutím informací dostupných v tomto okně můžete tento předpoklad rychle potvrdit nebo vyvrátit.

![Snímek obrazovky okna protokoly na úrovni aplikace na řídicím panelu Wire Data 2.0 ukazující šířku pásma sítě spotřebovaná každým protokolem.](./media/wire-data/log-search-example03.png)

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
| Závažnost | Závažnost podezřelého malwaru |
| RemoteIPCountry | Země nebo oblast vzdálené IP adresy |
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

## <a name="next-steps"></a>Další kroky

- Podrobné záznamy hledání dat kabelové sítě zobrazíte [prohledáním protokolů](../logs/log-query-overview.md).

