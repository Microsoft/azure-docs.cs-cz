---
title: Konfigurace řešení Service Map v Azure | Dokumentace Microsoftu
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobnosti o nasazení řešení Service Map ve vašem prostředí a jejich použití v různých scénářích.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2019
ms.author: bwren
ms.openlocfilehash: 8457334e00f7c8dfbaee75a12d6bb71b760e60d9
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696989"
---
# <a name="configure-service-map-in-azure"></a>Konfigurace řešení Service Map v Azure
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Můžete ho zobrazit servery přirozeným způsobem – propojené systémy, které doručují důležité služby. Service Map ukazuje propojení mezi servery, procesy a porty v jakékoli architektuře propojené TCP žádnou konfiguraci kromě instalace agenta vyžaduje.

Tento článek popisuje podrobnosti o konfiguraci řešení Service Map a připojování agentů. Informace o používání mapy služeb najdete v tématu [v Azure pomocí řešení Service Map]( service-map.md).

## <a name="supported-azure-regions"></a>Podporované oblasti Azure
Řešení Service Map je teď dostupná v těchto oblastech Azure:
- USA – východ
- Západní Evropa
- Západní střed USA
- Jihovýchodní Asie

## <a name="supported-windows-operating-systems"></a>Podporované operační systémy Windows
Následující část obsahuje seznam podporovaných operačních systémech pro agenta závislostí na Windows. 

>[!NOTE]
>Řešení Service Map podporuje pouze 64bitové platformy.
>

### <a name="windows-server"></a>Windows Server
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Plocha Windows
- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

## <a name="supported-linux-operating-systems"></a>Podporované operační systémy a Linux
Následující část obsahuje seznam podporovaných operačních systémech pro agenta závislostí na Red Hat Enterprise Linux, CentOS Linux a Oracle Linux (pomocí jádra RHEL).  

- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s vydání řetězec "2.6.16.21-0.8-xen" není podporován.
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.
- Jádro CentOSPlus není podporované.
- Jádro Oracle Unbreakable Enterprise (UEK) je popsané v další části tohoto článku.

### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Verze operačního systému | Verze jádra |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

### <a name="ubuntu-server"></a>Ubuntu Server

| Verze operačního systému | Verze jádra |
|:--|:--|
| Ubuntu 18.04 | jádra 4.15. * |
| Ubuntu 16.04.3 | jádra 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 s nedělitelné Enterprise jádra
| Verze operačního systému | Verze jádra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 s nedělitelné Enterprise jádra

| Verze operačního systému | Verze jádra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

## <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Verze operačního systému | Verze jádra
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

## <a name="dependency-agent-downloads"></a>Soubory ke stažení agenta závislostí

| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="connected-sources"></a>Připojené zdroje
Řešení Service Map získává data od agenta Microsoft Dependency. Agent závislostí závisí na agenta Log Analytics pro připojení ke službě Log Analytics. To znamená, že server musí mít nainstalovaný a nakonfigurovaný pomocí agenta závislostí agenta Log Analytics.  Následující tabulka popisuje připojené zdroje, které podporuje řešení Service Map.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Řešení Service Map analyzuje a shromažďuje data z počítačů s Windows. <br><br>Kromě [agenta Log Analytics pro Windows](../../azure-monitor/platform/log-analytics-agent.md), agenti Windows vyžadují agent služby Microsoft Dependency. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Agenti systému Linux | Ano | Řešení Service Map analyzuje a shromažďuje data z počítačů s Linuxem. <br><br>Kromě [agenta Log Analytics pro Linux](../../azure-monitor/platform/log-analytics-agent.md), vyžadují agent služby Microsoft Dependency agenti systému Linux. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Řešení Service Map analyzuje a shromažďuje data z agentů Windows a Linux v připojeného [skupiny pro správu System Center Operations Manager](../../azure-monitor/platform/om-agents.md). <br><br>Vyžaduje se přímé připojení z počítače s agentem nástroje System Center Operations Manager ke službě Log Analytics. |
| Účet služby Azure Storage | Ne | Řešení Service Map shromažďuje data z počítačů s agenty, takže není žádná data z něj získat ze služby Azure Storage. |

Na Windows, Microsoft Monitoring Agent (MMA) používá System Center Operations Manager a Log Analytics ke shromažďování a odesílání dat monitorování. (Tomuto agentovi, se nazývá agenta System Center Operations Manageru, agenta Log Analytics, MMA nebo přímý Agent, v závislosti na kontextu.) System Center Operations Manager a Log Analytics poskytují různé out v poli verze agenta MMA. Tyto verze dokážou podávat hlášení nástroji System Center Operations Manager, službě Log Analytics nebo oběma.  

V Linuxu, agenta Log Analytics pro Linux shromáždí a pošle monitorování dat do služby Log Analytics. Můžete použít Service Map na serverech s agentů Log Analytics se připojí přímo ke službě nebo, která se hlásí do skupiny pro správu nástroje Operations Manager integrovaný s Log Analytics.  

V tomto článku budeme odkazovat na všechny agenty, zda se systémem Linux nebo Windows připojené ke skupině pro správu System Center Operations Manager nebo přímo ke službě Log Analytics, jako *agenta Log Analytics*. 

Agent Service Map nepřenáší vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Data v Service Map je vždy přenášených v rámci agenta Log Analytics ve službě Log Analytics, buď přímo nebo přes bránu Log Analytics.

![Řešení Service Map agentů](media/service-map-configure/agents.png)

Pokud jste zákazníkem System Center Operations Manageru se skupiny pro správu připojené ke službě Log Analytics:

- Agenty System Center Operations Manager můžete získat přístup k Internetu pro připojení ke službě Log Analytics, není nutná žádná další konfigurace.  
- Agenty System Center Operations Manager nemůže získat přístup k Log Analytics přes Internet, musíte nakonfigurovat bránu Log Analytics pro práci s nástrojem System Center Operations Manager.
  
Pokud počítače Windows nebo Linuxem nemůžete připojit přímo ke službě, budete muset nakonfigurovat agenta Log Analytics pro připojení k pracovnímu prostoru Log Analytics pomocí brány. Další informace o tom, jak nasadit a nakonfigurovat bránu Log Analytics najdete v tématu [připojit počítače bez připojení k Internetu pomocí brány Log Analytics](../../azure-monitor/platform/gateway.md).  

### <a name="management-packs"></a>Sady Management Pack
Při řešení Service Map se aktivuje v pracovním prostoru Log Analytics, 300 KB management pack je předán do všech serverů Windows v daném pracovním prostoru. Pokud používáte agenty System Center Operations Manager v [připojené skupiny pro správu](../../azure-monitor/platform/om-agents.md), sada management pack Service Map bude nasazena ze System Center Operations Manager. 

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Je zapsán do %Programfiles%\Microsoft Monitoring Agent\Agent\Health služby State\Management Packs\. Zdroj dat, která používá sadu management pack je % Program files%\Microsoft monitorování Agent\Agent\Health služby State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="data-collection"></a>Shromažďování dat
Každý agent přenášet zhruba 25 MB za den, v závislosti na tom, jak komplexní jsou systémové závislosti můžete očekávat. Každý agent odešle data závislostí Service Map každých 15 sekund.  

Agent závislostí obvykle využívá 0,1 % systémové paměti a 0,1 % systému procesoru.

## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím vašeho používání služby Service Map. Tato data Microsoft používá k poskytování a vylepšování kvality, zabezpečení a integrity služby mapa služby. Data obsahují informace o konfiguraci vašeho softwaru, jako je operační systém a verze. Aby bylo možné poskytnout přesné a efektivní možnosti pro odstraňování potíží také obsahuje IP adresu, název DNS a název pracovní stanice. Neshromažďujeme jména, adresy ani jiné kontaktní údaje.

Další informace o shromažďování a používání dat najdete v článku [prohlášení o ochraně osobních údajů Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

## <a name="installation"></a>Instalace

### <a name="azure-vm-extension"></a>Rozšíření virtuálního počítače Azure
Rozšíření je k dispozici pro Windows (DependencyAgentWindows) i Linux (DependencyAgentLinux) a agenta závislostí můžete snadno nasadit na virtuální počítače Azure pomocí [rozšíření Azure VM](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Pomocí rozšíření virtuálního počítače Azure můžete nasadit agenta závislosti pro Windows a virtuální počítače s Linuxem pomocí skriptu prostředí PowerShell nebo přímo ve virtuálním počítači pomocí šablony Azure Resource Manageru.  Pokud provádíte nasazení agenta pomocí rozšíření virtuálního počítače Azure, agenty se automaticky aktualizují na nejnovější verzi.

Pokud chcete nasadit rozšíření virtuálního počítače Azure pomocí Powershellu, můžete použít v následujícím příkladu:

```PowerShell
#
# Deploy the Dependency agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Ujistěte se, že instalaci agenta závislosti na vašich virtuálních počítačů dokonce ještě jednodušší je vložit agenta, šablony Azure Resource Manageru.  Následující příklad kódu JSON lze přidat do *prostředky* vaší šablony.

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```

### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalace agenta závislostí na Microsoft Windows
Agent závislostí můžete nainstalovat ručně počítačích s Windows spuštěním `InstallDependencyAgent-Windows.exe`. Pokud spustíte tento spustitelný soubor bez jakýchkoli možností, spustí Průvodce instalací, který může sledovat instalace interaktivně.  

>[!NOTE]
>K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Použijte následující postup k instalaci agenta závislostí na každém počítači s Windows:

1.  Instalace agenta Log Analytics pro Windows, jednu z metod popsaných v následující [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Stáhnout agenta Windows a spusťte ho pomocí následujícího příkazu: 
    
    `InstallDependencyAgent-Windows.exe`

3.  Postupujte podle pokynů Průvodce instalací pro instalaci agenta.
4.  Pokud agenta závislostí nespustí, zkontrolujte protokoly podrobné informace o chybě. Adresář protokolu na agentech Windows nachází v %Programfiles%\Microsoft Agent\logs závislostí. 

#### <a name="windows-command-line"></a>Příkazový řádek Windows
K instalaci z příkazového řádku použijte parametry z následující tabulky. Spuštěním instalačního programu s následujícím parametrem /? zobrazíte seznam parametrů instalace.

    InstallDependencyAgent-Windows.exe /?

| Příznak | Popis |
|:--|:--|
| /? | Získá seznam parametrů příkazového řádku. |
| /S | Provede tichou instalaci bez zobrazení výzev uživateli. |

Soubory pro agenta závislostí Windows jsou umístěny v agenta závislostí C:\Program Files\Microsoft ve výchozím nastavení.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace agenta závislostí v Linuxu
Agenta závislostí je nainstalován na počítačích s Linuxem z `InstallDependencyAgent-Linux64.bin`, skript prostředí samorozbalovací binárním souborem. Soubor můžete spustit pomocí `sh` nebo přidejte oprávnění k souboru, samotné.

>[!NOTE]
> K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.

Instalace agenta závislostí na každý počítač s Linuxem pomocí následujících kroků:

1.  Instalace agenta Log Analytics, jednu z metod popsaných v následující [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).
2.  Instalace agenta závislostí Linux jako uživatel root spuštěním následujícího příkazu:
    
    `sh InstallDependencyAgent-Linux64.bin`

3.  Pokud agenta závislostí nespustí, zkontrolujte protokoly podrobné informace o chybě. Adresář protokolu na agentech pro Linux, je /var/opt/microsoft/dependency-agent/log.

Podívejte se do seznamu instalace příznaky, spustit instalaci aplikace pomocí – příznak následujícím způsobem.

    InstallDependencyAgent-Linux64.bin -help

| Příznak | Popis |
|:--|:--|
| – Nápověda | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| – Zkontrolujte | Zkontroluje oprávnění a operační systém bez instalace agenta. |

Soubory pro agenta závislostí jsou umístěny v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Příklady instalačního skriptu
Následující ukázkový skript je snadno nasazovat agenta závislosti na mnoha serverech najednou, k dispozici ke stažení a instalace agenta závislostí ve Windows nebo Linuxu.

### <a name="powershell-script-for-windows"></a>Skript PowerShellu pro Windows
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skript prostředí pro Linux
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```
## <a name="desired-state-configuration"></a>Konfigurace požadovaného stavu
Pokud chcete nasadit agenta závislostí pomocí Desired State Configuration (DSC), můžete použít modul xPSDesiredStateConfiguration pomocí následujícího ukázkového kódu:

```
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="remove-the-dependency-agent"></a>Odebrat agenta závislostí
### <a name="uninstall-agent-on-windows"></a>Odinstalujte agenta na Windows
Správce můžete odinstalovat agenta závislosti pro Windows pomocí ovládacích panelů.

Správce může také spustit %Programfiles%\Microsoft Agent\Uninstall.exe závislostí pro odinstalaci agenta závislostí.

### <a name="uninstall-agent-on-linux"></a>Odinstalace agenta v Linuxu
Agent závislostí můžete odinstalovat ze systému Linux pomocí následujícího příkazu.

RHEL, CentOs nebo Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```

## <a name="troubleshooting"></a>Řešení potíží
Pokud máte potíže s instalaci nebo spuštění řešení Service Map, tato část vám pomoct. Pokud stále nejde problém vyřešit, obraťte se prosím Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Problémy instalace agenta závislostí
#### <a name="installer-prompts-for-a-reboot"></a>Instalační program zobrazí výzvu k restartování
Agent závislostí *obecně* nevyžaduje restartování po instalaci nebo odinstalaci. Ale v některých výjimečných případech, Windows Server vyžaduje restartování počítače pokračujte s instalací. To se stane, když závislost, obvykle Microsoft Visual C++ Redistributable, vyžaduje restartování z důvodu soubor uzamčen.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Zpráva "nepovedlo se nainstalovat agenta závislostí: Visual Studio běhové knihovny se nepodařilo nainstalovat (kód = [číslo_účtu]) "se zobrazí

Agent Microsoft Dependency je založená na knihovnách modulu runtime Microsoft Visual Studio. Pokud dojde k problému při instalaci knihoven, zobrazí se zpráva. 

Instalační programy knihovny runtime vytvářet protokoly ve složce %LOCALAPPDATA%\temp. Soubor je dd_vcredist_arch_yyyymmddhhmmss.log, kde *arch* "x86" nebo "amd64" a *rrrrmmddhhmmss* je datum a čas (24hodinový formát), kdy byla vytvořena v protokolu. Protokol obsahuje podrobné informace o problému, který blokuje instalaci.

Může být užitečné k instalaci [nejnovější knihovny runtime](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) sami první.

V následující tabulce jsou uvedeny čísly kódů a doporučená řešení.

| Kód | Popis | Řešení |
|:--|:--|:--|
| 0x17 | Instalační program knihovny vyžaduje aktualizaci Windows, která nebyla nainstalována. | Vyhledejte v protokolu nejnovější instalační program knihovny.<br><br>Pokud je odkaz na "Windows8.1-KB2999226-x64.msu" následované řádku "Chyba 0x80240017: Nepovedlo se spustit MSU balíčku,"nemáte požadavky pro instalaci KB2999226. Postupujte podle pokynů v oddílu požadavky v [Universal C Runtime ve službě Windows](https://support.microsoft.com/kb/2999226). Může být potřeba spuštěním služby Windows Update a restartovat více než jednou. aby bylo možné nainstalujte příslušné požadované součásti.<br><br>Znovu spusťte instalační program agenta Microsoft Dependency. |

### <a name="post-installation-issues"></a>Po instalaci problémy
#### <a name="server-doesnt-appear-in-service-map"></a>Server se nezobrazí v Service Map
Pokud vaše instalace agenta závislostí proběhla úspěšně, ale nevidíte serveru v řešení Service Map:
* Agent závislostí úspěšné instalaci? Můžete si to ověřit tak, že kontroluje se, pokud je služba nainstalována a spuštěna.<br><br>
**Windows:** Vyhledejte službu s názvem "Agent služby Microsoft Dependency."<br>
**Linux:** Vyhledejte spuštěný proces "--agent služby microsoft dependency."

* Se nacházíte [bezplatné cenové úrovni Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Plán Free umožňuje až k pěti serverům jedinečné řešení Service Map. Všechny následující servery nezobrazí v Service Map, i v případě, že předchozí pět už odesílají data.

* Je odesílání protokolů serveru a data výkonu do Log Analytics? Přejděte na prohledávání protokolů a spusťte následující dotaz pro počítače: 

    Použití | Pokud počítač == "-název počítače | shrnutí sum(Quantity) any(QuantityUnit) podle datového typu

Obdrželi jste různých událostí ve výsledcích? Jsou data poslední? Pokud ano, agenta Log Analytics je správně funguje a komunikovat s Log Analytics. Pokud ne, zkontrolujte agenta na serveru: [Agenta log Analytics pro řešení potíží s Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) nebo [agenta Log Analytics pro řešení potíží pro Linux](../../azure-monitor/platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Server se zobrazí v Service Map, ale nemá žádné procesy
Pokud se zobrazí váš server v Service Map, ale nemá žádná data proces nebo připojení, která označuje, že agenta závislostí je nainstalovaná a spuštěná, ale ovladač jádra se nenačetla. 

Zkontrolujte soubor C:\Program Files\Microsoft závislost Agent\logs\wrapper.log (Windows) nebo soubor /var/opt/microsoft/dependency-agent/log/service.log (Linux). Poslední řádek souboru by měla zobrazovat, proč se nenačetla jádra. Například jádra nemusí být podporovány v Linuxu, pokud jste aktualizovali vaši jádra.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [použít Service Map]( service-map.md) poté, co byla nasazena a nakonfigurována.
