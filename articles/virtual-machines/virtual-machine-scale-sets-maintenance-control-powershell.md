---
title: Řízení údržby pro upgrady imagí operačního systému na Azure Virtual Machine Scale Sets pomocí PowerShellu
description: Naučte se řídit, kdy se mají automatické upgrady imagí operačního systému zavádět do služby Azure Virtual Machine Scale Sets pomocí řízení údržby a PowerShellu.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 62b2fd0554c218b733501805779745049d3b8c92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532607"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Verze Preview: řízení údržby pro upgrady imagí operačního systému na Azure Virtual Machine Scale Sets pomocí PowerShellu

Řízení údržby vám umožní určit, kdy se mají použít automatické upgrady imagí hostovaného operačního systému na vaše sady škálování virtuálních počítačů. Toto téma popisuje možnosti Azure PowerShell pro řízení údržby. Další informace o použití řízení údržby najdete v tématu [řízení údržby pro Azure Virtual Machine Scale Sets](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Řízení údržby pro upgrady imagí operačního systému v Azure Virtual Machine Scale Sets je momentálně v Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="enable-the-powershell-module"></a>Povolit modul prostředí PowerShell

Ujistěte `PowerShellGet` se, že je aktuální.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Nainstalujte `Az.Maintenance` modul prostředí PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Pokud instalujete místně, ujistěte se, že jste otevřeli příkazový řádek PowerShellu jako správce.

Může se zobrazit také výzva k potvrzení, že chcete nainstalovat z *nedůvěryhodného úložiště*. Zadejte `Y` nebo vyberte **Ano pro všechny** pro instalaci modulu.

## <a name="connect-to-an-azure-account"></a>Připojení k účtu Azure

Připojte se k požadovanému účtu Azure pomocí [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) a [set-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Vytvoření konfigurace údržby

Vytvořte skupinu prostředků jako kontejner pro vaši konfiguraci. V tomto příkladu se vytvoří skupina prostředků s názvem *myMaintenanceRG* v *eastus2*. Pokud již máte skupinu prostředků, kterou chcete použít, můžete tuto část přeskočit. Stačí nahradit název skupiny prostředků vlastními ve zbývajících příkladech.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Pomocí [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) vytvořte konfiguraci údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* , která je vymezená na bitovou kopii operačního systému. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> **Doba trvání** údržby musí být *5 hodin* nebo déle. **Opakování** údržby musí být nastavené na *den*.

Pomocí nástroje `-MaintenanceScope OSImage` je zajištěno, že se konfigurace údržby používá pro řízení aktualizací hostovaného operačního systému.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurace musí být pro vaši skupinu prostředků jedinečné.

K dostupným konfiguracím údržby se můžete dotázat pomocí příkazu [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Přidružte sadu škálování virtuálního počítače ke konfiguraci údržby.

Sada škálování virtuálního počítače se dá přidružit k jakékoli konfiguraci údržby bez ohledu na oblast a předplatné konfigurace údržby. Díky odchodu na konfiguraci údržby se nové aktualizace image operačního systému pro sadu škálování automaticky naplánovaly na další dostupné časové období údržby.

Pomocí [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) přidružte svoji škálu virtuálních počítačů ke konfiguraci údržby.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Povolit automatický upgrade operačního systému

Pro každou sadu škálování virtuálního počítače, která se chystá používat řízení údržby, můžete povolit automatické upgrady operačního systému. Další informace o tom, jak povolit automatické upgrady operačního systému v sadě škálování virtuálních počítačů, najdete v dokumentu věnovaném [automatickému upgradu](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) operačního systému pro virtuální počítače Azure. 


## <a name="next-steps"></a>Další kroky

Seznamte se s údržbou a aktualizacemi pro virtuální počítače běžící v Azure.

> [!div class="nextstepaction"]
> [Údržba a aktualizace](maintenance-and-updates.md)
