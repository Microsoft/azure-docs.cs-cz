---
title: Připojení hybridních počítačů k Azure pomocí PowerShellu
description: V tomto článku se dozvíte, jak nainstalovat agenta a připojit počítač k Azure pomocí serverů s podporou ARC Azure pomocí PowerShellu.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374760"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Připojení hybridních počítačů k Azure pomocí PowerShellu

Můžete povolit servery s podporou ARC Azure pro jeden nebo malý počet počítačů s Windows nebo Linux ve vašem prostředí provedením sady kroků ručně. Nebo můžete použít rutinu PowerShellu [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Tato rutina provádí následující akce:

- Nakonfiguruje hostitelský počítač ke stažení agenta pro Windows z webu Microsoft Download Center a balíčku agenta pro Linux z packages.microsoft.com.
- Nainstaluje agenta připojeného počítače.
- Registrace počítače pomocí ARC Azure

Tato metoda vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, jste členem místní skupiny Administrators.

Než začnete, zkontrolujte [požadavky](agent-overview.md#prerequisites) a ověřte, že vaše předplatné a prostředky splňují požadavky. Informace o podporovaných oblastech a dalších souvisejících aspektech najdete v tématu [podporované oblasti Azure](overview.md#supported-regions).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

- Počítač se Azure PowerShell. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

Předtím, než použijete Azure PowerShell ke správě rozšíření virtuálních počítačů na hybridním serveru spravovaném pomocí serverů s podporou ARC, je potřeba `Az.ConnectedMachine` modul nainstalovat. Na serveru s povoleným ARC spusťte následující příkaz:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Po dokončení instalace se vrátí následující zpráva:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Instalace a ověření agenta ve Windows

1. Otevřete konzolu PowerShellu jako správce.

2. Přihlaste se k Azure spuštěním příkazu `Connect-AzAccount` .

3. Chcete-li nainstalovat agenta připojeného počítače, použijte `Connect-AzConnectedMachine` `-Name` parametr s `-ResourceGroupName` parametry, a `-Location` . Pomocí `-SubscriptionId` parametru můžete přepsat výchozí předplatné v důsledku kontextu Azure vytvořeného po přihlášení.

    Pro instalaci agenta připojeného počítače na cílový počítač, který může přímo komunikovat do Azure, spusťte následující příkaz::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Pokud cílový počítač komunikuje prostřednictvím proxy server, spusťte následující příkaz:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Pokud se agent po dokončení instalace nepovede spustit, podívejte se do protokolů, kde najdete podrobnější informace o chybě. Ve Windows na *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*a na Linux na */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte si počítače na webu [Azure Portal](https://portal.azure.com).

![Úspěšné připojení k serveru](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Další kroky

* Informace o řešení potíží najdete v části [Poradce při potížích s agentem připojeného počítače](troubleshoot-agent-onboard.md).

* Naučte se, jak spravovat počítač pomocí [Azure Policy](../../governance/policy/overview.md), jako je [Konfigurace hosta](../../governance/policy/concepts/guest-configuration.md)virtuálního počítače, ověření, že se počítač hlásí k očekávanému log Analyticsmu pracovnímu prostoru, povolit monitorování pomocí [Azure monitor s virtuálními počítači](../../azure-monitor/insights/vminsights-enable-policy.md)a mnohem víc.

* Přečtěte si další informace o [agentovi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics pro systém Windows a Linux je vyžadován, pokud chcete shromažďovat data o monitorování operačního systému a úloh, spravovat je pomocí runbooků nebo funkcí automatizace, jako je Update Management, nebo použít jiné služby Azure, jako je [Azure Security Center](../../security-center/security-center-introduction.md).