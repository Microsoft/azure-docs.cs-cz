---
title: Automatické opravy hosta virtuálního počítače pro virtuální počítače Azure
description: Přečtěte si, jak automaticky opravovat virtuální počítače v Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679377"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Preview: Automatické opravy hosta virtuálního počítače pro virtuální počítače Azure

Povolení automatické opravy hosta virtuálního počítače pro vaše virtuální počítače Azure usnadňuje správu aktualizací tím, že bezpečně a automaticky opraví virtuální počítače, aby se zachovalo dodržování předpisů zabezpečení.

Automatické opravy hosta virtuálního počítače mají následující vlastnosti:
- Opravy klasifikované jako *kritické* nebo *zabezpečení* se automaticky stáhnou a aplikují na virtuálním počítači.
- Opravy se aplikují v době mimo špičku v časovém pásmu virtuálního počítače.
- Orchestrace opravy se spravuje v Azure a opravy se používají po [principech dostupnosti – první](#availability-first-patching).
- Stav virtuálního počítače, jak je určený prostřednictvím signálů stavu platformy, se monitoruje, aby se zjistilo selhání oprav.
- Funguje pro všechny velikosti virtuálních počítačů.

> [!IMPORTANT]
> Automatické opravy hosta virtuálního počítače jsou momentálně v Public Preview. K používání funkcí verze Public Preview, které jsou popsané níže, se vyžaduje postup výslovného souhlasu.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Jak funguje automatická oprava hosta virtuálního počítače?

Pokud je na virtuálním počítači povolené automatické opravy hosta virtuálního počítače, pak se na virtuálním počítači automaticky stáhnou a nasadí dostupné *kritické* opravy *zabezpečení* . Tento proces se při vydání nových oprav automaticky odstartuje každý měsíc. Vyhodnocení a instalace opravy jsou automatické a proces zahrnuje restartování virtuálního počítače podle potřeby.

Tento virtuální počítač se pravidelně vyhodnocuje každých několik dní a v rámci všech 30 dnů několikrát určí příslušné opravy pro daný virtuální počítač. Opravy můžete nainstalovat na virtuální počítač každý den v době mimo špičku pro virtuální počítač. Toto automatické posouzení zajišťuje, že všechny chybějící opravy budou zjištěny při nejbližší možné příležitosti.

Opravy se instalují do 30 dnů od měsíčních verzí oprav, které jsou popsané níže. Opravy se instalují jenom v době mimo špičku pro virtuální počítač v závislosti na časovém pásmu virtuálního počítače. Aby se aktualizace nainstalovaly automaticky, musí být virtuální počítač spuštěný v době mimo špičku. Pokud je virtuální počítač vypnutý během pravidelného posouzení, virtuální počítač se automaticky vyhodnotí a příslušné opravy se nainstalují automaticky během následujícího pravidelného vyhodnocení (obvykle během několika dní), kdy se virtuální počítač zapne.

Aktualizace definic a jiné opravy nejsou klasifikované  jako kritické *nebo se* neinstalují pomocí automatických oprav typu hosta virtuálního počítače. Pokud chcete nainstalovat opravy s jinými klasifikacemi oprav nebo naplánovat instalaci opravy v rámci vlastního okna údržby, můžete použít [Update Management](./windows/tutorial-config-management.md#manage-windows-updates).

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

### <a name="which-patches-are-installed"></a>Které opravy jsou nainstalovány?
Nainstalované opravy závisejí na fázi zavedení virtuálního počítače. Každý měsíc se spustí nové globální zavedení, kde se pro tento virtuální počítač nainstalují všechny aktualizace zabezpečení a důležité opravy pro jednotlivé virtuální počítače. Zavedení se orchestruje napříč všemi oblastmi Azure v dávkách (popsané v části dostupnost – první oprava).

Přesná sada oprav, které se mají nainstalovat, se liší v závislosti na konfiguraci virtuálního počítače, včetně typu operačního systému a časování posouzení. Je možné, že dva identické virtuální počítače v různých oblastech budou mít nainstalovány různé opravy, pokud je k dispozici více nebo méně dostupných oprav, když orchestrace opravy dosáhne různých oblastí v různou dobu. Podobně, ale méně často, virtuální počítače ve stejné oblasti, které se posuzují v různou dobu (v důsledku různých zón dostupnosti nebo dávek sady dostupnosti), můžou mít různé opravy.

Vzhledem k tomu, že automatické opravy hosta virtuálního počítače nekonfigurují zdroj oprav, můžou se v přesné sadě nainstalovaných oprav zobrazit taky rozdíl mezi dvěma podobnými virtuálními počítači nakonfigurovanými na různé zdroje oprav, jako je třeba veřejné úložiště vs Private cloudové úložiště.

U typů operačních systémů, které vydávají opravy na pevném tempo, můžou virtuální počítače nakonfigurované do veřejného úložiště pro operační systém očekávat stejnou sadu oprav v rámci různých fází zavedení za měsíc. Například virtuální počítače s Windows nakonfigurované na úložiště pro veřejné web Windows Update.

Nové zavedení se spouští každý měsíc, a pokud je virtuální počítač napájený v době mimo špičku, bude každý měsíc přijímat aspoň jedno zavedení opravy. Tím se zajistí, že se virtuální počítač opraví s nejnovějšími dostupnými aktualizacemi zabezpečení a kritických oprav na měsíc. Aby se zajistila konzistence v sadě nainstalovaných oprav, můžete nakonfigurovat své virtuální počítače tak, aby vyhodnotily a stáhly opravy z vlastních privátních úložišť.

## <a name="supported-os-images"></a>Podporované image operačních systémů
Ve verzi Preview se aktuálně podporují jenom virtuální počítače vytvořené z určitých imagí platformy operačního systému. Vlastní image se v současnosti ve verzi Preview nepodporují.

V současné době se podporují následující SKU platforem (a pravidelně se přidávají další):

| Publisher               | Nabídka OS      |  Skladová jednotka (SKU)               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18,04 – LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016 – Datacenter – Server – jádro |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019 – Datacenter – jádro |

## <a name="patch-orchestration-modes"></a>Režimy orchestrace oprav
Virtuální počítače v Azure teď podporují následující režimy orchestrace oprav:

**AutomaticByPlatform:**
- Tento režim je podporován pro virtuální počítače se systémy Linux a Windows.
- Tento režim umožňuje automatickou opravu hosta virtuálního počítače pro virtuální počítač a instalaci následné opravy provádí orchestrace Azure.
- Tento režim se vyžaduje pro dostupnost – první oprava.
- Tento režim se podporuje jenom pro virtuální počítače, které jsou vytvořené pomocí podporovaných imagí platformy operačního systému výše.
- U virtuálních počítačů s Windows nastavení tento režim také zakáže nativní automatické aktualizace na virtuálním počítači s Windows, aby nedocházelo k duplicitám.
- Pokud chcete tento režim použít pro virtuální počítače se systémem Linux, nastavte vlastnost `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` v šabloně virtuálního počítače.
- Pokud chcete tento režim použít pro virtuální počítače s Windows, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=true` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` v šabloně virtuálního počítače.

**AutomaticByOS:**
- Tento režim je podporován pouze pro virtuální počítače s Windows.
- Tento režim umožňuje automatické aktualizace na virtuálním počítači s Windows a opravy se na virtuálním počítači instalují prostřednictvím automatických aktualizací.
- Tento režim nepodporuje dostupnost – první oprava.
- Tento režim je nastaven ve výchozím nastavení, pokud pro virtuální počítač s Windows není zadán žádný jiný režim opravy.
- Pokud chcete tento režim použít pro virtuální počítače s Windows, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=true` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` v šabloně virtuálního počítače.

**Zásah**
- Tento režim je podporován pouze pro virtuální počítače s Windows.
- Tento režim zakáže automatické aktualizace na virtuálním počítači s Windows.
- Tento režim nepodporuje dostupnost – první oprava.
- Tento režim by měl být nastaven při použití vlastních řešení oprav.
- Pokud chcete tento režim použít pro virtuální počítače s Windows, nastavte vlastnost `osProfile.windowsConfiguration.enableAutomaticUpdates=false` a nastavte vlastnost  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` v šabloně virtuálního počítače.

**ImageDefault:**
- Tento režim je podporován pouze pro virtuální počítače se systémem Linux.
- Tento režim nepodporuje dostupnost – první oprava.
- Tento režim dodržuje výchozí konfiguraci oprav v imagi použitou k vytvoření virtuálního počítače.
- Tento režim je nastaven ve výchozím nastavení, pokud pro virtuální počítač se systémem Linux není zadán žádný jiný režim opravy.
- Pokud chcete tento režim použít pro virtuální počítače se systémem Linux, nastavte vlastnost `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` v šabloně virtuálního počítače.

> [!NOTE]
>Pro virtuální počítače s Windows se `osProfile.windowsConfiguration.enableAutomaticUpdates` dá vlastnost aktuálně nastavit jenom v případě, že je virtuální počítač vytvořený poprvé. Přepínání z ručního režimu na automatický režim nebo z automatických režimů na ruční režim není aktuálně podporováno. Přepnutí z režimu AutomaticByOS do režimu AutomaticByPlatfom je podporováno.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Požadavky na povolení automatických oprav hostů pro virtuální počítače

- Virtuální počítač musí mít nainstalovaného agenta virtuálního počítače Azure pro [systém Windows](./extensions/agent-windows.md) nebo [Linux](./extensions/agent-linux.md) .
- U virtuálních počítačů se systémem Linux musí být agent Azure Linux verze 2.2.53.1 nebo vyšší. Pokud je aktuální verze nižší než požadovaná verze, [aktualizujte agenta pro Linux](./extensions/update-linux-agent.md) .
- Pro virtuální počítače s Windows musí být na virtuálním počítači spuštěná služba web Windows Update.
- Virtuální počítač musí být schopný získat přístup k nakonfigurovaným koncovým bodům aktualizace. Pokud je váš virtuální počítač nakonfigurovaný tak, aby pro virtuální počítače s Windows používal privátní úložiště pro Linux nebo Windows Server Update Services (WSUS), musí být dostupné příslušné koncové body aktualizace.
- Použijte COMPUTE API verze 2020-12-01 nebo vyšší. Výpočetní rozhraní API verze 2020-06-01 se dá použít pro virtuální počítače s Windows s omezenými funkcemi.

Povolení funkcí verze Preview vyžaduje jednorázové přihlášení k funkcím **InGuestAutoPatchVMPreview** a **InGuestPatchVMPreview** na předplatné, jak je popsáno v následující části.

### <a name="rest-api"></a>REST API
Následující příklad popisuje, jak povolit verzi Preview pro vaše předplatné:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
K povolení verze Preview pro vaše předplatné použijte rutinu [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
K povolení verze Preview pro vaše předplatné použijte [AZ Feature Registry](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Registrace funkce může trvat až 15 minut. Postup kontroly stavu registrace:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Po registraci funkce pro vaše předplatné dokončete proces výslovných přihlášení tím, že tuto změnu rozšíříte do poskytovatele výpočetních prostředků.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Povolení automatických oprav hosta virtuálního počítače
Pokud chcete povolit automatické opravy hosta virtuálního počítače na virtuálním počítači s Windows, zajistěte, aby byla vlastnost *osProfile. windowsConfiguration. enableAutomaticUpdates* v definici šablony virtuálního počítače nastavená na *true* . Tuto vlastnost lze nastavit pouze při vytváření virtuálního počítače. Tuto dodatečnou vlastnost nelze použít pro virtuální počítače se systémem Linux.

### <a name="rest-api-for-linux-vms"></a>REST API pro virtuální počítače se systémem Linux
Následující příklad popisuje, jak povolit automatické opravy hosta virtuálního počítače:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>REST API pro virtuální počítače s Windows
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

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell pro virtuální počítače s Windows
Pomocí rutiny [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) Povolte automatické opravy hosta virtuálního počítače při vytváření nebo aktualizaci virtuálního počítače.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Azure CLI pro virtuální počítače s Windows
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

Pokud je pro virtuální počítač povolená Automatická oprava hosta virtuálního počítače, je na virtuálním počítači se systémem Linux nainstalovaná přípona virtuálního počítače typu a na virtuálním `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` počítači s Windows se nainstaluje rozšíření virtuálního počítače typu. Toto rozšíření není nutné ručně instalovat ani aktualizovat, protože toto rozšíření je spravováno platformou Azure jako součást procesu automatických oprav hostů hosta.

Povolení automatických aktualizací hostů virtuálních počítačů na virtuálním počítači může trvat déle než tři hodiny, protože povolení je dokončené během doby mimo špičku virtuálního počítače. Rozšíření se taky nainstaluje a aktualizuje v době mimo špičku pro virtuální počítač. Pokud se doba mimo špičku virtuálního počítače ukončí před tím, než se aktivuje, proces povolení se obnoví v nejbližším dostupném čase mimo špičku.

Automatické aktualizace jsou ve většině scénářů zakázané a instalace opravy se provádí prostřednictvím rozšíření, které se chystá dál. Platí následující podmínky.
- Pokud se u virtuálního počítače s Windows dřív web Windows Update v režimu opravy AutomaticByOS zapnutý, je pro virtuální počítač automaticky vypnutý automatický web Windows Update, když je rozšíření nainstalované.
- Pro virtuální počítače s Ubuntu jsou výchozí automatické aktualizace automaticky zakázané, když se automatické opravy hosta virtuálního počítače dokončí.
- Pro RHEL je potřeba automatické aktualizace zakázat ručně (Toto je omezení verze Preview). Spustit

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Pokud chcete ověřit, jestli se automatické opravy hosta virtuálního počítače dokončily a že je na virtuálním počítači nainstalované rozšíření pro opravy, můžete si prohlédnout zobrazení instance virtuálního počítače. Pokud je proces povolení dokončený, bude rozšíření nainstalované a výsledky posouzení pro virtuální počítač budou k dispozici v části `patchStatus` . K zobrazení instance virtuálního počítače se dá využít více způsobů, jak je popsáno níže.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Get-AzVM](/powershell/module/az.compute/get-azvm) s `-Status` parametrem získáte přístup k zobrazení instance pro váš virtuální počítač.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

Prostředí PowerShell v současné době poskytuje pouze informace o příponě patch. Informace o této službě `patchStatus` budou k dispozici také brzy prostřednictvím prostředí PowerShell.

### <a name="azure-cli"></a>Azure CLI
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

Povolení funkcí verze Preview vyžaduje jednorázové přihlášení k funkci **InGuestPatchVMPreview** na předplatné. Tato funkce je ve verzi Preview odlišná od registrace funkce Automatické aktualizace hosta virtuálního počítače, kterou jste provedli dříve pro **InGuestAutoPatchVMPreview**. Povolení další funkce Preview je samostatný a další požadavek. Verze Preview funkce pro posouzení opravy na vyžádání se dá povolit po [procesu povolení verze Preview](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) popsaném dříve pro automatické opravy hosta virtuálního počítače.

> [!NOTE]
>Vyhodnocení opravy na vyžádání neaktivuje automaticky instalaci opravy. Pokud jste povolili automatické opravy hosta virtuálního počítače, pak se vyhodnocené a použitelné opravy pro virtuální počítač budou instalovat během doby mimo špičku virtuálního počítače, a to po prvním procesu opravy dostupnosti, který je popsaný výše v tomto dokumentu.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Pomocí rutiny [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) můžete vyhodnotit dostupné opravy pro váš virtuální počítač.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
Pomocí [AZ VM resuzuje-patchs](/cli/azure/vm#az-vm-assess-patches) můžete vyhodnotit dostupné opravy pro váš virtuální počítač.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Další informace o vytváření a správě virtuálních počítačů s Windows](./windows/tutorial-manage-vm.md)
