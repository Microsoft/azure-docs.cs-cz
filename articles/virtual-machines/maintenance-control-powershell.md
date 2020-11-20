---
title: Řízení údržby pro virtuální počítače Azure pomocí PowerShellu
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržba pomocí řízení údržby a PowerShellu.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: f33cb7d4d005f15d0a5fcc70d56ebd4698f86694
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988217"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Řízení aktualizací pomocí řízení údržby a Azure PowerShell

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure. Toto téma popisuje možnosti Azure PowerShell pro řízení údržby. Další informace o výhodách použití řízení údržby, jejich omezení a dalších možností správy najdete v tématu [Správa aktualizací platformy pomocí řízení údržby](maintenance-control.md).
 
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


## <a name="create-a-maintenance-configuration"></a>Vytvoření konfigurace údržby

Vytvořte skupinu prostředků jako kontejner pro vaši konfiguraci. V tomto příkladu se vytvoří skupina prostředků s názvem *myMaintenanceRG* v *eastus*. Pokud již máte skupinu prostředků, kterou chcete použít, můžete tuto část přeskočit a nahradit název skupiny prostředků vlastní ve zbývajících příkladech.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Pomocí [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) vytvořte konfiguraci údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s oborem názvů hostitele. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Pomocí nástroje `-MaintenanceScope host` je zajištěno, že se konfigurace údržby používá pro řízení aktualizací hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurace musí být pro vaši skupinu prostředků jedinečné.

K dostupným konfiguracím údržby se můžete dotázat pomocí příkazu [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Vytvoření konfigurace údržby pomocí plánovaného okna

Můžete také deklarovat naplánované okno, když Azure použije aktualizace vašich prostředků. Tento příklad vytvoří konfiguraci údržby s názvem myConfig s plánovaným oknem 5 hodin ve čtvrtém pondělí každého měsíce. Po vytvoření naplánovaného okna už aktualizace nemusíte instalovat ručně.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> **Doba trvání** údržby musí být *2 hodiny* nebo déle. **Opakování** údržby musí být nastavené na nejméně jednou za 35 dní.

**Opakování** údržby může být vyjádřeno jako denní, týdenní nebo měsíční. Tady je několik příkladů:
 - denně – "recurEvery: Day" **nebo** "RecurEvery: 3Days" 
 - týdně – "recurEvery: 3Weeks" **nebo** "RecurEvery: týden Sobota, neděle" 
 - měsíčně – "recurEvery: month day23, day24" **nebo** "RecurEvery: měsíc poslední neděle" **nebo** "recurEvery: měsíc čtvrtého pondělí"  
      

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Pomocí [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) přiřaďte konfiguraci k vašemu IZOLOVANÉmu virtuálnímu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Použijte konfiguraci na virtuální počítač s použitím ID konfigurace. Zadejte `-ResourceType VirtualMachines` a zadejte název virtuálního počítače pro `-ResourceName` a skupinu prostředků virtuálního počítače pro `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Chcete-li použít konfiguraci na vyhrazeném hostiteli, je nutné zahrnout také `-ResourceType hosts` `-ResourceParentName` název skupiny hostitelů a `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Vyhledat nedokončené aktualizace

Pokud chcete zjistit, jestli čekají na aktualizace, použijte [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) . Pomocí `-subscription` Zadejte předplatné Azure pro virtuální počítač, pokud se liší od přihlášení, které jste přihlásili.

Pokud nejsou k dispozici žádné aktualizace k zobrazení, tento příkaz nebude nic vracet. V opačném případě vrátí objekt PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vyhledejte nedokončené aktualizace pro izolovaný virtuální počítač. V tomto příkladu je výstup formátovaný jako tabulka pro čitelnost.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Vyhrazený hostitel

Pro kontrolu nedokončených aktualizací pro vyhrazeného hostitele. V tomto příkladu je výstup formátovaný jako tabulka pro čitelnost. Nahraďte hodnoty pro prostředky vlastními.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Instalace aktualizací

Použijte [příkaz New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) k instalaci nedokončených aktualizací.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vytvořte žádost o použití aktualizací pro izolovaný virtuální počítač.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Po úspěšném provedení tohoto příkazu vrátí `PSApplyUpdate` objekt. Můžete použít atribut Name v `Get-AzApplyUpdate` příkazu ke kontrole stavu aktualizace. Podívejte se na téma [Zkontrolujte stav aktualizace](#check-update-status).

### <a name="dedicated-host"></a>Vyhrazený hostitel

Použijte aktualizace na vyhrazeného hostitele.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Kontrolovat stav aktualizace
Ke kontrole stavu aktualizace použijte [příkaz Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) . Níže uvedené příkazy zobrazují stav nejnovější aktualizace pomocí `default` `-ApplyUpdateName` parametru pro parametr. Můžete nahradit název aktualizace (vrácenou příkazem [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) ) a získat tak stav konkrétní aktualizace.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime bude čas, kdy se aktualizace dokončila, ať už iniciovaná vámi, nebo platformou v případě, že se okno samoobslužné údržby nepoužilo. Pokud se v rámci řízení údržby nikdy nepoužila aktualizace, zobrazí se výchozí hodnota.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vyhledat aktualizace konkrétního virtuálního počítače.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Vyhledat aktualizace vyhrazeného hostitele.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Odebrat konfiguraci údržby

Chcete-li odstranit konfiguraci údržby, použijte [příkaz Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) .

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).
