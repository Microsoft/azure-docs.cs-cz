---
title: Spouštění skriptů PowerShellu na virtuálním počítači s Windows v Azure
description: Toto téma popisuje, jak spustit PowerShellové skripty na virtuálním počítači Azure s Windows pomocí příkazu run.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 0a9a5e465e160da34a21f66fd7176a8fea5d1aac
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376252"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Spuštění skriptů PowerShellu na VIRTUÁLNÍm počítači s Windows pomocí příkazu Spustit

Příkaz Spustit používá agenta virtuálního počítače ke spouštění skriptů PowerShellu v rámci virtuálního počítače Azure s Windows. Tyto skripty se dají použít k obecné správě počítačů nebo aplikací a dají se použít k rychlé diagnostice a nápravě problémů s přístupem k VIRTUÁLNÍm počítačům a k síti a získání virtuálního počítače zpátky do funkčního stavu.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Výhody

K dispozici je několik možností, které lze použít pro přístup k virtuálním počítačům. Příkaz Spustit může vzdáleně spouštět skripty na virtuálních počítačích pomocí agenta virtuálního počítače. Příkaz run lze použít prostřednictvím Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)nebo [PowerShellu](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) pro virtuální počítače s Windows.

Tato možnost je užitečná ve všech situacích, kdy chcete spustit skript v rámci virtuálních počítačů, a jedním z možných způsobů, jak řešit potíže a opravit virtuální počítač, který nemá otevřený port RDP nebo SSH z důvodu nesprávného síťového nebo administrativního uživatele. rozšířeného.

## <a name="restrictions"></a>Omezení

Při použití příkazu Run platí následující omezení:

* Výstup je omezený na posledních 4096 bajtů.
* Minimální doba spuštění skriptu je přibližně 20 sekund.
* Skripty spouštěné jako systém ve Windows
* V jednom okamžiku může běžet jeden skript.
* Skripty, které se dotazují na informace (interaktivní režim), se nepodporují.
* Běžící skript nejde zrušit.
* Maximální doba, kterou může skript běžet, je 90 minut, po jejichž uplynutí vyprší časový limit.
* K vrácení výsledků skriptu se vyžaduje odchozí připojení z virtuálního počítače.

> [!NOTE]
> Aby fungovalo správně, příkaz run vyžaduje připojení (port 443) k veřejným IP adresám Azure. Pokud rozšíření nemá přístup k těmto koncovým bodům, skripty mohou být úspěšně spuštěny, ale nebudou vracet výsledky. Pokud blokujete provoz na virtuálním počítači, můžete použít [značky služby](../../virtual-network/security-overview.md#service-tags) k povolení provozu do veřejných IP adres Azure pomocí značky `AzureCloud`.

## <a name="available-commands"></a>Dostupné příkazy

Tato tabulka obsahuje seznam příkazů, které jsou k dispozici pro virtuální počítače s Windows. Příkaz **RunPowerShellScript** se dá použít ke spuštění libovolného vlastního skriptu. Když pomocí Azure CLI nebo PowerShellu spustíte příkaz, hodnota, kterou zadáte pro parametr `--command-id` nebo `-CommandId`, musí být jednou z hodnot uvedených níže. Pokud zadáte hodnotu, která není dostupným příkazem, zobrazí se chyba.

```error
The entity was not found in this Azure location
```

|**Název**|**Popis**|
|---|---|
|**RunPowerShellScript**|Spustí PowerShellový skript.|
|**EnableRemotePS**|Nakonfiguruje počítač tak, aby povoloval vzdálené prostředí PowerShell.|
|**EnableAdminAccount**|Zkontroluje, jestli je účet místního správce zakázaný, a pokud ho povolí.|
|**Příkazu**| Zobrazuje podrobné informace o IP adrese, masce podsítě a výchozí bráně pro každý adaptér vázaný na TCP/IP.|
|**RDPSettings**|Zkontroluje nastavení registru a nastavení zásad domény. Navrhuje akce zásad, pokud je počítač součástí domény, nebo upravuje nastavení na výchozí hodnoty.|
|**ResetRDPCert**|Odebere certifikát SSL vázaný na naslouchací proces protokolu RDP a obnoví zabezpečení naslouchacího procesu RDP na výchozí hodnotu. Pokud se zobrazí nějaké problémy s certifikátem, použijte tento skript.|
|**SetRDPPort**|Nastaví výchozí nebo uživatelem zadané číslo portu pro připojení ke vzdálené ploše. Povolí pravidlo brány firewall pro příchozí přístup k portu.|

## <a name="azure-cli"></a>Azure CLI

Následuje příklad použití příkazu [AZ VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) ke spuštění skriptu prostředí na virtuálním počítači Azure Linux.

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

## <a name="azure-portal"></a>Portál Azure

Přejděte k virtuálnímu počítači v [Azure](https://portal.azure.com) a v části **operace**vyberte **Spustit příkaz** . Zobrazí se seznam dostupných příkazů, které je možné spustit na virtuálním počítači.

![Spustit seznam příkazů](./media/run-command/run-command-list.png)

Vyberte příkaz, který se má spustit. Některé příkazy mohou mít volitelné nebo vyžadované vstupní parametry. Pro tyto příkazy jsou parametry zobrazeny jako textová pole, abyste mohli zadat vstupní hodnoty. U každého příkazu můžete zobrazit skript, který se spouští rozbalením **zobrazení skriptu**. **RunPowerShellScript** se liší od ostatních příkazů, protože umožňuje zadat vlastní skript.

> [!NOTE]
> Předdefinované příkazy nelze upravovat.

Po výběru příkazu klikněte na **Spustit** a spusťte skript. Skript se spustí a po jeho dokončení vrátí výstup a všechny chyby v okně výstup. Následující snímek obrazovky ukazuje příklad výstupu spuštění příkazu **RDPSettings** .

![Spustit výstup skriptu příkazu](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Následuje příklad použití rutiny [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) ke spuštění skriptu PowerShellu na virtuálním počítači Azure. Rutina očekává, že je skript odkazovaný parametrem `-ScriptPath` místní, na kterém je rutina spouštěna.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Omezení přístupu ke spuštění příkazu

Výpis příkazů pro spuštění nebo zobrazení podrobností příkazu vyžaduje oprávnění @no__t 0 na úrovni předplatného, které mají integrovanou roli [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a vyšší.

Spuštění příkazu vyžaduje oprávnění @no__t 0 na úrovni předplatného, které má role [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) a vyšší.

Můžete použít jednu z [předdefinovaných](../../role-based-access-control/built-in-roles.md) rolí nebo vytvořit [vlastní](../../role-based-access-control/custom-roles.md) roli pro použití příkazu Spustit.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak vzdáleně spouštět skripty a příkazy ve vašem VIRTUÁLNÍm počítači, najdete v tématu [spuštění skriptů na virtuálním](run-scripts-in-vm.md) počítači s Windows.
