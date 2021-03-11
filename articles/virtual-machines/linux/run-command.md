---
title: Spouštění skriptů prostředí v rámci virtuálního počítače se systémem Linux v Azure
description: Toto téma popisuje, jak spouštět skripty v rámci virtuálního počítače Azure Linux pomocí funkce spustit příkaz.
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 73dd15a5eed3e27d9b72bc0357e35901c04ba7a2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552928"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Spouštění skriptů prostředí ve VIRTUÁLNÍm počítači se systémem Linux pomocí příkazu Spustit

Funkce Spustit příkaz využívá agenta virtuálního počítače ke spouštění skriptů prostředí v rámci virtuálního počítače Azure s Linuxem. Tyto skripty můžete použít k obecné správě počítačů nebo aplikací. Můžou vám pomůžou rychle diagnostikovat a opravit problémy s přístupem a sítí virtuálních počítačů a získat virtuální počítač zpátky do dobrého stavu.

## <a name="benefits"></a>Výhody

K virtuálním počítačům máte přístup několika způsoby. Příkaz Spustit může vzdáleně spouštět skripty na virtuálních počítačích pomocí agenta virtuálního počítače. Příkaz Spustit můžete použít prostřednictvím Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)nebo [Azure CLI](/cli/azure/vm/run-command#az_vm_run_command_invoke) pro virtuální počítače se systémem Linux.

Tato možnost je užitečná ve všech situacích, kdy chcete spustit skript na virtuálním počítači. Je to jeden z možných způsobů řešení potíží a nápravy virtuálního počítače, který nemá otevřený port RDP nebo SSH z důvodu nesprávné konfigurace sítě nebo administrativního uživatele.

## <a name="restrictions"></a>Omezení

Při použití příkazu Run platí následující omezení:

* Výstup je omezený na posledních 4 096 bajtů.
* Minimální doba spuštění skriptu je přibližně 20 sekund.
* Skripty spouštěné ve výchozím nastavení jako uživatel se zvýšenými oprávněními v systému Linux.
* Můžete spustit vždy jeden skript.
* Skripty, které se dotazují na informace (interaktivní režim), se nepodporují.
* Běžící skript nemůžete zrušit.
* Maximální doba, kterou může skript běžet, je 90 minut. Po dokončení bude skript vyprší.
* K vrácení výsledků skriptu se vyžaduje odchozí připojení z virtuálního počítače.

> [!NOTE]
> Aby bylo možné správně fungovat, příkaz run vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud rozšíření nemá k těmto koncovým bodům přístup, můžou se skripty úspěšně spouštět, ale nevrátí výsledky. Pokud blokujete provoz na virtuálním počítači, můžete pomocí [značek služby](../../virtual-network/network-security-groups-overview.md#service-tags) dovolit provoz na veřejné IP adresy Azure pomocí `AzureCloud` značky.

## <a name="available-commands"></a>Dostupné příkazy

Tato tabulka obsahuje seznam příkazů, které jsou k dispozici pro virtuální počítače se systémem Linux. Pomocí příkazu **RunShellScript** můžete spustit libovolný vlastní skript, který chcete. Pokud ke spuštění příkazu použijete rozhraní příkazového řádku Azure CLI nebo PowerShell, hodnota, kterou zadáte pro `--command-id` parametr nebo, `-CommandId` musí být jedna z následujících hodnot uvedených v seznamu. Pokud zadáte hodnotu, která není dostupným příkazem, zobrazí se tato chyba:

```error
The entity was not found in this Azure location
```

|**Název**|**Popis**|
|---|---|
|**RunShellScript**|Spustí skript prostředí systému Linux.|
|**ifconfig**| Získá konfiguraci všech síťových rozhraní.|

## <a name="azure-cli"></a>Azure CLI

V následujícím příkladu se pomocí příkazu [AZ VM Run-Command](/cli/azure/vm/run-command#az_vm_run_command_invoke) spustí skript prostředí na virtuálním počítači Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Chcete-li spustit příkazy jako jiný uživatel, zadejte `sudo -u` uživatelský účet.

## <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com) klikněte na virtuální počítač a v části **operace** vyberte **Spustit příkaz** . Zobrazí se seznam dostupných příkazů ke spuštění na virtuálním počítači.

![Seznam příkazů](./media/run-command/run-command-list.png)

Vyberte příkaz, který se má spustit. Některé příkazy mohou mít volitelné nebo vyžadované vstupní parametry. Pro tyto příkazy jsou parametry uvedeny jako textová pole, abyste mohli zadat vstupní hodnoty. U každého příkazu můžete zobrazit skript, který se spouští rozbalením **zobrazení skriptu**. **RunShellScript** se liší od ostatních příkazů, protože umožňuje zadat vlastní skript.

> [!NOTE]
> Předdefinované příkazy nelze upravovat.

Po výběru příkazu spusťte skript výběrem možnosti **Spustit** . Po dokončení skriptu vrátí výstup a všechny chyby v okně výstup. Následující snímek obrazovky ukazuje příklad výstupu spuštění příkazu **ifconfig** .

![Spustit výstup skriptu příkazu](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Následující příklad používá ke spuštění skriptu PowerShellu na virtuálním počítači Azure rutinu [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) . Rutina očekává, že se skript, na který je odkazováno v `-ScriptPath` parametru, nachází v místním prostředí, kde se rutina spouští.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu k funkci Spustit příkaz

Výpis příkazů pro spuštění nebo zobrazení podrobností příkazu vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění. Toto oprávnění mají i předdefinovanou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a vyšší úrovně.

Spuštění příkazu vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění. Toto oprávnění mají role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a vyšší úrovně.

Můžete použít jednu z [předdefinovaných rolí](../../role-based-access-control/built-in-roles.md) nebo vytvořit [vlastní roli](../../role-based-access-control/custom-roles.md) pro použití příkazu Spustit.

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech, jak vzdáleně spouštět skripty a příkazy na vašem VIRTUÁLNÍm počítači, najdete v tématu [spuštění skriptů na svém virtuálním počítači se systémem Linux](run-scripts-in-vm.md).
