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
ms.openlocfilehash: 2fa87e860d0f5f5117840b9e230e383cdd6aae7c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187553"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Řešení potíží s chybami rozšíření Azure Windows VM
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Zobrazuje se stav rozšíření.
Šablony Azure Resource Manager lze spouštět z Azure PowerShell. Po provedení šablony lze stav rozšíření zobrazit z Azure Resource Explorer nebo nástrojů příkazového řádku.

Zde naleznete příklad:

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

