---
title: Konfigurace mapy služeb v Azure | Microsoft Docs
description: Service Map je řešení v Azure, které automaticky zjišťuje komponenty aplikací v systémech Windows a Linux a mapuje komunikace mezi těmito službami. Tento článek obsahuje podrobné informace pro nasazení mapy služeb ve vašem prostředí a jejich použití v různých scénářů.
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2018
ms.author: daseidma;bwren
ms.openlocfilehash: 872d5f05e4d607c9445d1af5cc9b9cb984c19e11
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752570"
---
# <a name="configure-service-map-in-azure"></a>Konfigurace mapy služeb v Azure
Service Map automaticky rozpozná komponenty aplikace v systémech Windows a Linux a mapuje komunikaci mezi službami. Můžete ji zobrazit vaše servery co možná z nich – jako vzájemně propojena systémy, které doručují důležité služby. Mapy služeb zobrazí připojení mezi servery, procesy a porty mezi všechny architektura připojení TCP se žádná konfigurace vyžaduje, než instalace agenta.

Tento článek popisuje podrobnosti konfigurace agentů mapy služeb a registrace. Informace o používání mapy služeb najdete v tématu [pomocí mapy služeb řešení v Azure]( monitoring-service-map.md).

## <a name="dependency-agent-downloads"></a>Soubory závislého agenta ke stažení
| File | Operační systém | Verze | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |


## <a name="connected-sources"></a>Připojené zdroje
Mapa služeb získává data od agenta nástroje Microsoft závislostí. Připojení tohoto závislého agenta ke službě Log Analytics zajišťuje agent sady OMS. To znamená, že server musí mít agenta OMS nainstalovaný a nakonfigurovaný nejprve a pak může být nainstalován Agent závislostí. Následující tabulka popisuje připojených zdrojů, které podporuje řešení mapy služeb.

| Připojený zdroj | Podporováno | Popis |
|:--|:--|:--|
| Agenti systému Windows | Ano | Mapa služeb analyzuje a shromažďuje data z počítače se systémem Windows agenta. <br><br>Agenti systému Windows vyžadují kromě [agenta sady OMS](../log-analytics/log-analytics-windows-agent.md) také závislého agenta Microsoft. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Agenti systému Linux | Ano | Mapa služeb analyzuje a shromažďuje data z počítače se systémem Linux agent. <br><br>Agenti systému Linux vyžadují kromě [agenta sady OMS](../log-analytics/log-analytics-linux-agents.md) také závislého agenta Microsoft. Úplný seznam verzí operačních systémů najdete v [podporovaných operačních systémech](#supported-operating-systems). |
| Skupina pro správu nástroje System Center Operations Manager | Ano | Mapa služeb analyzuje a shromažďuje data z agentů systému Windows a Linux v připojeného [skupiny pro správu System Center Operations Manager](../log-analytics/log-analytics-om-agents.md). <br><br>Vyžaduje se přímé připojení z počítače s agentem nástroje System Center Operations Manager ke službě Log Analytics. Do pracovního prostoru analýzy protokolů se předají data ze skupiny pro správu.|
| Účet služby Azure Storage | Ne | Mapy služeb shromažďuje data z počítačů agentů, takže není žádná data z něj shromažďovat ze služby Azure Storage. |

Mapa služeb podporuje pouze 64bitové platformy.

V systému Windows, Microsoft Monitoring Agent (MMA) se používá System Center Operations Manager a analýzy protokolů pro shromažďování a odesílání dat monitorování. (Tomuto agentovi, se nazývá agenta System Center Operations Manager, OMS Agent, Agent analýzy protokolů, MMA nebo přímé agenta, v závislosti na kontextu.) System Center Operations Manager a analýzy protokolů poskytují různé na více systémů v poli verze MMA. Tyto verze dokážou podávat hlášení nástroji System Center Operations Manager, službě Log Analytics nebo oběma.  

V systému Linux, OMS agenta pro Linux shromáždí a odesílá data k analýze protokolů monitorování. Mapa služeb můžete použít na serverech s agenty přímé OMS nebo na servery, které jsou připojené k analýze protokolů prostřednictvím skupin pro správu System Center Operations Manager.  

V tomto článku budeme označovat všechny agenty – jestli Linux nebo Windows, zda připojené ke skupině pro správu System Center Operations Manager nebo přímo k Log Analytics – jako *agenta OMS*. Konkrétní název nasazení agenta použijeme jen v případě, kdy to bude zapotřebí kvůli kontextu.

Mapa služeb agenta nepřenáší samotná data a nevyžaduje žádné změny brány firewall nebo porty. Data v mapy služeb vždy přenášená agentem OMS k analýze protokolů, buď přímo nebo prostřednictvím brány OMS.

![Agenti mapy služeb](media/monitoring-service-map/agents.png)

Pokud jste zákazník s System Center Operations Manager s skupiny pro správu připojené k analýze protokolů:

- Pokud agenty nástroje System Center Operations Manager můžete získat přístup k Internetu, aby se připojení k analýze protokolů, žádná další konfigurace se nevyžaduje.  
- Pokud agenty nástroje System Center Operations Manager nemůže získat přístup k analýze protokolů přes Internet, budete muset nakonfigurovat bránu OMS pro práci s nástrojem System Center Operations Manager.
  
Pokud používáte přímé agenta OMS, musíte nakonfigurovat agenta OMS připojit se k analýze protokolů nebo k bráně OMS. Bránu OMS si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=52666). Další informace o tom, jak nasadit a nakonfigurovat bránu OMS najdete v tématu [připojte počítače bez přístupu k Internetu pomocí brány OMS](../log-analytics/log-analytics-oms-gateway.md).  

### <a name="management-packs"></a>Sady Management Pack
Po aktivaci mapy služeb v pracovním prostoru analýzy protokolů 300 KB management pack je odeslány na všechny servery Windows v něm. Pokud používáte System Center Operations Manager agentů v [připojené skupiny pro správu](../log-analytics/log-analytics-om-agents.md), z System Center Operations Manager je nasazena sada management pack mapy služeb. Pokud jsou agenti připojení přímo, doručí sadu Management Pack služba Log Analytics.

Tato sada Management Pack má název Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Je zapsán do %Programfiles%\Microsoft monitorování Agent\Agent\Health služby State\Management Packs\. Zdroje dat, který používá sada management pack je % Program files%\Microsoft monitorování Agent\Agent\Health služby State\Resources\<AutoGeneratedID > \ Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="installation"></a>Instalace
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Instalace agenta závislostí v systému Microsoft Windows
K instalaci a odinstalaci tohoto agenta se vyžadují oprávnění správce.

Na počítačích s Windows pomocí InstallDependencyAgent Windows.exe je nainstalován Agent nástroje závislostí. Pokud tento spustitelný soubor spustíte bez jakýchkoli parametrů, spustí se průvodce interaktivní instalací.  

Nainstalujte agenta závislost na každém počítači s Windows pomocí následujících kroků:

1.  Nainstalovat agenta OMS jednu z metod popsaných v následující [shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů](../log-analytics/log-analytics-concept-hybrid.md).
2.  Stáhnout agenta pro Windows a spusťte jej pomocí následujícího příkazu: <br>`InstallDependencyAgent-Windows.exe`
3.  Pomocí průvodce agenta nainstalujte.
4.  Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. Na agenty se systémem Windows k adresáři protokolu není %Programfiles%\Microsoft Agent\logs závislostí. 

#### <a name="windows-command-line"></a>Příkazový řádek Windows
K instalaci z příkazového řádku použijte parametry z následující tabulky. Spuštěním instalačního programu s následujícím parametrem /? zobrazíte seznam parametrů instalace.

    InstallDependencyAgent-Windows.exe /?

| Příznak | Popis |
|:--|:--|
| /? | Získá seznam parametrů příkazového řádku. |
| /S | Provede tichou instalaci bez zobrazení výzev uživateli. |

Soubory závislého agenta pro Windows jsou standardně umístěné zde: C:\Program Files\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Instalace závislého agenta v Linuxu
K instalaci nebo konfiguraci tohoto agenta se vyžaduje přístup uživatele root.

Závislý agent se do linuxových počítačů instaluje příkazem InstallDependencyAgent-Linux64.bin, což je skript prostředí se samorozbalovacím binárním souborem. Můžete spustit soubor s použitím dílet nebo přidat oprávnění ke samotném souboru.
 
Následujícím postupem nainstalujte závislého agenta na jednotlivé počítače s Linuxem:

1.  Nainstalovat agenta OMS jednu z metod popsaných v následující [shromažďovat data z počítačů ve vašem prostředí s analýzy protokolů](../log-analytics/log-analytics-concept-hybrid.md).
2.  Nainstalujte agenta závislostí Linux jako kořenového adresáře pomocí následujícího příkazu:<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Pokud se závislého agenta nepodaří spustit, najdete podrobné informace o chybě v protokolech. V agentech Linux k adresáři protokolu není /var/opt/microsoft/dependency-agent/log.

Chcete-li zobrazit seznam instalace příznaky, spustit instalaci programu-pomoci příznak následujícím způsobem.

    InstallDependencyAgent-Linux64.bin -help

| Příznak | Popis |
|:--|:--|
| – Nápověda | Získá seznam parametrů příkazového řádku. |
| -s | Provede tichou instalaci bez zobrazení výzev uživateli. |
| – Zkontrolujte | Zkontroluje oprávnění a operační systém bez instalace agenta. |

Soubory závislého agenta se nacházejí v následujících adresářích:

| Soubory | Umístění |
|:--|:--|
| Základní soubory | /opt/microsoft/dependency-agent |
| Soubory protokolu | /var/opt/microsoft/dependency-agent/log |
| Konfigurační soubory | /etc/opt/microsoft/dependency-agent/config |
| Spustitelné soubory služby | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binární soubory úložiště | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Příklady instalačního skriptu
Se snadným nasazením závislého agenta na mnoho serverů najednou vám pomůže skript. Následující příklady skriptů můžete použít ke stažení a instalaci závislého agenta do Windows nebo Linuxu.

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

## <a name="azure-vm-extension"></a>Rozšíření virtuálního počítače Azure
Můžete snadno nasadit agenta závislostí k virtuálním počítačům Azure pomocí [rozšíření virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).  Pomocí rozšíření virtuálního počítače Azure můžete nasadit agenta závislost na virtuální počítače pomocí skriptu prostředí PowerShell nebo přímo v šabloně Virtuálního počítače Azure Resource Manager.  Rozšíření není k dispozici pro Windows (DependencyAgentWindows) i Linux (DependencyAgentLinux).  Pokud nasazujete prostřednictvím rozšíření virtuálního počítače Azure, můžete agenty automaticky aktualizují na nejnovější verzi.

Pokud chcete nasadit rozšíření virtuálního počítače Azure pomocí prostředí PowerShell, můžete v následujícím příkladu:

```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
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

Zajistěte, aby byl Agent závislost na všech virtuálních počítačů i jednodušší je do šablony Azure Resource Manager zahrňte agenta.  Všimněte si, že Agent závislostí stále závisí na agenta OMS, proto [rozšíření virtuálního počítače agenta OMS](../virtual-machines/extensions/oms-linux.md) musí být nejprve nasazený.  Následující fragment JSON jde přidat do *prostředky* část šablony.

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


## <a name="desired-state-configuration"></a>Konfigurace požadovaného stavu
K nasazení závislého agenta prostřednictvím konfigurace požadovaného stavu můžete použít modul xPSDesiredStateConfiguration a několik následujících řádků kódu:

```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
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

## <a name="uninstallation"></a>Odinstalace
### <a name="uninstall-the-dependency-agent-on-windows"></a>Odinstalace závislého agenta ve Windows
Správce může závislého agenta pro Windows odinstalovat přes Ovládací panely.

Správce může závislého agenta odinstalovat také spuštěním souboru %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Odinstalace závislého agenta v Linuxu
Pomocí následujícího příkazu můžete odinstalovat agenta závislostí ze systému Linux.
<br>RHEL, CentOs nebo Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>Řešení potíží
Pokud máte potíže s instalaci nebo spuštění mapy služeb, v této části vám může pomoct. Pokud stále nemůžete vyřešit problém, kontaktujte prosím Microsoft Support.

### <a name="dependency-agent-installation-problems"></a>Problémy instalace agenta závislostí
#### <a name="installer-prompts-for-a-reboot"></a>Instalační program zobrazí výzvu k restartování
Agent závislostí *obecně* nevyžaduje restartování instalace nebo odinstalace. Ale v některých výjimečných případech, Windows Server vyžaduje restart pokračujte s instalací. To se stane, když závislost, obvykle Microsoft Visual C++ Redistributable, vyžaduje restartování počítače z důvodu uzamčení souborů.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>Zpráva "nelze pro instalaci agenta závislost: Visual Studio Runtime knihovny se nepodařilo nainstalovat (kód = [číslo_účtu])" se zobrazí

Agent služby Microsoft Dependency je založen na knihovny Microsoft Visual Studio runtime. Pokud dojde k problému při instalaci knihoven, zobrazí se zpráva s. 

Instalační programy knihovna modulu runtime vytvořit protokoly ve složce %LOCALAPPDATA%\temp. Soubor je dd_vcredist_arch_yyyymmddhhmmss.log, kde *architektura* "x86" nebo "amd64" a *rrrrmmddhhmmss* je datum a čas (24hodinovém formátu), kdy byla vytvořena v protokolu. Protokol obsahuje podrobné informace o problému, která blokuje instalaci.

Může být užitečné k instalaci [nejnovější modulu runtime knihoven](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) sami první.

Následující tabulka uvádí čísla kódu a návrhy řešení.

| Kód | Popis | Řešení |
|:--|:--|:--|
| 0x17 | Instalační program knihovny vyžaduje služby Windows update, který nebyl nainstalován. | Vyhledejte v protokolu nejnovější instalační program knihovny.<br><br>Pokud je odkaz na "Windows8.1-KB2999226-x64.msu" následovaný řádek "Chyba 0x80240017: spuštění MSU balíčku se nezdařilo" nemáte o požadavcích na instalaci KB2999226. Postupujte podle pokynů v části s předpoklady v [Universal C Runtime v systému Windows](https://support.microsoft.com/kb/2999226). Může být nutné spuštěním služby Windows Update a restartovat více než jednou. aby bylo možné nainstalovat požadované součásti.<br><br>Agent služby Microsoft Dependency instalační program spusťte znovu. |

### <a name="post-installation-issues"></a>Po instalaci problémy
#### <a name="server-doesnt-appear-in-service-map"></a>Server se nezobrazí v mapy služeb
Pokud vaše Agent služby Dependency instalace proběhla úspěšně, ale nevidíte serveru v řešení mapy služeb:
* Je závislost Agent nainstalován úspěšně? Můžete to ověřit tak, že zkontrolujete, zda je služba nainstalována a spuštěna.<br><br>
**Windows**: Podívejte se do služby s názvem "Agent služby Microsoft Dependency."<br>
**Linux**: Podívejte se na kterém běží procesem "microsoft závislost agent."

* Jste na [volné cenová úroveň Operations Management Suite/Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)? Plánu úrovně Free umožňuje až pět jedinečných serverů mapy služeb. Všechny následující servery nezobrazí v mapě služby i v případě, že předchozí pět už odesílají data.

* Je server odesílání protokolů a údajů o výkonu vzhledem k analýze protokolů? Přejděte do hledání protokolů a spusťte následující dotaz pro tento počítač: 

        * Computer="<your computer name here>" | measure count() by Type
        
  Obdrželi jste celou řadu událostí ve výsledcích? Je poslední data? Pokud ano, je agenta OMS fungování a komunikaci s analýzy protokolů. Pokud ne, vyhledejte agenta OMS na serveru: [řešení potíží s agentem OMS pro systém Windows](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues) nebo [agenta OMS pro řešení potíží s Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Server se zobrazí v mapy služeb, ale nemá žádné procesy
Pokud se zobrazí váš server v mapy služeb, ale nemá žádná data procesu nebo připojení, určující, že Agent závislostí je nainstalovaná a spuštěná, ale nebyla načíst ovladač jádra. 

Zkontrolujte soubor C:\Program Files\Microsoft závislostí Agent\logs\wrapper.log (Windows) nebo /var/opt/microsoft/dependency-agent/log/service.log soubor (Linux). Poslední řádky souboru by měl být uveden, proč nebylo načíst jádra. Například jádra nemusí být podporováno v systému Linux, je-li aktualizovat vaše jádra.

## <a name="data-collection"></a>Shromažďování dat
Můžete očekávat každého agenta k přenosu přibližně 25 MB za den, v závislosti na tom, jak komplexní jsou závislosti vašeho systému. Každý agent odesílá data závislostí mapy služeb každých 15 sekund.  

Agent závislostí obvykle spotřebuje 0,1 systémové paměti a procentem 0,1 systému procesoru.

## <a name="supported-azure-regions"></a>Podporované oblasti Azure
Mapa služeb je nyní k dispozici v následujících oblastech Azure:
- Východ USA
- Západní Evropa
- Západní střed USA
- Jihovýchodní Asie


## <a name="supported-operating-systems"></a>Podporované operační systémy
V následujících částech najdete seznam operačních systémů pro závislého agenta. Mapa služeb nepodporuje 32bitové architektury pro všechny operační systémy.

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Plocha Windows
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux a Oracle Linux (s jádrem RHEL)
- Jsou podporované jen verze s výchozím a SMP jádrem Linuxu.
- Verze s nestandardním jádrem, jako jsou PAE a Xen, nejsou podporované v žádné distribuci Linuxu. Například systém s verzí řetězec "2.6.16.21-0.8-xen" nepodporuje.
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.
- Jádro CentOSPlus není podporované.
- Jádro Oracle Unbreakable Enterprise (UEK) je popsané v další části tohoto článku.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Verze operačního systému | Verze jádra |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
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

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Verze operačního systému | Verze jádra |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- Vlastní jádra, včetně opětovně zkompilovaných standardních jader, nejsou podporovaná.

| Verze operačního systému | Verze jádra |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux s jádrem Unbreakable Enterprise Kernel
#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Verze operačního systému | Verze jádra
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Verze operačního systému | Verze jádra
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Verze operačního systému | Verze jádra
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>Diagnostická data a data použití
Microsoft automaticky shromažďuje data o využití a výkonu prostřednictvím používání služby mapy služeb. Tato data Microsoft používá k poskytování a zlepšování kvality, zabezpečení a integrity služby mapy služeb. Data zahrnují informace o konfiguraci vašeho softwaru, jako je verze operačního systému a. Zahrnuje taky IP adresu, název DNS a název pracovní stanice s cílem poskytnout přesná a efektivní možnosti pro odstraňování potíží. Neshromažďujeme jména, adresy ani jiné kontaktní informace.

Další informace o shromažďování a používání dat najdete v tématu [prohlášení o ochraně osobních údajů služeb Microsoft Online](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Další postup
- Zjistěte, jak [pomocí mapy služeb]( monitoring-service-map.md) po byla nasazena a nakonfigurována.
