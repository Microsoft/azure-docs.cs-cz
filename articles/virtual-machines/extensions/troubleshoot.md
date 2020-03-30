---
title: Poradce při potížích s rozšířením virtuálního aplikace windows
description: Informace o řešení potíží s chybami rozšíření virtuálních aplikací Azure pro Windows
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
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073658"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Řešení potíží s chybami rozšíření virtuálních aplikací Azure pro Windows
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Zobrazení stavu rozšíření
Šablony Azure Resource Manageru se můžou spouštět z Azure PowerShellu. Po spuštění šablony se stav rozšíření zobrazí z Průzkumníka prostředků Azure nebo z nástrojů příkazového řádku.

Zde naleznete příklad:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Zde je ukázkový výstup:

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

## <a name="troubleshooting-extension-failures"></a>Poradce při potížích s chybami rozšíření
### <a name="rerun-the-extension-on-the-vm"></a>Opětovné spuštění rozšíření na virtuálním počítači
Pokud na virtuálním počítači používáte vlastní rozšíření skriptu, můžete někdy narazit na chybu, kde byl virtuální počítač úspěšně vytvořen, ale skript se nezdařil. Za těchto podmínek je doporučeným způsobem obnovení z této chyby odebrání rozšíření a opětovné spuštění šablony znovu.
Poznámka: V budoucnu by tato funkce byla rozšířena, aby se odstranila potřeba odinstalace rozšíření.

#### <a name="remove-the-extension-from-azure-powershell"></a>Odebrání rozšíření z Azure PowerShellu
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Po odebrání rozšíření, šablonu lze znovu spustit ke spuštění skriptů na virtuálním počítači.

