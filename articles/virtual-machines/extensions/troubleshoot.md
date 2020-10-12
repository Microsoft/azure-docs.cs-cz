---
title: Řešení potíží s chybami rozšíření virtuálních počítačů s Windows
description: Přečtěte si, jak řešit potíže s chybami rozšíření Azure Windows VM
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: ad3197f20428ec751b4e3520af72dc5f8eb9ad28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89180351"
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
Tento certifikát se automaticky znovu vygeneruje restartováním agenta hosta systému Windows z virtuálního počítače:
- Otevřete Správce úloh.
- Přejít na kartu Podrobnosti
- Vyhledání procesu WindowsAzureGuestAgent.exe
- Klikněte pravým tlačítkem a vyberte Ukončit úlohu. Proces se automaticky restartuje.


K virtuálnímu počítači můžete také aktivovat nový GoalState spuštěním možnosti prázdná aktualizace:

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Rozhraní příkazového řádku Azure:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Pokud nefunguje prázdná aktualizace, můžete k virtuálnímu počítači z Azure Portál pro správu přidat nový prázdný datový disk a později ho odebrat, až se certifikát znovu přidá.
