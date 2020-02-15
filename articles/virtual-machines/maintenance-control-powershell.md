---
title: Řízení údržby pro virtuální počítače Azure pomocí PowerShellu
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržba pomocí řízení údržby a PowerShellu.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 7e4586a5fba91fbc7432aa352b9608be728e8654
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251936"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Preview: řízení aktualizací pomocí řízení údržby a Azure PowerShell

Spravujte aktualizace platforem, které nevyžadují restart, pomocí řízení údržby. Azure často aktualizuje svoji infrastrukturu, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako jsou hraní her, streamování médií a finanční transakce, neumožňují tolerovat zamrznutí nebo odpojení virtuálních počítačů za účelem údržby ani pár sekund. Řízení údržby vám dává možnost počkat na aktualizace platformy a použít ji v průběhu 35ého okna. 

Řízení údržby vám umožní určit, kdy se mají pro izolované virtuální počítače použít aktualizace.

Pomocí řízení údržby můžete:
- Dávka se aktualizuje do jednoho balíčku aktualizace.
- Počkejte až 35 dní, než se aktualizace použijí. 
- Automatizujte aktualizace platforem pro okno údržby pomocí Azure Functions.
- Konfigurace údržby pracují v rámci předplatných a skupin prostředků. 

> [!IMPORTANT]
> Řízení údržby je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./linux/dedicated-hosts.md)nebo být vytvořené pomocí [izolované velikosti virtuálního počítače](./linux/isolation.md).
- Po 35 dnech se automaticky použije aktualizace.
- Uživatel musí mít přístup k **přispěvateli prostředků** .


## <a name="enable-the-powershell-module"></a>Povolit modul prostředí PowerShell

Ujistěte se, že je `PowerShellGet` aktuální.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Rutiny AZ. Maintenance PowerShell jsou ve verzi Preview, takže je potřeba nainstalovat modul s parametrem `AllowPrerelease` v Cloud Shell nebo v místní instalaci PowerShellu.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Pokud instalujete místně, ujistěte se, že jste otevřeli příkazový řádek PowerShellu jako správce.

Může se zobrazit také výzva k potvrzení, že chcete nainstalovat z *nedůvěryhodného úložiště*. Zadejte `Y` nebo vyberte **Ano pro všechny** pro instalaci modulu.



## <a name="create-a-maintenance-configuration"></a>Vytvořit konfiguraci údržby

Vytvořte skupinu prostředků jako kontejner pro vaši konfiguraci. V tomto příkladu se vytvoří skupina prostředků s názvem *myMaintenanceRG* v *eastus*. Pokud již máte skupinu prostředků, kterou chcete použít, můžete tuto část přeskočit a nahradit název skupiny prostředků vlastní ve zbývajících příkladech.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Pomocí [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) vytvořte konfiguraci údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s oborem názvů hostitele. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Pomocí `-MaintenanceScope host` zajistíte, aby se konfigurace údržby používala k řízení aktualizací hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurace musí být pro vaše předplatné jedinečné.

K dostupným konfiguracím údržby se můžete dotázat pomocí příkazu [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Pomocí [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) přiřaďte konfiguraci k vašemu IZOLOVANÉmu virtuálnímu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Použijte konfiguraci na virtuální počítač s použitím ID konfigurace. Zadejte `-ResourceType VirtualMachines` a zadejte název virtuálního počítače pro `-ResourceName`a skupinu prostředků virtuálního počítače pro `-ResourceGroupName`. 

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

Chcete-li použít konfiguraci pro vyhrazeného hostitele, je také nutné zahrnout `-ResourceType hosts`, `-ResourceParentName` s názvem skupiny hostitelů a `-ResourceParentType hostGroups`. 


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

Pokud chcete zjistit, jestli čekají na aktualizace, použijte [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) . Pomocí `-subscription` určete předplatné Azure pro virtuální počítač, pokud se liší od přihlášení, které jste přihlásili.

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

Použijte [příkaz New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) k instalaci nedokončených aktualizací.

### <a name="isolated-vm"></a>Izolovaný virtuální počítač

Vytvořte žádost o použití aktualizací pro izolovaný virtuální počítač.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Po úspěšném provedení tohoto příkazu vrátí objekt `PSApplyUpdate`. Chcete-li zjistit stav aktualizace, můžete použít atribut Name v příkazu `Get-AzApplyUpdate`. Podívejte se na téma [Zkontrolujte stav aktualizace](#check-update-status).

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
Ke kontrole stavu aktualizace použijte [příkaz Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) . Níže uvedené příkazy zobrazují stav nejnovější aktualizace pomocí `default` pro parametr `-ApplyUpdateName`. Můžete nahradit název aktualizace (vrácenou příkazem [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) ) a získat tak stav konkrétní aktualizace.

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

Chcete-li odstranit konfiguraci údržby, použijte [příkaz Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) .

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).
