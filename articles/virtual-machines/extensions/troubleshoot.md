---
title: Řešení potíží s chybami rozšíření virtuálních počítačů s Windows
description: Přečtěte si, jak řešit potíže s chybami rozšíření Azure Windows VM
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564794"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Řešení potíží s chybami rozšíření Azure Windows VM
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Zobrazuje se stav rozšíření.
Šablony Azure Resource Manager lze spouštět z Azure PowerShell. Po provedení šablony lze stav rozšíření zobrazit z Azure Resource Explorer nebo nástrojů příkazového řádku.

Tady je příklad:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Zde je ukázkový výstup:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Řešení potíží se selháním rozšíření

### <a name="rerun-the-extension-on-the-vm"></a>Znovu spustit rozšíření na virtuálním počítači
Pokud spouštíte skripty na virtuálním počítači pomocí rozšíření vlastních skriptů, někdy můžete spustit chybu, při které byl virtuální počítač úspěšně vytvořen, ale skript se nezdařil. Za těchto podmínek se doporučuje obnovit z této chyby rozšíření a znovu znovu spustit šablonu.
Poznámka: v budoucnu by se tato funkce rozšířila, aby se odstranila nutnost Odinstalace rozšíření.

#### <a name="remove-the-extension-from-azure-powershell"></a>Odebrat rozšíření z Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Po odebrání rozšíření je možné šablonu znovu spustit, aby se spouštěly skripty na virtuálním počítači.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Aktivace nového GoalState k virtuálnímu počítači
Můžete si všimnout, že rozšíření nebylo spuštěno nebo se nedaří spustit z důvodu chybějícího generátoru certifikátů služby Windows Azure CRP (Tento certifikát se používá k zabezpečení přenosu chráněných nastavení rozšíření).
Tento certifikát se automaticky znovu vygeneruje tím, že se z virtuálního počítače restartuje Agent hosta systému Windows:
- Otevřete Správce úloh.
- Přejít na kartu Podrobnosti
- Vyhledání procesu WindowsAzureGuestAgent.exe
- Klikněte pravým tlačítkem a vyberte Ukončit úlohu. Proces se automaticky restartuje.


K virtuálnímu počítači můžete také aktivovat nový GoalState spuštěním příkazu "znovu použít virtuální počítač". [Znovu použít](/rest/api/compute/virtualmachines/reapply) virtuální počítač je rozhraní API představené v 2020, které znovu použije stav virtuálního počítače. Tuto možnost doporučujeme provést v okamžiku, kdy můžete tolerovat krátké výpadky virtuálních počítačů. Při opakovaném použití samotného nezpůsobí restartování virtuálního počítače a velká většina pokusů o opakované použití nerestartuje virtuální počítač, existuje velmi malé riziko, že se použije jiná nedokončená aktualizace modelu virtuálního počítače, když se znovu použije Trigger do nového stavu cíle a tato jiná změna by mohla vyžadovat restart. 

Azure Portal:

Na portálu vyberte virtuální počítač a v levém podokně v části **Podpora a řešení potíží** vyberte znovu **nasadit + znovu použít** a pak vyberte **znovu použít**.


Azure PowerShell *(nahraďte název RG a název virtuálního počítače hodnotami)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(nahraďte název RG a název virtuálního počítače hodnotami)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Pokud se u virtuálního počítače znovu nepracuje, můžete do virtuálního počítače z Azure Portál pro správu přidat nový prázdný datový disk a později ho odebrat, až se certifikát znovu přidá.