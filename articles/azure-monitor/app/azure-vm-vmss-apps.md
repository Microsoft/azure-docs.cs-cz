---
title: Monitorování výkonu aplikací hostovaných na virtuálních počítačích Azure a Azure Virtual Machine Scale Sets | Microsoft Docs
description: Sledování výkonu aplikací pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Zatížení grafu a doba odezvy, informace o závislostech a nastavení výstrah pro výkon.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: 69ae6ab6caedd9e89d277b92558931685d2ea320
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877308"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>Monitorování výkonu aplikací hostovaných na virtuálních počítačích Azure a Azure Virtual Machine Scale Sets

Povolení monitorování webových aplikací založených na rozhraní .NET běžících na [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) a [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) je teď jednodušší než kdy dřív. Získejte všechny výhody použití Application Insights beze změny kódu.

Tento článek vás provede povolením Application Insights monitoring pomocí rozšíření ApplicationMonitoringWindows a poskytnutím úvodních pokynů pro automatizaci procesu pro rozsáhlá nasazení.

> [!IMPORTANT]
> Rozšíření Azure ApplicationMonitoringWindows je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit monitorování aplikací pro hostované aplikace virtuálních počítačů Azure a Azure Virtual Machine Scale set:

* **Monitorování aplikací na základě agentů** (Rozšíření ApplicationMonitoringWindows).
    * Tato metoda je nejjednodušší pro povolení a není nutná žádná pokročilá konfigurace. Často se označuje jako monitorování za běhu. U virtuálních počítačů Azure a služby Azure Virtual Machine Scale Sets doporučujeme, abyste tuto úroveň monitorování povedli minimálně. Na základě vašeho konkrétního scénáře můžete vyhodnotit, jestli je potřeba ruční instrumentace.
    * V současné době jsou podporovány pouze aplikace hostované v rozhraní .NET IIS.

* **Ruční instrumentace aplikace pomocí kódu** instalací sady Application Insights SDK.

    * Tento přístup je mnohem přizpůsobitelnější, ale vyžaduje [Přidání závislosti na balíčky NuGet sady Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Tato metoda také znamená, že je nutné spravovat aktualizace na nejnovější verzi balíčků sami.

    * Pokud potřebujete vlastní volání rozhraní API ke sledování událostí nebo závislostí, které se ve výchozím nastavení nezachycují pomocí monitorování založeného na agentech, musíte použít tuto metodu. Další informace najdete v [článku rozhraní API pro vlastní události a metriky](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Pokud se zjistí jenom monitorování na základě agentů a ruční instrumentaci založené na sadě SDK, bude se dodržovat jenom ruční nastavení instrumentace. K tomu je potřeba zabránit odesílání duplicitních dat. Další informace o této části najdete v [části řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-apps#troubleshooting) níže.

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>Správa monitorování na základě agentů pro aplikace .NET na virtuálním počítači pomocí PowerShellu

Instalace nebo aktualizace rozšíření monitorování aplikací pro virtuální počítač
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

Odinstalovat rozšíření monitorování aplikací z virtuálního počítače
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Dotazování na stav rozšíření monitorování aplikací pro virtuální počítač
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Získat seznam nainstalovaných rozšíření pro virtuální počítač
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>Správa monitorování na základě agentů pro aplikace .NET v Azure Virtual Machine Scale set pomocí PowerShellu

Instalace nebo aktualizace rozšíření monitorování aplikací pro sadu škálování virtuálního počítače Azure
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Odinstalace rozšíření monitorování aplikací ze sady škálování virtuálních počítačů Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Dotazování na stav rozšíření monitorování aplikací pro sadu škálování virtuálních počítačů Azure
```powershell
# Not supported by extensions framework
```

Získat seznam nainstalovaných rozšíření pro sadu škálování virtuálních počítačů Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Řešení potíží

Níže najdete naše podrobné pokyny k odstraňování potíží pro monitorování na základě rozšíření pro aplikace .NET běžící na virtuálních počítačích Azure a ve službě Azure Virtual Machine Scale Sets.

> [!NOTE]
> Aplikace .NET Core, Java a Node. js jsou podporované jenom na virtuálních počítačích Azure a Azure Virtual Machine Scale Sets prostřednictvím ruční instrumentace založené na sadě SDK, a proto se tyto kroky nevztahují na tyto scénáře.

Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujících adresářích:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Další postup
* Naučte se, jak [nasadit aplikaci do sady škálování virtuálních počítačů Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Nastavte webové testy dostupnosti](monitor-web-app-availability.md) , které se mají upozornit, pokud je koncový bod mimo provoz.
