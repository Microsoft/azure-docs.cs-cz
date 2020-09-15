---
title: Řešení potíží s agentem hosta systému Windows Azure
description: Řešení potíží s agentem hosta systému Windows Azure nepracuje s problémy
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 597ea6e7ff7dbcfcb8a99d4e4de3c1b82915ee07
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561257"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Řešení potíží s agentem hosta systému Windows Azure

Agent hosta Windows Azure je agent virtuálního počítače (VM). Umožňuje virtuálnímu počítači komunikovat s řadičem prostředků infrastruktury (základní fyzický server, na kterém je hostovaný virtuální počítač), na IP adrese 168.63.129.16. Jedná se o virtuální veřejnou IP adresu, která usnadňuje komunikaci. Další informace najdete v tématu [co je IP adresa 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

 Virtuální počítač, který se migruje do Azure z místního prostředí nebo který se vytvoří pomocí přizpůsobené image, nemá nainstalovaný Agent hosta systému Windows Azure. V těchto scénářích musíte agenta virtuálního počítače nainstalovat ručně. Další informace o instalaci agenta virtuálního počítače najdete v tématu [Přehled agenta virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

Po úspěšné instalaci agenta hosta Windows Azure se můžete na virtuálním počítači podívat na tyto služby, které jsou uvedené v části Services. msc:
 
- Služba agenta hosta systému Windows Azure
- Služba telemetrie
- Služba agenta VP

**Služba agenta hosta systému Windows Azure**: Tato služba je služba, která zodpovídá za všechna protokolování v WAppAgent. log. Tato služba zodpovídá za konfiguraci různých rozšíření a komunikace z hosta na hostitele. 

**Služba telemetrie**: Tato služba zodpovídá za posílání dat telemetrie virtuálního počítače na back-end Server.

**Služba agenta VP**: Tato služba zodpovídá za instalaci hostovaného agenta. Transparentní instalační program je také součástí agenta VP, který pomáhá upgradovat další součásti a služby hostovaného agenta. RDAgent je také zodpovědný za odesílání prezenčních signálů z virtuálního počítače hosta do agenta hostitele na fyzickém serveru.

> [!NOTE]
> Počínaje verzí 2.7.41491.971 agenta hosta virtuálního počítače je součást telemetrie zahrnutá ve službě RDAgent, proto se tato služba telemetrie nemusí zobrazit v nově vytvořených virtuálních počítačích.

## <a name="checking-agent-status-and-version"></a>Kontroluje se stav a verze agenta.

V Azure Portal na stránce vlastností virtuálního počítače vyberte a ověřte **Stav agenta**. Pokud Agent hosta systému Windows Azure pracuje správně, zobrazí se stav **připraveno**. Pokud je agent virtuálního počítače v **NEPŘIPRAVENÉM** stavu, nebudou rozšíření a **příkaz Spustit** na Azure Portal fungovat.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Řešení potíží s agentem virtuálního počítače, který není ve stavu připraveno

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Krok 1 zkontroluje, jestli je nainstalovaná služba Windows Azure Host agent.

- Vyhledat balíček

    Vyhledejte složku C:\WindowsAzure. Pokud se zobrazí složka GuestAgent, která zobrazuje číslo verze, to znamená, že na virtuálním počítači je nainstalovaný Agent hosta Windows Azure. Můžete také vyhledat nainstalovaný balíček.  Pokud je na virtuálním počítači nainstalovaný Agent hosta Windows Azure, balíček se uloží do následujícího umístění: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Spuštěním následujícího příkazu PowerShellu můžete ověřit, jestli je na virtuálním počítači nasazený agent virtuálního počítače:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    Ve výstupu vyhledejte vlastnost **ProvisionVMAgent** a ověřte, zda je hodnota nastavena na **hodnotu true**. Pokud je to, znamená to, že agent je nainstalovaný na virtuálním počítači.
    
- Kontrolovat služby a procesy

   Přejdete do konzoly služby (Services. msc) a zkontrolujete stav těchto služeb: Služba agenta hosta systému Windows Azure, služba RDAgent, služba Windows Azure telemetrie a služba Windows Azure Network Agent.
 
    Můžete také ověřit, jestli tyto služby běží, kontrolou Správce úloh pro následující procesy:
       
    - WindowsAzureGuestAgent.exe: Služba agenta hosta systému Windows Azure
    - WaAppAgent.exe: služba RDAgent
    - WindowsAzureNetAgent.exe: služba Windows Azure Network Agent
    - WindowsAzureTelemetryService.exe: Služba Microsoft Azure telemetrie

   Pokud tyto procesy a služby nemůžete najít, znamená to, že nemáte nainstalovaný Agent hosta Windows Azure.

- Zjištění programu a funkce

    V Ovládacích panelech přejdete na **programy a funkce** , abyste zjistili, jestli je nainstalovaná služba Windows Azure Host agent.

Pokud nenajdete žádné balíčky, služby a procesy spuštěné a nevidíte v části programy a funkce agenta hosta Windows Azure, zkuste [nainstalovat službu agenta hosta systému Windows Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Pokud se Agent hosta nenainstaluje správně, můžete [agenta virtuálního počítače nainstalovat offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).

Pokud vidíte služby a jsou spuštěné, restartujte službu, která zjistí, zda byl problém vyřešen. Pokud jsou služby zastaveny, spusťte je a počkejte několik minut. Potom zkontrolujte, zda je **Stav agenta** hlášen jako **připravený**. Pokud zjistíte, že se tyto služby zhroutí, můžou tyto služby způsobit zhroucení některých procesů třetích stran. Pokud chcete tento problém vyřešit, obraťte se na [Podpora Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Krok 2: ověření, jestli funguje automatické aktualizace

Agent hosta systému Windows Azure obsahuje funkci Automatické aktualizace. Bude automaticky kontrolovat nové aktualizace a instalovat je. Pokud funkce Automatické aktualizace nefunguje správně, zkuste odinstalovat a nainstalovat agenta hosta Windows Azure pomocí následujících kroků:

1. Pokud se v části **programy a funkce**objeví Agent hosta systému Windows Azure, odinstalujte agenta hosta systému Windows Azure.

2. Otevřete okno příkazového řádku, které má oprávnění správce.

3. Zastavte služby agenta hosta. Pokud se služby nezastaví, musíte nastavit služby na **Ruční spuštění** a pak restartovat virtuální počítač.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Odstraňte služby agenta hosta:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. V části `C:\WindowsAzure` vytvořit složku s názvem Old.

1. Přesuňte všechny složky, které jsou pojmenované balíčky nebo GuestAgent, do původní složky.

1. Nejnovější verzi instalačního balíčku agenta si [můžete](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409)stáhnout a nainstalovat odsud. K dokončení instalace musíte mít oprávnění správce.

1. Nainstalujte agenta hosta pomocí následujícího příkazu:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Potom zkontrolujte, zda se služba agenta hosta spouští správně.
 
    Ve výjimečných případech, kdy se Agent hosta nenainstaluje správně, můžete [agenta virtuálního počítače nainstalovat offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Krok 3 – ověření, jestli se virtuální počítač může připojit k řadiči prostředků infrastruktury

Pomocí nástroje, jako je PsPing, otestujte, jestli se virtuální počítač může připojit k 168.63.129.16 na portech 80, 32526 a 443. Pokud se virtuální počítač nepřipojí podle očekávání, ověřte, jestli je v místní bráně firewall na VIRTUÁLNÍm počítači otevřená odchozí komunikace přes porty 80, 443 a 32526. Pokud je tato IP adresa zablokovaná, může agent virtuálního počítače v nejrůznějších scénářích zobrazit neočekávané chování.

## <a name="advanced-troubleshooting"></a>Řešení potíží na pokročilé úrovni

Události pro řešení potíží s agentem hosta systému Windows Azure se zaznamenávají do následujících souborů protokolu:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Následuje několik běžných scénářů, ve kterých může Agent hosta systému Windows Azure zadat stav **Nepřipraveno** nebo přestat fungovat podle očekávání.

### <a name="agent-stuck-on-starting"></a>Zablokování agenta při spuštění

V protokolu WaAppAgent vidíte, že je agent zablokovaný na spouštěcím procesu a nemůže se spustit.

**Informace protokolu**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent Start. 2.7.41491.901 verze

**Výsledcích**
 
Virtuální počítač stále používá starší verzi agenta hosta systému Windows Azure. Ve složce C:\WindowsAzure si můžete všimnout, že jsou nainstalovaná více instancí agenta hosta systému Windows Azure, včetně několika stejných verzí. Vzhledem k tomu, že je nainstalováno více instancí agentů, virtuální počítač nespustí nejnovější verzi agenta hosta systému Windows Azure.

**Řešení**

Ručně odinstalujte agenta hosta Windows Azure a pak ho znovu nainstalujte pomocí následujících kroků:

1. Otevřete ovládací panely > programy a funkce a odinstalujte agenta hosta systému Windows Azure.
1. Otevřete Správce úloh a zastavte následující služby: Služba agenta hosta systému Windows Azure, služba RDAgent, služba Windows Azure telemetrie a služba Windows Azure Network Agent.
1. V části C:\WindowsAzure vytvořte složku s názvem OLD.
1. Přesuňte všechny složky, které jsou pojmenované balíčky nebo GuestAgent, do původní složky. Také přesuňte všechny složky GuestAgent v C:\WindowsAzure\logs, které začínají jako GuestAgent_x. x. xxxxx do původní složky.
1. Stáhněte a nainstalujte si nejnovější verzi agenta MSI. K dokončení instalace musíte mít oprávnění správce.
1. Nainstalujte agenta hosta pomocí následujícího příkazu MSI:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Ověřte, že jsou teď spuštěné RDAgent, Agent hosta Windows Azure a služby Microsoft Azure telemetrie.
 
1. Zkontrolujte protokol WaAppAgent. log a ujistěte se, že je spuštěná nejnovější verze agenta hosta Windows Azure.
 
1. Odstraňte STARou složku v C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Nepovedlo se připojit k IP adrese WireServer (IP adresa hostitele). 

Všimněte si následujících chybových záznamů v WaAppAgent. log a telemetrie. log:

**Informace protokolu**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Výsledcích**

Virtuální počítač se nemůže připojit k hostitelskému serveru wireserver.

**Řešení**

1. Vzhledem k tomu, že wireserver není dosažitelný, připojte se k VIRTUÁLNÍmu počítači pomocí vzdálené plochy a potom se pokuste získat přístup k následující adrese URL z internetového prohlížeče: http://168.63.129.16/?comp=versions 
1. Pokud se nemůžete spojit s adresou URL z kroku 1, zkontrolujte síťové rozhraní, abyste zjistili, jestli je nastavený jako DHCP s povoleným serverem DNS. Chcete-li zjistit stav služby DHCP v síťovém rozhraní, spusťte následující příkaz:  `netsh interface ip show config` .
1. Pokud je server DHCP zakázán, spusťte následující příkaz, který zajistí změnu hodnoty žlutě na název vašeho rozhraní: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Vyhledejte všechny problémy, které by mohly být způsobeny bránou firewall, proxy serverem nebo jiným zdrojem, které by mohly blokovat přístup k IP adrese 168.63.129.16.
1. Ověřte, zda brána Windows Firewall nebo brána firewall jiného výrobce blokují přístup k portům 80, 443 a 32526. Další informace o tom, proč by se tato adresa neměla zablokovat, najdete v tématu [co je IP adresa 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

### <a name="guest-agent-is-stuck-stopping"></a>Agent hosta je zablokovaný jako zastavení.  

Všimněte si, že následující položky chyb v WaAppAgent. log:

**Informace protokolu** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Výsledcích**

Agent hosta Windows Azure se zablokuje při zastavování.

**Řešení**

1. Ujistěte se, že na virtuálním počítači běží WaAppAgent.exe. Pokud není spuštěný, restartujte službu rdgagent a počkejte pět minut. Když je spuštěný WaAppAgent.exe, ukončete proces WindowsAzureGuest.exe.
2. Pokud krok 1 problém nevyřeší, odeberte aktuálně nainstalovanou verzi a nainstalujte nejnovější verzi agenta ručně.

### <a name="npcap-loopback-adapter"></a>Adaptér zpětné smyčky Npcap 

Všimněte si, že následující položky chyb v WaAppAgent. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Výsledcích**

Na virtuálním počítači je nainstalovaný adaptér Npcap Loopback pomocí nástroje Wireshark. Wireshark je open source nástroj pro profilaci síťového provozu a analýzu paketů. Takový nástroj se často označuje jako analyzátor sítě, analyzátor síťového protokolu nebo sledování.

**Řešení**

Nástroj WireShark může nainstalovat adaptér Npcap Loopback. Zkuste ho zakázat a potom zkontrolujte, jestli je problém vyřešený.

## <a name="next-steps"></a>Další kroky

Pokud chcete dále řešit potíže s "agentem hosta Windows Azure nefunguje", obraťte se na [podporu Microsoftu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
