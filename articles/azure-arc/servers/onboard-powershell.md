---
title: Připojení hybridních počítačů k Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítač k Azure pomocí serverů s podporou ARC Azure. Můžete to provést pomocí PowerShellu.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831612"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Připojení hybridních počítačů k Azure pomocí PowerShellu

Pro servery s obloukem Azure ARC můžete provést ruční kroky, abyste je povolili pro jeden nebo víc počítačů se systémem Windows nebo Linux ve vašem prostředí. Alternativně můžete použít rutinu PowerShellu [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) ke stažení agenta připojeného počítače, instalaci agenta a registraci počítače pomocí ARC Azure. Rutina stáhne balíček Windows Agent (Instalační služba systému Windows) z webu Microsoft Download Center a balíčku agenta pro Linux z úložiště balíčků Microsoftu.

Tato metoda vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, jste členem místní skupiny Administrators. Tento proces můžete provést interaktivně nebo vzdáleně na Windows serveru pomocí [vzdálené komunikace PowerShellu](/powershell/scripting/learn/ps101/08-powershell-remoting).

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky. Informace o podporovaných oblastech a dalších souvisejících aspektech najdete v tématu [podporované oblasti Azure](overview.md#supported-regions).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

- Počítač se Azure PowerShell. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

Pomocí PowerShellu můžete spravovat rozšíření virtuálních počítačů na hybridních serverech spravovaných servery s podporou ARC Azure. Před použitím PowerShellu nainstalujte `Az.ConnectedMachine` modul. Na serveru, který je povolený pomocí ARC Azure, spusťte následující příkaz:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po dokončení instalace se zobrazí následující zpráva:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními.

2. Přihlaste se k Azure spuštěním příkazu `Connect-AzAccount` .

3. Chcete-li nainstalovat agenta připojeného počítače, použijte `Connect-AzConnectedMachine` `-Name` parametr s `-ResourceGroupName` parametry, a `-Location` . Pomocí `-SubscriptionId` parametru můžete přepsat výchozí předplatné v důsledku kontextu Azure vytvořeného po přihlášení. Spusťte jeden z následujících příkazů:

    * Pokud chcete nainstalovat agenta připojeného počítače na cílový počítač, který může přímo komunikovat s Azure, spusťte:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Chcete-li nainstalovat agenta připojeného počítače do cílového počítače, který komunikuje prostřednictvím proxy server, spusťte příkaz:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Ve Windows ověřte tento soubor: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. V systému Linux ověřte tento soubor: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Instalace a připojení pomocí vzdálené komunikace PowerShellu

Tady je postup konfigurace jednoho nebo více serverů Windows se servery, které jsou povolené pomocí ARC Azure. Na vzdáleném počítači je nutné povolit vzdálenou komunikaci prostředí PowerShell. Můžete to provést pomocí rutiny `Enable-PSRemoting`.

1. Otevřete konzolu PowerShellu jako správce.

2. Přihlaste se k Azure spuštěním příkazu `Connect-AzAccount` .

3. K instalaci agenta připojeného počítače použijte `Connect-AzConnectedMachine` s `-ResourceGroupName` `-Location` parametry a. Názvy prostředků Azure budou automaticky používat název hostitele každého serveru. Pomocí `-SubscriptionId` parametru můžete přepsat výchozí předplatné v důsledku kontextu Azure vytvořeného po přihlášení.

    * Pro instalaci agenta připojeného počítače na cílový počítač, který může přímo komunikovat do Azure, spusťte následující příkaz:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Chcete-li nainstalovat agenta připojeného počítače do několika vzdálených počítačů současně, přidejte seznam názvů vzdálených počítačů, každou oddělenou čárkou.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Následující příklad ukazuje výsledky příkazu, který cílí na jeden počítač:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po instalaci a konfiguraci agenta pro registraci u serverů s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte počítač v [Azure Portal](https://portal.azure.com).

![Snímek obrazovky s řídicím panelem serverů, který zobrazuje úspěšné připojení k serveru.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

* V případě potřeby si přečtěte téma [Poradce při potížích s agentem připojené počítače](troubleshoot-agent-onboard.md).

* Naučte se spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md). Můžete použít [konfiguraci hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověřit, že se počítač hlásí do očekávaného pracovního prostoru Log Analytics a povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/vm/vminsights-enable-policy.md).

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, nebo je spravovat pomocí Azure Automation runbooků nebo funkcí, jako je Update Management. Tento agent je taky nutný k používání dalších služeb Azure, například [Azure Security Center](../../security-center/security-center-introduction.md).
