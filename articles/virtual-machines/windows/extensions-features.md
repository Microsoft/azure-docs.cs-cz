---
title: Rozšíření virtuálního počítače a funkce pro Windows v Azure | Microsoft Docs
description: Zjistěte, jaká rozšíření jsou k dispozici pro virtuální počítače Azure, seskupené podle co se poskytují nebo zvýšit.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09eb2e723be80fd623ad53c9bda8271ef8846a0f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192430"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozšíření virtuálního počítače a funkce pro Windows

Rozšíření virtuálního počítače Azure se malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například pokud virtuální počítač vyžaduje instalace softwaru, ochrana proti virům nebo Docker konfigurace, rozšíření virtuálního počítače můžete použít k dokončení těchto úloh. Rozšíření virtuálního počítače Azure můžete spustit pomocí rozhraní příkazového řádku Azure, prostředí PowerShell, šablony Azure Resource Manager a portálu Azure. Rozšíření můžete dodávat s nové nasazení virtuálního počítače nebo spouštění všechny existující systém.

Tento dokument obsahuje přehled rozšíření virtuálního počítače, požadavků na používání rozšíření virtuálního počítače a pokyny o tom, jak zjistit, spravovat a odebrání rozšíření virtuálního počítače. Tento dokument obsahuje zobecněný informace, protože mnoho rozšíření virtuálního počítače nejsou k dispozici, každý s konfigurací potenciálně jedinečný. Podrobnosti o konkrétní rozšíření najdete v každý dokument specifické pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Nejsou k dispozici mnoho různých rozšíření virtuálního počítače Azure, každý s konkrétní případ použití. Jsou některé případy použití příklad:

- Použijte PowerShell požadovaná stav konfigurace virtuálního počítače pomocí rozšíření DSC pro Windows. Další informace najdete v tématu [stavu Azure požadovaná konfigurace rozšíření](extensions-dsc-overview.md).
- Konfigurace monitorování virtuálních počítačů pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [virtuálních počítačích Azure připojit k analýze protokolů](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurace sledování infrastruktury Azure s příponou Datadog. Další informace najdete v tématu [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Konfigurace virtuálního počítače Azure pomocí Chef. Další informace najdete v tématu [automatizace Azure nasazení virtuálního počítače s Chef](chef-automation.md).

Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače Windows a Linux. Rozšíření vlastních skriptů pro systém Windows umožňuje všech skriptů prostředí PowerShell ke spuštění na virtuálním počítači. To je užitečné při návrhu Azure nasazení, které vyžadují konfiguraci nad rámec jaké nativní Azure nástrojů může poskytnout. Další informace najdete v tématu [rozšíření skriptů vlastní virtuální počítač Windows](extensions-customscript.md).


## <a name="prerequisites"></a>Požadavky

Každé rozšíření virtuálního počítače může mít vlastní sadu požadavků. Například rozšíření virtuálního počítače Docker má předpokladem podporované distribuce systému Linux. Požadavky jednotlivých rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure
Agent virtuálního počítače Azure spravuje interakci mezi virtuální počítač Azure a kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačích Azure, včetně spuštění rozšíření virtuálního počítače. Agent virtuálního počítače Azure je předinstalován v Azure Marketplace bitové kopie a může být nainstalována na podporovaných operačních systémech.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálního počítače
Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, spusťte následující příkaz s modul Powershellu pro Azure Resource Manager. Ujistěte se, že pokud používáte tento příkaz zadejte požadované umístění.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálního počítače

Rozšíření virtuálního počítače Azure lze spustit na existující virtuální počítače, což je užitečné, když potřebujete udělat změny konfigurace nebo obnovit připojení již nasazené virtuálního počítače. Rozšíření virtuálního počítače můžete také dodávat s nasazení šablony Azure Resource Manager. Pomocí rozšíření pomocí šablony Resource Manageru můžete povolit virtuální počítače Azure k nasazení a nakonfigurování bez nutnosti zásahu po nasazení.

Tyto metody slouží ke spuštění rozšíření stávajícího virtuálního počítače.

### <a name="powershell"></a>PowerShell

Existuje několik příkazů prostředí PowerShell pro spouštění jednotlivých rozšíření. Pokud chcete zobrazit seznam, spusťte následující příkazy prostředí PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

To poskytuje výstup podobný následujícímu:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

Následující příklad používá rozšíření vlastních skriptů na stažení skriptu z úložiště Githubu do cílového virtuálního počítače a pak spusťte skript. Další informace o rozšíření vlastních skriptů najdete v tématu [přehled rozšíření vlastních skriptů](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

V tomto příkladu se používá rozšíření pro virtuální počítač přístup k resetování hesla pro správu systému Windows virtuálního počítače. Další informace o rozšíření pro přístup virtuálních počítačů najdete v tématu [služby Vzdálená plocha resetovat ve virtuálním počítači Windows](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` Příkaz můžete použít ke spuštění všech rozšíření virtuálního počítače. Další informace najdete v tématu [odkaz na sadu AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Rozšíření virtuálního počítače je použít pro existující virtuální počítač prostřednictvím portálu Azure. To pokud chcete udělat, vyberte virtuální počítač, který chcete použít, zvolte **rozšíření**a klikněte na tlačítko **přidat**. To poskytuje seznam dostupných rozšíření. Vyberte možnost, chcete a postupujte podle kroků v průvodci.

Následující obrázek znázorňuje instalaci rozšíření Microsoft Antimalware z portálu Azure.

![Nainstalujte rozšíření proti malwaru](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů můžete přidat do šablony Azure Resource Manager a provést při nasazení šablony. Nasazení rozšíření pomocí šablony je užitečné pro vytváření kompletně nakonfigurovaný Azure nasazení. Například následující kód JSON je převzat ze šablony Resource Manageru, která nasadí sada virtuálních počítačů s vyrovnáváním zatížení a Azure SQL database a poté nainstaluje aplikace .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače má na starosti instalace softwaru.

Další informace najdete v tématu [úplné šablony Resource Manageru](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Další informace najdete v tématu [šablon pro tvorbu Azure Resource Manageru pomocí rozšíření virtuálního počítače Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Když používáte rozšíření virtuálního počítače, může být nutné zahrnovat citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupových klíčů k účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která data šifruje a dešifruje ji pouze uvnitř cílového virtuálního počítače. Každé rozšíření obsahuje schéma konkrétní chráněné konfigurace, které budou popsané v dokumentaci konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro systém Windows. Všimněte si, že příkaz k provedení obsahuje sadu přihlašovacích údajů. V tomto příkladu spuštění příkazu se šifrovat nebude.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Zabezpečené spouštění řetězec přesunutím **příkaz k provedení** vlastnost, která má **chráněné** konfigurace.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálního počítače

Každé rozšíření virtuálního počítače může mít specifické pro řešení potíží. Například pokud používáte rozšíření vlastních skriptů, podrobnosti provádění skriptu naleznete místně na virtuálním počítači, na kterém byl rozšíření spustit. Kroky řešení potíží konkrétní rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

Následující kroky řešení potíží použít na všechny přípony virtuálního počítače.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače pro virtuální počítač, použijte následující příkaz prostředí PowerShell vrátit stav rozšíření. Názvy parametrů příkladu nahraďte vlastními hodnotami. `Name` Přebírá parametr daný název rozšíření v době provedení.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Výstup vypadá takto:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

Stav spuštění rozšíření možné také najít na portálu Azure. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**a vyberte požadované rozšíření.

### <a name="rerun-vm-extensions"></a>Znovu spustit, rozšíření virtuálního počítače

Můžou nastat případy, ve kterých musí být znovu rozšíření virtuálního počítače. To provedete pomocí odebírání rozšíření a potom znovu spustit rozšíření s metodu provádění podle svého výběru. Chcete-li odebrat rozšíření, spusťte následující příkaz s modulu Azure PowerShell. Názvy parametrů příkladu nahraďte vlastními hodnotami.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozšíření může být odebrán také pomocí portálu Azure. Postupujte následovně:

1. Vyberte virtuální počítač.
2. Vyberte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Vyberte **odinstalovat**.

## <a name="common-vm-extensions-reference"></a>Běžné odkaz rozšíření virtuálního počítače
| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Windows |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Rozšíření DSC pro Windows |Rozšíření prostředí PowerShell DSC (Desired State Configuration) |[Rozšíření DSC pro Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření diagnostiky Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření pro přístup virtuálních počítačů Azure |Spravovat uživatele a přihlašovací údaje |[Rozšíření pro přístup virtuálních počítačů pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
