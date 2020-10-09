---
title: Automatické opravy hosta virtuálního počítače pro virtuální počítače s Windows v Azure
description: Přečtěte si, jak automaticky opravovat virtuální počítače s Windows v Azure.
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/09/2020
ms.author: manayar
ms.openlocfilehash: 0a777b9008864368a6d1731cae0374e55a4c585f
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91842865"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>Preview: Automatické opravy hosta virtuálního počítače pro virtuální počítače s Windows v Azure

Povolení automatické opravy hosta virtuálního počítače pro virtuální počítače s Windows pomáhá zjednodušit správu aktualizací tím, že bezpečně a automaticky opraví virtuální počítače, aby se zachovalo dodržování předpisů zabezpečení.

Automatické opravy hosta virtuálního počítače mají následující vlastnosti:
- Opravy klasifikované jako *kritické* nebo *zabezpečení* se automaticky stáhnou a aplikují na virtuálním počítači.
- Opravy se aplikují v době mimo špičku v časovém pásmu virtuálního počítače.
- Orchestrace opravy se spravuje v Azure a opravy se používají po principech dostupnosti – první.
- Stav virtuálního počítače, jak je určený prostřednictvím signálů stavu platformy, se monitoruje, aby se zjistilo selhání oprav.
- Funguje pro všechny velikosti virtuálních počítačů.

> [!IMPORTANT]
> Automatické opravy hosta virtuálního počítače jsou momentálně v Public Preview. K používání funkcí verze Public Preview, které jsou popsané níže, se vyžaduje postup výslovného souhlasu.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Jak funguje automatická oprava hosta virtuálního počítače?

Pokud je na virtuálním počítači povolené automatické opravy hosta virtuálního počítače, pak se na virtuálním počítači automaticky stáhnou a nasadí dostupné *kritické* opravy *zabezpečení* . Tento proces se automaticky vypíná v každém měsíci při vydání nových oprav prostřednictvím web Windows Update. Vyhodnocení a instalace opravy jsou automatické a proces zahrnuje restartování virtuálního počítače podle potřeby.

Tento virtuální počítač se pravidelně vyhodnocuje, aby se zjistily příslušné opravy pro daný virtuální počítač. Opravy můžete nainstalovat na virtuální počítač každý den v době mimo špičku pro virtuální počítač. Toto automatické posouzení zajišťuje, že všechny chybějící opravy budou zjištěny při nejbližší možné příležitosti.

Opravy se instalují do 30 dnů od měsíčního web Windows Update vydané verze, podle níže popsané orchestrace s první dostupností. Opravy se instalují jenom v době mimo špičku pro virtuální počítač v závislosti na časovém pásmu virtuálního počítače. Aby se aktualizace nainstalovaly automaticky, musí být virtuální počítač spuštěný v době mimo špičku. Pokud je virtuální počítač vypnutý během pravidelného posouzení, virtuální počítač se automaticky vyhodnotí a příslušné opravy se nainstalují automaticky během příštího pravidelného posuzování, když je virtuální počítač zapnutý.

Pokud chcete nainstalovat opravy s jinými klasifikacemi oprav nebo naplánovat instalaci opravy v rámci vlastního okna údržby, můžete použít [Update Management](tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Dostupnost – první Oprava

Proces instalace opravy se globálně orchestruje pomocí Azure pro všechny virtuální počítače, které mají povolenou automatickou opravu hosta virtuálního počítače. Tato orchestrace se řídí zásadami dostupnosti v rámci různých úrovní dostupnosti poskytovaných Azure.

Pro skupinu virtuálních počítačů, které projdeme aktualizaci, bude platforma Azure orchestrovat aktualizace:

**Napříč oblastmi:**
- Měsíční aktualizace se v rámci Azure v rámci několika fázích orchestruje tak, aby se předešlo selháním globálního nasazení.
- Fáze může mít jednu nebo více oblastí a aktualizace se přesune do dalších fází pouze v případě, že jsou oprávněné virtuální počítače v rámci fáze aktualizace úspěšně dokončeny.
- Geografické spárované oblasti se neaktualizují souběžně a nemůžou být ve stejné oblastní fázi.
- Úspěšnost aktualizace se měří sledováním aktualizace příspěvku stavu virtuálního počítače. Stav virtuálního počítače se sleduje prostřednictvím indikátorů stavu platformy pro virtuální počítač.

**V rámci oblasti:**
- Virtuální počítače v různých Zóny dostupnosti se neaktualizují současně.
- Virtuální počítače, které nejsou součástí skupiny dostupnosti, se účtují na základě optimálního úsilí, aby se předešlo souběžným aktualizacím pro všechny virtuální počítače v rámci předplatného.

**V rámci skupiny dostupnosti:**
- Všechny virtuální počítače ve společné skupině dostupnosti se neaktualizují současně.
-   Virtuální počítače ve společné skupině dostupnosti se aktualizují v rámci hraničních domén aktualizací a virtuální počítače napříč více aktualizačními doménami se neaktualizují současně.

Datum instalace opravy pro daný virtuální počítač se může lišit od měsíců po měsících, protože konkrétní virtuální počítač se může v rámci měsíčních cyklů oprav vybrat v jiné dávce.

## <a name="supported-os-images"></a>Podporované image operačních systémů
Ve verzi Preview se aktuálně podporují jenom virtuální počítače vytvořené z určitých imagí platformy operačního systému. Vlastní image se v současnosti ve verzi Preview nepodporují.

V současné době se podporují následující SKU platforem (a pravidelně se přidávají další):

| Publisher               | Nabídka OS      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016 – Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016 – Datacenter – Server – jádro |
| Microsoft Corporation   | WindowsServer | 2019 – Datacenter |
| Microsoft Corporation   | WindowsServer | 2019 – Datacenter – Server – jádro |

## <a name="patch-orchestration-modes"></a>Režimy orchestrace oprav
Virtuální počítače s Windows v Azure teď podporují následující režimy orchestrace oprav:

**AutomaticByPlatform:**
- Tento režim umožňuje automatickou opravu hosta virtuálního počítače pro virtuální počítač s Windows a instalaci následné opravy provádí orchestrace Azure.
- Nastavení tohoto režimu také zakáže nativní automatické aktualizace na virtuálním počítači s Windows, aby nedocházelo k duplicitám.
- Tento režim se podporuje jenom pro virtuální počítače, které jsou vytvořené pomocí podporovaných imagí platformy operačního systému výše.
- Chcete-li použít tento režim, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=true` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` v šabloně virtuálního počítače.

**AutomaticByOS:**
- Tento režim umožňuje automatické aktualizace na virtuálním počítači s Windows a opravy se na virtuálním počítači instalují prostřednictvím automatických aktualizací.
- Tento režim je nastaven ve výchozím nastavení, pokud není zadán žádný jiný režim opravy.
- Chcete-li použít tento režim, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=true` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` v šabloně virtuálního počítače.

**Zásah**
- Tento režim zakáže automatické aktualizace na virtuálním počítači s Windows.
- Tento režim by měl být nastaven při použití vlastních řešení oprav.
- Chcete-li použít tento režim, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=false` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` v šabloně virtuálního počítače.

> [!NOTE]
>Vlastnost se `osProfile.windowsConfiguration.enableAutomaticUpdates` dá v současné době nastavit jenom při prvním vytvoření virtuálního počítače. Přepínání z ručního režimu na automatický režim nebo z automatických režimů na ruční režim není aktuálně podporováno. Přepnutí z režimu AutomaticByOS do režimu AutomaticByPlatfom je podporováno.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Požadavky na povolení automatických oprav hostů pro virtuální počítače

- Virtuální počítač musí mít nainstalovaného [agenta virtuálního počítače Azure](../extensions/agent-windows.md) .
- Služba web Windows Update musí být na virtuálním počítači spuštěná.
- Virtuální počítač musí být schopný získat přístup k koncovým koncovým bodům web Windows Update. Pokud je váš virtuální počítač nakonfigurovaný tak, aby používal Windows Server Update Services (WSUS), musí být příslušné koncové body serveru WSUS dostupné.
- Použijte COMPUTE API verze 2020-06-01 nebo vyšší.

Povolení funkcí verze Preview vyžaduje jednorázové přihlášení k funkci *InGuestAutoPatchVMPreview* pro každé předplatné, jak je popsáno níže.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak povolit verzi Preview pro vaše předplatné:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
K povolení verze Preview pro vaše předplatné použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
K povolení verze Preview pro vaše předplatné použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>Povolení automatických oprav hosta virtuálního počítače
Chcete-li povolit automatické opravy hosta virtuálního počítače, zajistěte, aby vlastnost *osProfile. windowsConfiguration. enableAutomaticUpdates* byla v definici šablony virtuálního počítače nastavena na *hodnotu true* . Tuto vlastnost lze nastavit pouze při vytváření virtuálního počítače.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak povolit automatické opravy hosta virtuálního počítače:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) Povolte automatické opravy hosta virtuálního počítače při vytváření nebo aktualizaci virtuálního počítače.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [AZ VM Create](/cli/azure/vm#az-vm-create) můžete povolit automatické opravy hosta virtuálního počítače při vytváření nového virtuálního počítače. Následující příklad konfiguruje automatickou opravu hosta virtuálního počítače pro virtuální počítač s názvem *myVM* ve skupině prostředků s názvem *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Pokud chcete upravit existující virtuální počítač, použijte příkaz [AZ VM Update](/cli/azure/vm#az-vm-update) .

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Povolení a posouzení

> [!NOTE]
>Povolení automatických aktualizací hostů virtuálních počítačů na virtuálním počítači může trvat déle než tři hodiny, protože povolení je dokončené během doby mimo špičku virtuálního počítače. V případě, že dojde k instalaci posouzení a opravy pouze v době mimo špičku, musí být virtuální počítač spuštěný i v době mimo špičku, aby bylo možné použít opravy.

Když je pro virtuální počítač povolená Automatická oprava hosta virtuálního počítače, na `Microsoft.CPlat.Core.WindowsPatchExtension` virtuálním počítači se nainstaluje rozšíření virtuálního počítače typu. Toto rozšíření není nutné ručně instalovat ani aktualizovat, protože toto rozšíření je spravováno platformou Azure jako součást procesu automatických oprav hostů hosta.

Povolení automatických aktualizací hostů virtuálních počítačů na virtuálním počítači může trvat déle než tři hodiny, protože povolení je dokončené během doby mimo špičku virtuálního počítače. Rozšíření se taky nainstaluje a aktualizuje v době mimo špičku pro virtuální počítač. Pokud se doba mimo špičku virtuálního počítače ukončí před tím, než se aktivuje, proces povolení se obnoví v nejbližším dostupném čase mimo špičku. Pokud se u virtuálního počítače dřív web Windows Update v režimu opravy AutomaticByOS zapnutý, je pro virtuální počítač automaticky vypnutý automatický web Windows Update při instalaci rozšíření.

Pokud chcete ověřit, jestli se automatické opravy hosta virtuálního počítače dokončily a že je na virtuálním počítači nainstalované rozšíření pro opravy, můžete si prohlédnout zobrazení instance virtuálního počítače. Pokud je proces povolení dokončený, bude rozšíření nainstalované a výsledky posouzení pro virtuální počítač budou k dispozici v části `patchStatus` . K zobrazení instance virtuálního počítače se dá využít více způsobů, jak je popsáno níže.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Get-AzVM](/powershell/module/az.compute/get-azvm) s `-Status` parametrem získáte přístup k zobrazení instance pro váš virtuální počítač.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Prostředí PowerShell v současné době poskytuje pouze informace o příponě patch. Informace o této službě `patchStatus` budou k dispozici také brzy prostřednictvím prostředí PowerShell.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pro přístup k zobrazení instance virtuálního počítače použijte [příkaz AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) .

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Princip stavu opravy pro váš virtuální počítač

`patchStatus`Část odpovědi zobrazení instance poskytuje podrobné informace o nejnovějším posouzení a poslední instalaci opravy pro váš virtuální počítač.

Výsledky posouzení pro váš virtuální počítač je možné zkontrolovat v `availablePatchSummary` části. Pro virtuální počítač, který má povolenou automatickou opravu hosta virtuálního počítače, se hodnocení pravidelně provádí. Počet dostupných oprav po posouzení, které je uvedeno v části `criticalAndSecurityPatchCount` a `otherPatchCount` výsledky. Automatické opravy hosta virtuálního počítače budou instalovat všechny opravy vyhodnocené v rámci klasifikací *kritické* a *bezpečnostní* opravy. Všechny ostatní hodnocené opravy se přeskočí.

Výsledky instalace opravy pro váš virtuální počítač je možné zkontrolovat v `lastPatchInstallationSummary` části. V této části najdete podrobné informace o pokusu o poslední instalaci opravy na virtuálním počítači, včetně počtu nainstalovaných a neúspěšných a vynechaných oprav. Opravy se instalují jenom během období údržby pro virtuální počítač mimo špičku. Nedokončené a neúspěšné opravy se automaticky opakují během příštího časového období údržby v době mimo špičku.

## <a name="on-demand-patch-assessment"></a>Posouzení oprav na vyžádání
Pokud je pro váš virtuální počítač už povolená Automatická oprava hosta virtuálního počítače, na VIRTUÁLNÍm počítači se v době mimo špičku virtuálního počítače provede pravidelné vyhodnocení opravy. Tento proces je automatický a výsledky nejnovějšího vyhodnocení si můžete prohlédnout v zobrazení instance virtuálního počítače, jak je popsáno výše v tomto dokumentu. Pro váš virtuální počítač můžete kdykoli okamžitě aktivovat hodnocení oprav. Dokončení posouzení opravy může trvat několik minut a v zobrazení instance virtuálního počítače se aktualizuje stav nejnovějšího posouzení.

Povolení funkcí verze Preview vyžaduje jednorázové přihlášení k funkci *InGuestPatchVMPreview* na předplatné. Verze Preview funkce pro posouzení opravy na vyžádání se dá povolit po [procesu povolení verze Preview](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) popsaném dříve pro automatické opravy hosta virtuálního počítače.

> [!NOTE]
>Vyhodnocení opravy na vyžádání neaktivuje automaticky instalaci opravy. Vyhodnocené a použitelné opravy pro virtuální počítač se budou instalovat jenom v době mimo špičku virtuálního počítače, a to po procesu aktualizace, který je popsaný výše v tomto dokumentu.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) můžete vyhodnotit dostupné opravy pro váš virtuální počítač.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Pomocí [AZ VM resuzuje-patchs](/cli/azure/vm#az-vm-assess-patches) můžete vyhodnotit dostupné opravy pro váš virtuální počítač.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Další informace o vytváření a správě virtuálních počítačů s Windows](tutorial-manage-vm.md)
