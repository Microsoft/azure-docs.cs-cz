---
title: Spuštění skriptů PowerShellu ve virtuálním počítači s Windows v Azure
description: Toto téma popisuje, jak spustit skripty PowerShellu ve virtuálním počítači Azure Windows pomocí funkce Spustit příkaz.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749226"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Spuštění skriptů prostředí PowerShell ve vašem virtuálním počítači se systémem Windows pomocí příkazu Spustit

Funkce Spustit příkaz používá agenta virtuálního počítače (VM) ke spouštění skriptů PowerShellu v rámci virtuálního počítače Azure Windows. Tyto skripty můžete použít pro obecnou správu počítače nebo aplikací. Můžou vám pomoct rychle diagnostikovat a napravit problémy s přístupem k virtuálním montovnám a sítím a dostat virtuální ho zpět do dobrého stavu.

 

## <a name="benefits"></a>Výhody

K virtuálním počítačům můžete přistupovat několika způsoby. Příkaz Run Command můžete spouštět skripty na virtuálních počítačích vzdáleně pomocí agenta virtuálního počítače. Příkaz Spustit se používá prostřednictvím portálu Azure Portal, [rozhraní REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)nebo prostředí [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) pro virtuální počítače s Windows.

Tato funkce je užitečná ve všech scénářích, kde chcete spustit skript ve virtuálním počítači. Je to jeden z jediných způsobů, jak řešit potíže a napravit virtuální počítač, který nemá otevřený port RDP nebo SSH z důvodu nesprávné konfigurace síťového nebo administrativního uživatele.

## <a name="restrictions"></a>Omezení

Při použití příkazu Spustit platí následující omezení:

* Výstup je omezen na posledních 4 096 bajtů.
* Minimální doba spuštění skriptu je přibližně 20 sekund.
* Skripty běží jako systém v systému Windows.
* Může být spuštěn jeden skript najednou.
* Skripty, které vyzývají k zadání informací (interaktivní režim), nejsou podporovány.
* Spuštění skriptu nelze zrušit.
* Maximální doba spuštění skriptu je 90 minut. Poté, to bude časový modiše.
* Odchozí připojení z virtuálního virtuálního soudu je nutné vrátit výsledky skriptu.

> [!NOTE]
> Chcete-li správně fungovat, příkaz Run Command vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud rozšíření nemá přístup k těmto koncovým bodům, skripty může úspěšně spustit, ale nevrátí výsledky. Pokud blokujete provoz na virtuálním počítači, můžete pomocí [značek služeb](../../virtual-network/security-overview.md#service-tags) povolit provoz `AzureCloud` na veřejné IP adresy Azure pomocí značky.

## <a name="available-commands"></a>Dostupné příkazy

V této tabulce je uveden seznam příkazů dostupných pro virtuální servery windows. Příkaz **RunPowerShellScript** můžete použít ke spuštění libovolného vlastního skriptu. Pokud ke spuštění příkazu používáte rozhraní příkazu Azure CLI nebo `--command-id` PowerShell, musí být hodnota, kterou zadáte pro parametr or, `-CommandId` jednou z následujících uvedených hodnot. Zadáte-li hodnotu, která není k dispozici příkaz, zobrazí se tato chyba:

```error
The entity was not found in this Azure location
```

|**Název**|**Popis**|
|---|---|
|**RunPowerShellScript**|Spustí skript prostředí PowerShell.|
|**Povolit vzdálené ps**|Nakonfiguruje počítač tak, aby umožňoval vzdálené prostředí PowerShell.|
|**Povolit účet správce**|Zkontroluje, zda je účet místního správce zakázán, a pokud to povolí.|
|**Ipconfig**| Zobrazuje podrobné informace o adrese IP, masce podsítě a výchozí bráně pro každý adaptér vázaný na protokol TCP/IP.|
|**Nastavení protokolu RDP**|Zkontroluje nastavení registru a zásad domény. Navrhne akce zásad, pokud je počítač součástí domény nebo upraví nastavení na výchozí hodnoty.|
|**ResetRDPCert**|Odebere certifikát SSL vázaný na naslouchací proces PROTOKOLU RDP a obnoví výchozí zabezpečení naslouchací proces protokolu RDP. Tento skript použijte, pokud se zobrazí nějaké problémy s certifikátem.|
|**SetRDPPort**|Nastaví výchozí nebo uživatelem určené číslo portu pro připojení ke vzdálené ploše. Povolí pravidla brány firewall pro příchozí přístup k portu.|

## <a name="azure-cli"></a>Azure CLI

Následující příklad používá příkaz [run-command az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ke spuštění skriptu prostředí na virtuálním počítači Azure Windows.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>portál Azure

Přejděte na virtuální počítač na [webu Azure portal](https://portal.azure.com) a vyberte spustit **příkaz** v části **OPERACE**. Zobrazí se seznam dostupných příkazů, které se mají spouštět na virtuálním počítači.

![Seznam příkazů](./media/run-command/run-command-list.png)

Zvolte příkaz, který chcete spustit. Některé příkazy mohou mít volitelné nebo požadované vstupní parametry. Pro tyto příkazy jsou parametry zobrazeny jako textová pole, abyste zadali vstupní hodnoty. Pro každý příkaz můžete zobrazit skript, který je spuštěn rozbalením **zobrazit skript**. **RunPowerShellScript** se liší od ostatních příkazů, protože umožňuje poskytnout vlastní skript.

> [!NOTE]
> Předdefinované příkazy nelze upravovat.

Po výběru příkazu spusťte skript výběrem možnosti **Spustit.** Po dokončení skriptu vrátí výstup a všechny chyby ve výstupním okně. Následující snímek obrazovky ukazuje ukázkový výstup ze spuštění příkazu **RDPSettings.**

![Spuštění výstupu příkazového skriptu](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Následující příklad používá rutinu [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) ke spuštění skriptu Prostředí PowerShell na virtuálním počítači Azure. Rutina očekává, že skript odkazovaný `-ScriptPath` v parametru bude místní, kde je rutina spuštěna.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Výpis příkazů spuštění nebo zobrazení podrobností příkazu vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění na úrovni předplatného. Integrovaná role [čtečky](../../role-based-access-control/built-in-roles.md#reader) a vyšší úrovně mají toto oprávnění.

Spuštění příkazu `Microsoft.Compute/virtualMachines/runCommand/action` vyžaduje oprávnění na úrovni předplatného. Toto oprávnění má role [přispěvatele virtuálního počítače](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a vyšší úrovně.

Můžete použít jednu z [předdefinovaných rolí](../../role-based-access-control/built-in-roles.md) nebo vytvořit [vlastní roli](../../role-based-access-control/custom-roles.md) pro použití příkazu Spustit.

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech vzdáleného spouštění skriptů a příkazů ve virtuálním počítači najdete v tématu [Spouštění skriptů ve virtuálním počítači s Windows](run-scripts-in-vm.md).
