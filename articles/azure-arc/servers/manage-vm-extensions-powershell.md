---
title: Povolení rozšíření virtuálních počítačů pomocí Azure PowerShell
description: Tento článek popisuje, jak nasadit rozšíření virtuálních počítačů na servery s podporou ARC Azure běžícími v hybridních cloudových prostředích pomocí Azure PowerShell.
ms.date: 11/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6b38c425042c260a29682db11212a1f6324abd38
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387406"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Povolení rozšíření virtuálních počítačů Azure pomocí Azure PowerShell

V tomto článku se dozvíte, jak nasadit a odinstalovat rozšíření virtuálních počítačů Azure, které podporují servery s podporou ARC Azure, na hybridní počítač se systémem Linux nebo Windows pomocí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

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
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentforLinux"
```

Následující příklad povoluje rozšíření vlastních skriptů na serveru s povoleným ARC:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Rozšíření virtuálního počítače Key Vault (Preview)

> [!WARNING]
> Klienti PowerShellu se často přidávají `\` do `"` v settings.js, na kterých bude akvvm_service selže s chybou: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Následující příklad povoluje rozšíření Key Vault VM (Preview) na serveru s povoleným ARC:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
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
