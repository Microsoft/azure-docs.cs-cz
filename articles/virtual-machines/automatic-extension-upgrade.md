---
title: Automatický upgrade rozšíření pro virtuální počítače a sady škálování v Azure
description: Naučte se, jak povolit automatickou aktualizaci rozšíření pro virtuální počítače a službu Virtual Machine Scale Sets v Azure.
author: mayanknayar
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: acc014785105d14c3109cfa420f0e9402ca3f534
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417213"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Preview: automatický upgrade rozšíření pro virtuální počítače a sady škálování v Azure

Automatický upgrade rozšíření je k dispozici ve verzi Preview pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Pokud je na virtuálním počítači nebo v sadě škálování povolený automatický upgrade rozšíření, rozšíření se automaticky upgraduje, kdykoli Vydavatel rozšíření uvolní novou verzi pro toto rozšíření.

 Automatický upgrade rozšíření má následující funkce:
- Podporováno pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets nejsou aktuálně podporovány.
- Upgrady se používají v modelu nasazení s první dostupností (popsané níže).
- Při použití na Virtual Machine Scale Sets v jedné dávce nebude upgradováno více než 20% Virtual Machine Scale Sets virtuálních počítačů (za předpokladu, že minimálně jeden virtuální počítač na dávku).
- Funguje pro všechny velikosti virtuálních počítačů a pro rozšíření systému Windows i Linux.
- Automatické upgrady můžete kdykoli odhlásit.
- Automatickou aktualizaci rozšíření lze povolit na Virtual Machine Scale Sets libovolné velikosti.
- Každé podporované rozšíření je zaregistrované individuálně a můžete zvolit, která rozšíření se budou automaticky upgradovat.
- Podporováno ve všech oblastech veřejného cloudu.


> [!IMPORTANT]
> Automatický upgrade rozšíření je nyní v Public Preview. K používání funkcí verze Public Preview, které jsou popsané níže, se vyžaduje postup výslovného souhlasu.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Jak funguje upgrade automatického rozšíření?
Proces upgradu rozšíření funguje tak, že nahrazuje stávající verzi rozšíření na virtuálním počítači s novou verzí rozšíření publikovanou vydavatelem rozšíření. Stav virtuálního počítače se sleduje po instalaci nového rozšíření. Pokud virtuální počítač není v dobrém stavu do 5 minut od dokončení upgradu, nová verze rozšíření se vrátí zpět na předchozí verzi.

Neúspěšná aktualizace rozšíření se automaticky zopakuje. Opakovaný pokus o opakování probíhá každých několik dní automaticky bez zásahu uživatele.


## <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Proces upgradu pro Virtual Machine Scale Sets
1. Před zahájením procesu upgradu ověří Orchestrator, že v celé sadě škálování není v pořádku (z jakéhokoli důvodu není k dispozici více než 20% virtuálních počítačů).

2. Upgrade Orchestrator identifikuje dávku instancí virtuálních počítačů, které se mají upgradovat, přičemž každá z nich může mít maximálně 20% z celkového počtu virtuálních počítačů, a to za předpokladu, že je minimální velikost dávky jednoho virtuálního počítače.

3. Pro škálované sady s nakonfigurovanými sondami stavu aplikace nebo rozšíření stavu aplikace čeká upgrade až na 5 minut (nebo na definovanou konfiguraci sondy stavu), aby se virtuální počítač stal v dobrém stavu, než se přejde na upgrade další dávky. Pokud virtuální počítač po upgradu neobnoví svůj stav, pak se ve výchozím nastavení přeinstaluje předchozí verze rozšíření pro daný virtuální počítač.

4. Nástroj upgrade Orchestrator také sleduje procento virtuálních počítačů, které se po upgradu stanou špatnými. Upgrade se zastaví, pokud během procesu upgradu dojde k nesprávnému více než 20% upgradovaných instancí.

Výše uvedený proces pokračuje, dokud nebudou upgradovány všechny instance v sadě škálování.

Nástroj Orchestrator upgrade Orchestrator před upgradem každé dávky kontroluje celkový stav nastavené stupnice. Při upgradu dávky mohou existovat i jiné souběžné plánované nebo neplánované aktivity údržby, které by mohly ovlivnit stav virtuálních počítačů sady škálování. V takových případech, pokud se více než 20% instancí sady škálování stane špatným, se upgrade sady škálování zastaví na konci aktuální dávky.


### <a name="availability-first-updates"></a>Dostupnost – první aktualizace
Model dostupnosti-First pro aktualizace Orchestrované platformou zajistí, že Konfigurace dostupnosti v Azure se budou respektovat napříč různými úrovněmi dostupnosti.

Pro skupinu virtuálních počítačů, které projdeme aktualizaci, bude platforma Azure orchestrovat aktualizace:

**Napříč oblastmi:**
- Aktualizace se v rámci Azure v rámci celého režimu přesune globálně, aby se předešlo chybám nasazení v rámci Azure.
- "Fáze" může představovat jednu nebo více oblastí a aktualizace se přesune napříč fázemi pouze v případě, že jsou oprávněné virtuální počítače ve fázi úspěšně aktualizovány.
- Geografické spárované oblasti se neaktualizují současně a nemohou být ve stejné oblastní fázi.
- Úspěšnost aktualizace se měří sledováním stavu aktualizace po aktualizaci virtuálního počítače. Stav virtuálního počítače se sleduje prostřednictvím indikátorů stavu platformy pro virtuální počítač. V případě Virtual Machine Scale Sets je stav virtuálního počítače sledován prostřednictvím sond stavu aplikace nebo rozšíření stavu aplikace, pokud se používá pro sadu škálování.

**V rámci oblasti:**
- Virtuální počítače v různých Zóny dostupnosti se neaktualizují současně.
- Samostatné virtuální počítače, které nejsou součástí skupiny dostupnosti, se účtují na základě optimálního úsilí, aby se předešlo souběžným aktualizacím pro všechny virtuální počítače v rámci předplatného.  

**V rámci "Set":**
- Všechny virtuální počítače ve společné sadě dostupnosti nebo sadě škálování se neaktualizují současně.  
- Virtuální počítače ve společné skupině dostupnosti se aktualizují v rámci hraničních domén aktualizací a virtuální počítače napříč více aktualizačními doménami se neaktualizují současně.  
- Virtuální počítače ve společné sadě škálování virtuálních počítačů se seskupují v dávkách a aktualizovaly v rámci hranic aktualizačních domén.


## <a name="supported-extensions"></a>Podporovaná rozšíření
Verze Preview automatického upgradu rozšíření podporuje následující rozšíření (a pravidelně se přidávají):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) a [Linux](./extensions/agent-dependency-linux.md)
- [Rozšíření stavu aplikace](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows a Linux


## <a name="enabling-preview-access"></a>Povolení přístupu ve verzi Preview
Povolení funkcí verze Preview vyžaduje jednorázové přihlášení k funkci **AutomaticExtensionUpgradePreview** pro každé předplatné, jak je popsáno níže.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak povolit verzi Preview pro vaše předplatné:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
K povolení verze Preview pro vaše předplatné použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
K povolení verze Preview pro vaše předplatné použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Po registraci této funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do zprostředkovatele výpočetních prostředků.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Povoluje se upgrade automatického rozšíření.
Chcete-li povolit automatický upgrade rozšíření pro rozšíření, je nutné zajistit, aby vlastnost *enableAutomaticUpgrade* byla nastavena na *hodnotu true* a přidána do každé definice rozšíření jednotlivě.


### <a name="rest-api-for-virtual-machines"></a>REST API pro Virtual Machines
Pokud chcete povolit automatickou aktualizaci rozšíření rozšíření (v tomto příkladu Dependency Agent rozšíření) na virtuálním počítači Azure, použijte následující:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST API pro Virtual Machine Scale Sets
K přidání rozšíření do modelu sady škálování použijte následující postup:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell pro Virtual Machines
Použijte rutinu [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell pro Virtual Machine Scale Sets
Pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) přidejte rozšíření do modelu sady škálování:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Aktualizujte sadu škálování pomocí [Update-AzVmss](/powershell/module/az.compute/update-azvmss) po přidání rozšíření.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI pro Virtual Machines
Použijte rutinu [AZ VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) .

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Rozhraní příkazového řádku Azure pro Virtual Machine Scale Sets
Pomocí rutiny [AZ VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) přidejte rozšíření do modelu sady škálování:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Upgrady rozšíření s více rozšířeními

Virtuální počítač nebo sada škálování virtuálního počítače můžou mít kromě dalších rozšíření bez automatických rozšíření rozšíření povolené víc rozšíření s povoleným automatickým upgradem rozšíření.  

Je-li pro virtuální počítač k dispozici více upgradů rozšíření, mohou být tyto inovace dávkově spojeny. Každý upgrade rozšíření se ale použije samostatně na virtuálním počítači. Selhání u jednoho rozšíření nemá vliv na ostatní rozšíření, která by mohla být upgradována. Pokud je třeba pro upgrade naplánovaná dvě rozšíření a první upgrade rozšíření selže, bude se dál upgradovat druhé rozšíření.

Automatické upgrady rozšíření je možné použít i v případě, že virtuální počítač nebo sada škálování virtuálního počítače mají několik rozšíření nakonfigurovaných pomocí [sekvence rozšíření](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Sekvence rozšíření se vztahují na první nasazení virtuálního počítače a všechny následné upgrady rozšíření na rozšíření se použijí nezávisle.


## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Přečtěte si o rozšíření pro stav aplikace](./windows/automatic-vm-guest-patching.md)
