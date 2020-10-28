---
title: Připojení hybridních počítačů k Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítač k Azure pomocí serverů s podporou ARC Azure pomocí PowerShellu.
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: bb114ec3e279a7ea696d834af8eb7240cb892dc1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891937"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Připojení hybridních počítačů k Azure pomocí PowerShellu

Můžete povolit servery s podporou ARC Azure pro jeden nebo víc počítačů se systémem Windows nebo Linux ve vašem prostředí provedením sady ručních kroků. Nebo můžete použít rutinu PowerShellu [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) ke stažení agenta připojeného počítače, instalaci agenta a registraci počítače pomocí ARC Azure. Rutina stáhne balíček Instalační služba systému Windows agenta pro Windows z Microsoft Download Center a balíčku agenta pro Linux z úložiště balíčků Microsoftu.

Tato metoda vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, jste členem místní skupiny Administrators. Tento proces můžete provést interaktivně nebo vzdáleně na Windows serveru pomocí [vzdálené komunikace PowerShellu](/powershell/scripting/learn/ps101/08-powershell-remoting).

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky. Informace o podporovaných oblastech a dalších souvisejících aspektech najdete v tématu [podporované oblasti Azure](overview.md#supported-regions).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

- Počítač se Azure PowerShell. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

Předtím, než použijete Azure PowerShell ke správě rozšíření virtuálních počítačů na hybridním serveru spravovaném pomocí serverů s podporou ARC, je potřeba `Az.ConnectedMachine` modul nainstalovat. Na serveru s povoleným ARC spusťte následující příkaz:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po dokončení instalace se vrátí následující zpráva:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Instalace agenta a připojení k Azure

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními.

2. Přihlaste se k Azure spuštěním příkazu `Connect-AzAccount` .

3. Chcete-li nainstalovat agenta připojeného počítače, použijte `Connect-AzConnectedMachine` `-Name` parametr s `-ResourceGroupName` parametry, a `-Location` . Pomocí `-SubscriptionId` parametru můžete přepsat výchozí předplatné v důsledku kontextu Azure vytvořeného po přihlášení. Spusťte jeden z následujících příkazů:

    * Pokud chcete nainstalovat agenta připojeného počítače na cílový počítač, který může přímo komunikovat s Azure, spusťte:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    * Chcete-li nainstalovat agenta připojeného počítače do cílového počítače, který komunikuje prostřednictvím proxy server, spusťte příkaz:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Ve Windows na *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* a na Linux na */var/opt/azcmagent/log/himds.log* .

## <a name="install-and-connect-using-powershell-remoting"></a>Instalace a připojení pomocí vzdálené komunikace PowerShellu

Pomocí následujících kroků nakonfigurujte cílový server Windows nebo počítač se servery s podporou ARC Azure. Na vzdáleném počítači musí být povolená Vzdálená komunikace prostředí PowerShell. Pomocí `Enable-PSRemoting` rutiny povolte vzdálenou komunikaci PowerShellu.

1. Otevřete konzolu PowerShellu jako správce.

2. Přihlaste se k Azure spuštěním příkazu `Connect-AzAccount` .

3. Chcete-li nainstalovat agenta připojeného počítače, použijte `Connect-AzConnectedMachine` `-Name` parametr s `-ResourceGroupName` parametry, a `-Location` . Pomocí `-SubscriptionId` parametru můžete přepsat výchozí předplatné v důsledku kontextu Azure vytvořeného po přihlášení.

Pro instalaci agenta připojeného počítače na cílový počítač, který může přímo komunikovat do Azure, spusťte následující příkaz:

```azurepowershell
$session = Connect-PSSession -ComputerName myMachineName
Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
```

V následujícím příkladu jsou výsledky příkazu:

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

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://portal.azure.com).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).