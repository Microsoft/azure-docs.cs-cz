---
title: Spouštění skriptů PowerShellu na virtuálním počítači s Windows v Azure
description: Toto téma popisuje, jak spustit PowerShellové skripty na virtuálním počítači Azure s Windows pomocí funkce spustit příkaz.
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: e2cd8ee4095db235215a2beaa68975e819b474c1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560680"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Spouštění skriptů PowerShellu na VIRTUÁLNÍm počítači s Windows pomocí příkazu Spustit

Funkce příkazu Run používá agenta virtuálního počítače ke spouštění skriptů PowerShellu v rámci virtuálního počítače Azure s Windows. Tyto skripty můžete použít k obecné správě počítačů nebo aplikací. Můžou vám pomůžou rychle diagnostikovat a opravit problémy s přístupem a sítí virtuálních počítačů a získat virtuální počítač zpátky do dobrého stavu.



## <a name="benefits"></a>Výhody

K virtuálním počítačům máte přístup několika způsoby. Příkaz Spustit může vzdáleně spouštět skripty na virtuálních počítačích pomocí agenta virtuálního počítače. Příkaz Spustit můžete použít prostřednictvím Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)nebo [PowerShellu](/powershell/module/az.compute/invoke-azvmruncommand) pro virtuální počítače s Windows.

Tato možnost je užitečná ve všech situacích, kdy chcete spustit skript na virtuálním počítači. Je to jeden z možných způsobů řešení potíží a nápravy virtuálního počítače, který nemá otevřený port RDP nebo SSH z důvodu nesprávné konfigurace sítě nebo administrativního uživatele.

## <a name="restrictions"></a>Omezení

Při použití příkazu Run platí následující omezení:

* Výstup je omezený na posledních 4 096 bajtů.
* Minimální doba spuštění skriptu je přibližně 20 sekund.
* Skripty se spouštějí jako systém ve Windows.
* V jednom okamžiku může běžet jeden skript.
* Skripty, které se dotazují na informace (interaktivní režim), se nepodporují.
* Běžící skript nemůžete zrušit.
* Maximální doba, kterou může skript běžet, je 90 minut. Po uplynutí této doby dojde k vypršení časového limitu.
* K vrácení výsledků skriptu se vyžaduje odchozí připojení z virtuálního počítače.
* Nedoporučujeme spouštět skript, který způsobí zastavení nebo aktualizaci agenta virtuálního počítače. To může mít příponu ve stavu přechodu, což vede k vypršení časového limitu.

> [!NOTE]
> Aby bylo možné správně fungovat, příkaz run vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud rozšíření nemá k těmto koncovým bodům přístup, můžou se skripty úspěšně spouštět, ale nevrátí výsledky. Pokud blokujete provoz na virtuálním počítači, můžete pomocí [značek služby](../../virtual-network/network-security-groups-overview.md#service-tags) dovolit provoz na veřejné IP adresy Azure pomocí `AzureCloud` značky.

## <a name="available-commands"></a>Dostupné příkazy

Tato tabulka obsahuje seznam příkazů, které jsou k dispozici pro virtuální počítače s Windows. Pomocí příkazu **RunPowerShellScript** můžete spustit libovolný vlastní skript, který chcete. Pokud ke spuštění příkazu použijete rozhraní příkazového řádku Azure CLI nebo PowerShell, hodnota, kterou zadáte pro `--command-id` parametr nebo, `-CommandId` musí být jedna z následujících hodnot uvedených v seznamu. Pokud zadáte hodnotu, která není dostupným příkazem, zobrazí se tato chyba:

```error
The entity was not found in this Azure location
```

|**Název**|**Popis**|
|---|---|
|**RunPowerShellScript**|Spustí skript prostředí PowerShell.|
|**EnableRemotePS**|Nakonfiguruje počítač tak, aby povoloval vzdálené prostředí PowerShell.|
|**EnableAdminAccount**|Zkontroluje, jestli je účet místního správce zakázaný, a pokud ho povolí.|
|**Příkazu**| Zobrazuje podrobné informace o IP adrese, masce podsítě a výchozí bráně pro každý adaptér vázaný na protokol TCP/IP.|
|**RDPSettings**|Zkontroluje nastavení registru a nastavení zásad domény. Navrhuje akce zásad, pokud je počítač součástí domény, nebo upravuje nastavení na výchozí hodnoty.|
|**ResetRDPCert**|Odebere certifikát TLS/SSL vázaný na naslouchací proces protokolu RDP a obnoví zabezpečení naslouchacího procesu RDP na výchozí hodnotu. Pokud se zobrazí nějaké problémy s certifikátem, použijte tento skript.|
|**SetRDPPort**|Nastaví výchozí nebo uživatelem zadané číslo portu pro připojení ke vzdálené ploše. Povolí pravidla brány firewall pro příchozí přístup k portu.|

## <a name="azure-cli"></a>Azure CLI

V následujícím příkladu se pomocí příkazu [AZ VM Run-Command](/cli/azure/vm/run-command#az-vm-run-command-invoke) spustí skript prostředí na virtuálním počítači Azure s Windows.

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

V [Azure Portal](https://portal.azure.com) klikněte na virtuální počítač a v části **operace** vyberte **Spustit příkaz** . Zobrazí se seznam dostupných příkazů ke spuštění na virtuálním počítači.

![Seznam příkazů](./media/run-command/run-command-list.png)

Vyberte příkaz, který se má spustit. Některé příkazy mohou mít volitelné nebo vyžadované vstupní parametry. Pro tyto příkazy jsou parametry uvedeny jako textová pole, abyste mohli zadat vstupní hodnoty. U každého příkazu můžete zobrazit skript, který se spouští rozbalením **zobrazení skriptu**. **RunPowerShellScript** se liší od ostatních příkazů, protože umožňuje zadat vlastní skript.

> [!NOTE]
> Předdefinované příkazy nelze upravovat.

Po výběru příkazu spusťte skript výběrem možnosti **Spustit** . Po dokončení skriptu vrátí výstup a všechny chyby v okně výstup. Následující snímek obrazovky ukazuje příklad výstupu spuštění příkazu **RDPSettings** .

![Spustit výstup skriptu příkazu](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Následující příklad používá ke spuštění skriptu PowerShellu na virtuálním počítači Azure rutinu [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) . Rutina očekává, že se skript, na který je odkazováno v `-ScriptPath` parametru, nachází v místním prostředí, kde se rutina spouští.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu k funkci Spustit příkaz

Výpis příkazů pro spuštění nebo zobrazení podrobností příkazu vyžaduje `Microsoft.Compute/locations/runCommands/read` oprávnění na úrovni předplatného. Toto oprávnění mají i předdefinovanou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a vyšší úrovně.

Spuštění příkazu vyžaduje `Microsoft.Compute/virtualMachines/runCommand/action` oprávnění. Toto oprávnění mají role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a vyšší úrovně.

Můžete použít jednu z [předdefinovaných rolí](../../role-based-access-control/built-in-roles.md) nebo vytvořit [vlastní roli](../../role-based-access-control/custom-roles.md) pro použití příkazu Spustit.

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech, jak vzdáleně spouštět skripty a příkazy na vašem VIRTUÁLNÍm počítači, najdete v tématu [spuštění skriptů na virtuálním](run-scripts-in-vm.md)počítači s Windows.
