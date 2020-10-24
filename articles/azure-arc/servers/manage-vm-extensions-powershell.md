---
title: Povolení rozšíření virtuálních počítačů pomocí Azure PowerShell
description: Tento článek popisuje, jak nasadit rozšíření virtuálních počítačů na servery s podporou ARC Azure běžícími v hybridních cloudových prostředích pomocí Azure PowerShell.
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2408f75c7b6d81ba297de6dcdb85a712cd8908f
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495448"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Povolení rozšíření virtuálních počítačů Azure pomocí Azure PowerShell

V tomto článku se dozvíte, jak nasadit a odinstalovat rozšíření virtuálních počítačů Azure, které podporují servery s podporou ARC Azure, na hybridní počítač se systémem Linux nebo Windows pomocí Azure PowerShell.

## <a name="prerequisites"></a>Předpoklady

- Počítač se Azure PowerShell. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

Předtím, než použijete Azure PowerShell ke správě rozšíření virtuálních počítačů na hybridním serveru spravovaném pomocí serverů s podporou ARC, je potřeba `Az.ConnectedMachine` modul nainstalovat. Na serveru s povoleným ARC spusťte následující příkaz:

`Install-Module -Name Az.ConnectedMachine`.

Po dokončení instalace se vrátí následující zpráva:

`The installed extension `AZ. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Povolit rozšíření

Pokud chcete povolit rozšíření virtuálního počítače na serveru s povoleným ARC, použijte [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) s `-Name` `-ResourceGroupName` parametry,, `-MachineName` , `-Location` , `-Publisher` ,- `ExtensionType` a `-Settings` .

Následující příklad povolí Log Analytics rozšíření virtuálního počítače na serveru Linux s povoleným ARC:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

Následující příklad povoluje rozšíření vlastních skriptů na serveru s povoleným ARC:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>Nainstalovaná rozšíření seznamu

Pokud chcete získat seznam rozšíření virtuálních počítačů na serveru s povoleným ARC, použijte [příkaz Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) s `-MachineName` `-ResourceGroupName` parametry a.

Příklad:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Odebrání nainstalovaného rozšíření

K odebrání nainstalovaného rozšíření virtuálního počítače na serveru s povoleným ARC použijte příkaz [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) s `-Name` `-MachineName` `-ResourceGroupName` parametry a.

Chcete-li například odebrat rozšíření virtuálního počítače Log Analytics pro Linux, spusťte následující příkaz:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Další kroky

- Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí rozhraní příkazového [řádku Azure CLI](manage-vm-extensions-cli.md)ze šablon [Azure Portal](manage-vm-extensions-portal.md)nebo [Azure Resource Manager](manage-vm-extensions-template.md).

- Informace o řešení potíží najdete v [Průvodci pro řešení potíží s rozšířeními virtuálních počítačů](troubleshoot-vm-extensions.md).
