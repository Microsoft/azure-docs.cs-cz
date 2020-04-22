---
title: Spouštění skriptů prostředí v virtuálním počítači SIO v Azure
description: Toto téma popisuje, jak spouštět skripty ve virtuálním počítači Azure Linux pomocí funkce Spustit příkaz
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758600"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Spouštění skriptů prostředí ve vašem virtuálním počítači s Linuxem pomocí příkazu Spustit

Funkce Spustit příkaz používá agenta virtuálního počítače (VM) ke spuštění skriptů prostředí v rámci virtuálního počítače Azure Linux. Tyto skripty můžete použít pro obecnou správu počítače nebo aplikací. Můžou vám pomoct rychle diagnostikovat a napravit problémy s přístupem k virtuálním montovnám a sítím a dostat virtuální ho zpět do dobrého stavu.

## <a name="benefits"></a>Výhody

K virtuálním počítačům můžete přistupovat několika způsoby. Příkaz Run Command můžete spouštět skripty na virtuálních počítačích vzdáleně pomocí agenta virtuálního počítače. Příkaz Spustit se používá prostřednictvím portálu Azure Portal, [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)nebo azure [cli](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) pro virtuální počítače s Linuxem.

Tato funkce je užitečná ve všech scénářích, kde chcete spustit skript ve virtuálním počítači. Je to jeden z jediných způsobů, jak řešit potíže a napravit virtuální počítač, který nemá otevřený port RDP nebo SSH z důvodu nesprávné konfigurace síťového nebo administrativního uživatele.

## <a name="restrictions"></a>Omezení

Při použití příkazu Spustit platí následující omezení:

* Výstup je omezen na posledních 4 096 bajtů.
* Minimální doba spuštění skriptu je přibližně 20 sekund.
* Skripty běží ve výchozím nastavení jako zvýšený uživatel na Linuxu.
* Můžete spustit jeden skript najednou.
* Skripty, které vyzývají k zadání informací (interaktivní režim), nejsou podporovány.
* Spuštění skriptu nelze zrušit.
* Maximální doba spuštění skriptu je 90 minut. Poté, skript bude časový modiše.
* Odchozí připojení z virtuálního virtuálního soudu je nutné vrátit výsledky skriptu.

> [!NOTE]
> Chcete-li správně fungovat, příkaz Run Command vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud rozšíření nemá přístup k těmto koncovým bodům, skripty může úspěšně spustit, ale nevrátí výsledky. Pokud blokujete provoz na virtuálním počítači, můžete pomocí [značek služeb](../../virtual-network/security-overview.md#service-tags) povolit provoz `AzureCloud` na veřejné IP adresy Azure pomocí značky.

## <a name="available-commands"></a>Dostupné příkazy

V této tabulce je uveden seznam příkazů dostupných pro virtuální počítače s Linuxem. Příkaz **RunShellScript** můžete použít ke spuštění libovolného vlastního skriptu. Pokud ke spuštění příkazu používáte rozhraní příkazu Azure CLI nebo `--command-id` PowerShell, musí být hodnota, kterou zadáte pro parametr or, `-CommandId` jednou z následujících uvedených hodnot. Zadáte-li hodnotu, která není k dispozici příkaz, zobrazí se tato chyba:

```error
The entity was not found in this Azure location
```

|**Název**|**Popis**|
|---|---|
|**RunShellScript**|Spustí skript prostředí Linux.|
|**Ifconfig**| Získá konfiguraci všech síťových rozhraní.|

## <a name="azure-cli"></a>Azure CLI

Následující příklad používá příkaz [run-command az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ke spuštění skriptu prostředí na virtuálním počítači Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Chcete-li spouštět příkazy `sudo -u` jako jiný uživatel, zadejte zadání uživatelského účtu.

## <a name="azure-portal"></a>portál Azure

Přejděte na virtuální počítač na [webu Azure portal](https://portal.azure.com) a vyberte spustit **příkaz** v části **OPERACE**. Zobrazí se seznam dostupných příkazů, které se mají spouštět na virtuálním počítači.

![Seznam příkazů](./media/run-command/run-command-list.png)

Zvolte příkaz, který chcete spustit. Některé příkazy mohou mít volitelné nebo požadované vstupní parametry. Pro tyto příkazy jsou parametry zobrazeny jako textová pole, abyste zadali vstupní hodnoty. Pro každý příkaz můžete zobrazit skript, který je spuštěn rozbalením **zobrazit skript**. **RunShellScript** se liší od ostatních příkazů, protože umožňuje zadat vlastní skript.

> [!NOTE]
> Předdefinované příkazy nelze upravovat.

Po výběru příkazu spusťte skript výběrem možnosti **Spustit.** Po dokončení skriptu vrátí výstup a všechny chyby ve výstupním okně. Následující snímek obrazovky ukazuje ukázkový výstup ze spuštění příkazu **ifconfig.**

![Spuštění výstupu příkazového skriptu](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Následující příklad používá rutinu [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) ke spuštění skriptu Prostředí PowerShell na virtuálním počítači Azure. Rutina očekává, že skript odkazovaný `-ScriptPath` v parametru bude místní, kde je rutina spuštěna.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Výpis příkazů spuštění nebo zobrazení podrobností příkazu vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění na úrovni předplatného. Integrovaná role [čtečky](../../role-based-access-control/built-in-roles.md#reader) a vyšší úrovně mají toto oprávnění.

Spuštění příkazu `Microsoft.Compute/virtualMachines/runCommand/action` vyžaduje oprávnění na úrovni předplatného. Toto oprávnění má role [přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a vyšší úrovně.

Můžete použít jednu z [předdefinovaných rolí](../../role-based-access-control/built-in-roles.md) nebo vytvořit [vlastní roli](../../role-based-access-control/custom-roles.md) pro použití příkazu Spustit.

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech vzdáleného spouštění skriptů a příkazů ve virtuálním počítači najdete v tématu [Spouštění skriptů ve vašem virtuálním počítači s Linuxem](run-scripts-in-vm.md).
