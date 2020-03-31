---
title: Řízení údržby pro virtuální počítače Azure pomocí PowerShellu
description: Zjistěte, jak řídit, když se údržba použije na vaše virtuální počítače Azure pomocí řízení údržby a PowerShellu.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060141"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Náhled: Řízení aktualizací pomocí řízení údržby a Azure PowerShellu

Spravujte aktualizace platformy, které nevyžadují restartování pomocí řízení údržby. Azure často aktualizuje svou infrastrukturu, aby zlepšil spolehlivost, výkon, zabezpečení nebo spustil nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako je hraní her, streamování médií a finanční transakce, nemohou tolerovat ani několik sekund zamrznutí nebo odpojení virtuálního počítače z důvodu údržby. Řízení údržby vám dává možnost čekat na aktualizace platformy a aplikovat je v rámci 35denního rolovacího okna. 

Řízení údržby umožňuje rozhodnout, kdy použít aktualizace na izolované virtuální počítače.

S kontrolou údržby můžete:
- Dávkové aktualizace do jednoho balíčku aktualizace.
- Vyčkejte až 35 dní, než chcete nainstalovat aktualizace. 
- Automatizujte aktualizace platformy pro okno údržby pomocí Funkce Azure.
- Konfigurace údržby fungují napříč předplatnými a skupinami prostředků. 

> [!IMPORTANT]
> Řízení údržby je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./linux/dedicated-hosts.md)nebo být vytvořeny pomocí [izolované velikosti virtuálního počítače](./linux/isolation.md).
- Po 35 dnech bude automaticky použita aktualizace.
- Uživatel musí mít přístup **přispěvatele prostředků.**


## <a name="enable-the-powershell-module"></a>Povolení modulu PowerShellu

Ujistěte `PowerShellGet` se, že je aktuální.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Rutiny Prostředí Az.Maintenance PowerShell jsou ve verzi preview, takže `AllowPrerelease` je třeba nainstalovat modul s parametrem v prostředí Cloud Shell nebo místní instalaci prostředí PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Pokud instalujete místně, ujistěte se, že otevřete výzvu prostředí PowerShell jako správce.

Můžete být také požádáni o potvrzení, že chcete nainstalovat z *nedůvěryhodného úložiště*. Zadejte `Y` nebo vyberte **možnost Ano všem,** chcete-li modul nainstalovat.



## <a name="create-a-maintenance-configuration"></a>Vytvoření konfigurace údržby

Vytvořte skupinu prostředků jako kontejner pro vaši konfiguraci. V tomto příkladu je vytvořena skupina prostředků s názvem *myMaintenanceRG* v *eastus*. Pokud již máte skupinu prostředků, kterou chcete použít, můžete tuto část přeskočit a nahradit název skupiny prostředků vlastním ve zbývajících příkladech.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Pomocí [funkce New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) vytvořte konfiguraci údržby. Tento příklad vytvoří konfiguraci údržby s názvem *myConfig* s rozsahem hostitele. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Použití `-MaintenanceScope host` zajišťuje, že konfigurace údržby se používá pro řízení aktualizací hostitele.

Pokud se pokusíte vytvořit konfiguraci se stejným názvem, ale v jiném umístění, zobrazí se chyba. Názvy konfigurací musí být jedinečné pro vaše předplatné.

Můžete dotaz na dostupné konfigurace údržby pomocí [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Přiřazení konfigurace

Pomocí [new-azconfigurationassignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) přiřadit konfiguraci k izolovanému virtuálnímu počítači nebo vyhrazenému hostiteli Azure.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Použijte konfiguraci na virtuální ms pomocí ID konfigurace. Zadejte `-ResourceType VirtualMachines` a zadejte název `-ResourceName`virtuálního_ virtuálního_ a. `-ResourceGroupName` 

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

Chcete-li použít konfiguraci na vyhrazeného `-ResourceType hosts`hostitele, musíte také zahrnout `-ResourceParentName` , `-ResourceParentType hostGroups`s názvem skupiny hostitelů a . 


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

## <a name="check-for-pending-updates"></a>Vyhledat čekající aktualizace

Pomocí [funkce Get-AzMaintenanceUpdate zjistěte,](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) zda existují čekající aktualizace. Slouží `-subscription` k určení předplatného Azure virtuálního počítače, pokud se liší od toho, ke kterému jste přihlášeni.

Pokud nejsou k dispozici žádné aktualizace zobrazit, tento příkaz vrátí nic. V opačném případě vrátí objekt PSApplyUpdate:

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

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Zkontrolujte čekající aktualizace pro izolovaný virtuální ms. V tomto příkladu je výstup formátován jako tabulka pro čitelnost.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Vyhrazený hostitel

Chcete-li vyhledat čekající aktualizace pro vyhrazeného hostitele. V tomto příkladu je výstup formátován jako tabulka pro čitelnost. Nahraďte hodnoty prostředků vlastními.

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

Použijte [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) použít čekající aktualizace.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Vytvořte požadavek na použití aktualizací na izolovaný virtuální ms.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Při úspěchu tento příkaz `PSApplyUpdate` vrátí objekt. Pomocí atributu Name v `Get-AzApplyUpdate` příkazu můžete zkontrolovat stav aktualizace. Viz [Kontrola stavu aktualizace](#check-update-status).

### <a name="dedicated-host"></a>Vyhrazený hostitel

Použijte aktualizace vyhrazeného hostitele.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Zkontrolovat stav aktualizace
Pomocí [funkce Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) zkontrolujte stav aktualizace. Níže uvedené příkazy zobrazují stav nejnovější aktualizace `default` pomocí `-ApplyUpdateName` parametru. Můžete nahradit název aktualizace (vrácené příkazem [New-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) získat stav konkrétní aktualizace.

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
LastUpdateTime bude čas, kdy aktualizace byla dokončena, buď iniciované vámi, nebo platformou v případě, že okno samoúdržby nebylo použito. Pokud nikdy nebyla provedena aktualizace prostřednictvím řízení údržby, zobrazí se výchozí hodnota.

### <a name="isolated-vm"></a>Izolovaný virtuální virtuální ms

Zkontrolujte aktualizace konkrétního virtuálního počítače.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Vyhrazený hostitel

Zkontrolujte aktualizace vyhrazeného hostitele.

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

## <a name="remove-a-maintenance-configuration"></a>Odebrání konfigurace údržby

Pomocí [funkce Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) odstraňte konfiguraci údržby.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Další kroky
Další informace najdete v [tématu Údržba a aktualizace](maintenance-and-updates.md).
