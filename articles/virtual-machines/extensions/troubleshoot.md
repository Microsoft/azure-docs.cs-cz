---
title: Řešení potíží s chybami rozšíření virtuálních počítačů Windows | Dokumentace Microsoftu
description: Další informace o řešení potíží s chybami rozšíření virtuálních počítačů Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979689"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Řešení potíží s chybami rozšíření virtuálních počítačů Windows Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Zobrazení stavu rozšíření
Šablony Azure Resource Manageru můžete spustit z prostředí Azure PowerShell. Po spuštění šablony stav extension lze zobrazit v Průzkumníku prostředků Azure nebo nástroje příkazového řádku.

Zde naleznete příklad:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Tady je ukázkový výstup:

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
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Řešení potíží s chybami rozšíření
### <a name="rerun-the-extension-on-the-vm"></a>Znovu spusťte rozšíření na virtuálním počítači
Pokud se spouštění skriptů na virtuálním počítači pomocí rozšíření vlastních skriptů, může občas spouštějí došlo k chybě, kde byl virtuální počítač vytvořen úspěšně, ale došlo k selhání skriptu. Za těchto podmínek je doporučeným způsobem, jak tuto chybu odebrat rozšíření a znovu spustit šablonu.
Poznámka: Tato funkce v budoucích verzích by se zvýšila odebrat potřeba odinstalovat rozšíření.

#### <a name="remove-the-extension-from-azure-powershell"></a>Odeberte rozšíření z prostředí Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Odebraný rozšíření šablonou může být znovu spustit na spouštění skriptů na virtuálním počítači.

