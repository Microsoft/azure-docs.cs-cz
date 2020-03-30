---
title: Sledování výkonu na virtuálních počítačích Azure – Přehledy aplikací Azure
description: Monitorování výkonu aplikací pro škálovací sady virtuálních zařízení Azure a virtuálních strojů Azure. Doba načítání a odezvy grafu, informace o závislostech a nastavení výstrah na výkon.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661324"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Nasazení agenta Azure Monitor Application Insights na virtuálních počítačích Azure a škálovací sady virtuálních počítačů Azure

Povolení monitorování webových aplikací založených na rozhraní .NET spuštěných na [virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/) a [škálovacích sadách virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) je teď jednodušší než kdy dřív. Získejte všechny výhody používání Application Insights bez úpravy kódu.

Tento článek vás provede povolením monitorování přehledů aplikací pomocí agenta Application Insights a poskytne předběžné pokyny pro automatizaci procesu pro rozsáhlá nasazení.

> [!IMPORTANT]
> Agent Azure Application Insights pro rozhraní .NET je aktuálně ve verzi Public Preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce nemusí být podporovány a některé mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit monitorování aplikací pro virtuální počítače Azure a škálovací sady virtuálních počítačů Azure hostované aplikace:

* **Bezkódové** prostřednictvím agenta Application Insights
    * Tato metoda je nejjednodušší povolit a není vyžadována žádná pokročilá konfigurace. Často se označuje jako "runtime" monitorování.

    * Pro virtuální počítače Azure a škálovací sady virtuálních počítačů Azure doporučujeme minimálně povolit tuto úroveň monitorování. Poté můžete na základě konkrétního scénáře vyhodnotit, zda je potřeba ruční instrumentace.

    * Agent Application Insights automaticky shromažďuje stejné signály závislostí out-of-the-box jako .NET SDK. Další [informace najdete v tématu Automatické shromažďování závislostí.](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)
        > [!NOTE]
        > V současné době jsou podporovány pouze aplikace hostované pomocí rozhraní IIS. Pomocí sady SDK můžete instrumentovat aplikace ASP.NET jádra, javy a node.js hostované na virtuálních počítačích Azure a škálovacích sadách virtuálních počítačů.

* **Na základě kódu** prostřednictvím sady SDK

    * Tento přístup je mnohem více přizpůsobitelné, ale vyžaduje [přidání závislosti na balíčcích Application Insights SDK NuGet](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Tato metoda také znamená, že budete muset spravovat aktualizace nejnovější verze balíčků sami.

    * Pokud potřebujete provést vlastní volání rozhraní API ke sledování událostí nebo závislostí, které nejsou ve výchozím nastavení zachyceny pomocí monitorování založeného na agentovi, budete muset použít tuto metodu. Další informace najdete v [článku api pro vlastní události a metriky.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)

> [!NOTE]
> Pokud je zjištěno monitorování založené na agentech a ruční instrumentace založená na sadě SDK, bude dodrženo pouze ruční nastavení instrumentace. Tím zabráníte odeslání duplicitních dat. Další informace o tomto článku najdete v [části řešení potíží](#troubleshooting) níže.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Správa agenta přehledů aplikací pro aplikace .NET na virtuálních počítačích Azure pomocí PowerShellu

> [!NOTE]
> Před instalací agenta Application Insights budete potřebovat připojovací řetězec. [Vytvořte nový prostředek Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) nebo zkopírujte připojovací řetězec z existujícího prostředku přehledů aplikací.

> [!NOTE]
> Začínáte s powershellem? Podívejte se na [příručku Začínáme](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0).

Instalace nebo aktualizace agenta Application Insights jako rozšíření pro virtuální počítače Azure
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Agenta Application Insights můžete nainstalovat nebo aktualizovat jako rozšíření napříč více virtuálními počítači ve velkém měřítku pomocí smyčky Powershellu.

Odinstalace rozšíření Agent application insights z virtuálního počítače Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Stav rozšíření agenta přehledů aplikací pro virtuální počítač Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Získání seznamu nainstalovaných rozšíření pro virtuální počítač Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Můžete také zobrazit nainstalované rozšíření v [okně virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) na portálu.

> [!NOTE]
> Ověřte instalaci kliknutím na stream živých metrik v rámci prostředku Application Insights přidruženého k připojovacímu řetězci, který jste použili k nasazení rozšíření agenta Application Insights. Pokud odesíláte data z více virtuálních počítačů, vyberte cílové virtuální počítače Azure v části Název serveru. Může trvat až minutu, než data začnou proudit.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Správa agenta přehledů aplikací pro aplikace .NET ve škálovacích sadách virtuálních strojů Azure pomocí powershellu

Instalace nebo aktualizace agenta Application Insights jako rozšíření pro škálovací sadu virtuálních strojů Azure
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

Odinstalace rozšíření monitorování aplikací ze škálovacích sad virtuálních strojů Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Stav rozšíření monitorování aplikací dotazu pro škálovací sady virtuálních strojů Azure
```powershell
# Not supported by extensions framework
```

Získání seznamu nainstalovaných rozšíření pro škálovací sady virtuálních strojů Azure
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Řešení potíží

Najděte tipy pro řešení potíží s rozšířením Application Insights Monitoring Agent Extension pro aplikace .NET spuštěné na virtuálních počítačích Azure a škálovacísady virtuálních počítačů.

> [!NOTE]
> Aplikace .NET Core, Java a Node.js jsou podporované jenom na virtuálních počítačích Azure a škálovacích sadách virtuálních počítačů Azure prostřednictvím ruční instrumentace založené na sadě SDK, a proto se následující kroky nevztahují na tyto scénáře.

Výstup spuštění rozšíření je zaznamenán do souborů nalezených v následujících adresářích:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [nasadit aplikaci do škálovací sady virtuálních strojů Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Nastavte webové testy dostupnosti,](monitor-web-app-availability.md) které mají být upozorněny, pokud je koncový bod vypnutý.
