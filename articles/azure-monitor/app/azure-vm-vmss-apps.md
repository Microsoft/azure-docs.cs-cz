---
title: Monitorování výkonu na virtuálních počítačích Azure – Azure Application Insights
description: Sledování výkonu aplikací pro virtuální počítače Azure a Azure Virtual Machine Scale Sets. Zatížení grafu a doba odezvy, informace o závislostech a nastavení výstrah pro výkon.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 0951d1d622f59de4780735fad78ac73649ea2369
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711477"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Nasazení agenta Azure Monitor Application Insights na virtuální počítače Azure a Azure Virtual Machine Scale Sets

Povolení monitorování webových aplikací založených na platformě .NET nebo Java, které běží na [virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/) a [Azure Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) , je teď jednodušší než kdy dřív. Získejte všechny výhody použití Application Insights beze změny kódu.

Tento článek vás provede povolením Application Insights monitorování pomocí agenta Application Insights a poskytuje předběžné pokyny pro automatizaci procesu pro rozsáhlá nasazení.
> [!IMPORTANT]
> Aplikace založené na **jazyce Java** běžící na virtuálních počítačích Azure a VMSS se monitorují pomocí **[Application Insightsho agenta Java 3,0](./java-in-process-agent.md)**, který je všeobecně dostupný.

> [!IMPORTANT]
> Agent Azure Application Insights pro aplikace ASP.NET běžící na **virtuálních počítačích Azure a VMSS** je momentálně ve verzi Public Preview. Pokud chcete monitorovat vaše aplikace ASP.Net spuštěné **v místním** prostředí, použijte [agenta Azure Application Insights pro místní servery](./status-monitor-v2-overview.md), které jsou všeobecně dostupné a plně podporované.
> Verze Preview pro virtuální počítače Azure a VMSS se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit monitorování aplikací pro virtuální počítače Azure a hostované aplikace Azure Virtual Machine Scale Sets:

### <a name="auto-instrumentation-via-application-insights-agent"></a>Automatické instrumentace prostřednictvím agenta Application Insights

* Tato metoda je nejjednodušší pro povolení a není nutná žádná pokročilá konfigurace. Často se označuje jako monitorování za běhu.

* Pro virtuální počítače Azure a službu Azure Virtual Machine Scale Sets doporučujeme, abyste tuto úroveň monitorování povedli minimálně. Na základě vašeho konkrétního scénáře můžete vyhodnotit, jestli je potřeba ruční instrumentace.

> [!NOTE]
> Automatická instrumentace je aktuálně dostupná jenom pro aplikace hostované službou IIS a Java. Pomocí sady SDK můžete instrumentovat aplikace ASP.NET Core, Node.js a Pythonu hostované na virtuálních počítačích Azure a ve službě Virtual Machine Scale Sets.


#### <a name="net"></a>.NET

  * Agent Application Insights automaticky shromažďuje stejné signály závislostí jako sadu .NET SDK. Další informace najdete v tématu [Automatická kolekce závislostí](./auto-collect-dependencies.md#net) .
        
#### <a name="java"></a>Java
  * Pro Java je doporučeným přístupem **[Agent java 3,0 Application Insights](./java-in-process-agent.md)** . Nejoblíbenější knihovny a architektury, jakož i protokoly a závislosti, se [shromažďují automaticky](./java-in-process-agent.md#auto-collected-requests-dependencies-logs-and-metrics), a to s využitím velkého množství [dalších konfigurací](./java-standalone-config.md) .

### <a name="code-based-via-sdk"></a>Pomocí sady SDK založené na kódu
    
#### <a name="net"></a>.NET
  * V případě aplikací .NET je tento přístup mnohem přizpůsobitelný, ale vyžaduje [Přidání závislosti na Application Insights balíčky NuGet sady SDK](./asp-net.md). Tato metoda také znamená, že je nutné spravovat aktualizace na nejnovější verzi balíčků sami.

  * Pokud potřebujete vlastní volání rozhraní API ke sledování událostí nebo závislostí, které se ve výchozím nastavení nezachycují pomocí monitorování založeného na agentech, musíte použít tuto metodu. Další informace najdete v [článku rozhraní API pro vlastní události a metriky](./api-custom-events-metrics.md) .

    > [!NOTE]
    > Jenom pro aplikace .NET – při monitorování a ruční instrumentaci založené na sadě SDK se uplatní jenom ruční nastavení instrumentace. K tomu je potřeba zabránit odesílání duplicitních dat. Další informace o této části najdete v [části řešení potíží](#troubleshooting) níže.

#### <a name="net-core"></a>.NET Core
Chcete-li monitorovat aplikace .NET Core, použijte [sadu SDK](./asp-net-core.md) 

#### <a name="java"></a>Java 

Pokud potřebujete další vlastní telemetrii pro aplikace Java, přečtěte si téma co [je k dispozici](./java-in-process-agent.md#send-custom-telemetry-from-your-application), přidejte [Vlastní rozměry](./java-standalone-config.md#custom-dimensions)nebo použijte [procesory telemetrie](./java-standalone-telemetry-processors.md). 

#### <a name="nodejs"></a>Node.js

K instrumentaci aplikace Node.js použijte [sadu SDK](./nodejs.md).

#### <a name="python"></a>Python

Pokud chcete monitorovat aplikace v Pythonu, použijte [sadu SDK](./opencensus-python.md).

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Správa Application Insights agenta pro aplikace .NET na virtuálních počítačích Azure pomocí PowerShellu

> [!NOTE]
> Před instalací agenta Application Insights budete potřebovat připojovací řetězec. [Vytvořte nový prostředek Application Insights](./create-new-resource.md) nebo zkopírujte připojovací řetězec z existujícího prostředku Application Insights.

> [!NOTE]
> Začínáte s PowerShellem? Přečtěte si [příručku Začínáme](/powershell/azure/get-started-azureps).

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
> Agenta Application Insights můžete nainstalovat nebo aktualizovat jako rozšíření v rámci více Virtual Machines na škále pomocí smyčky prostředí PowerShell.

Odinstalace rozšíření agenta Application Insights z virtuálního počítače Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Dotaz na stav rozšíření agenta Application Insights pro virtuální počítač Azure
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Získat seznam nainstalovaných rozšíření pro virtuální počítač Azure
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
Nainstalovaná rozšíření můžete zobrazit také v okně [virtuálního počítače Azure](../../virtual-machines/extensions/overview.md) na portálu.

> [!NOTE]
> Ověřte instalaci kliknutím na Live Metrics Stream v rámci prostředku Application Insights přidruženého k připojovacímu řetězci, který jste použili k nasazení rozšíření agenta Application Insights. Pokud odesíláte data z více Virtual Machines, vyberte v části název serveru cílové virtuální počítače Azure. Může trvat až minutu, než se data začnou přesměrovat.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Správa Application Insights agenta pro aplikace .NET na Azure Virtual Machine Scale Sets pomocí PowerShellu

Instalace nebo aktualizace agenta Application Insights jako rozšíření pro sadu škálování virtuálního počítače Azure
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

Odinstalace rozšíření monitorování aplikací ze služby Azure Virtual Machine Scale Sets
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Dotazování na stav rozšíření monitorování aplikací pro službu Azure Virtual Machine Scale Sets
```powershell
# Not supported by extensions framework
```

Získat seznam nainstalovaných rozšíření pro Azure Virtual Machine Scale Sets
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Poradce při potížích

Přečtěte si tipy pro řešení potíží pro rozšíření Application Insights Monitoring Agent pro aplikace .NET běžící na virtuálních počítačích Azure a službě Virtual Machine Scale Sets.

> [!NOTE]
> Aplikace .NET Core, Node.js a Python jsou podporované jenom na virtuálních počítačích Azure a Azure Virtual Machine Scale Sets prostřednictvím ruční instrumentace založené na sadě SDK, a proto se tyto kroky nevztahují na tyto scénáře.

Výstup spuštění rozšíření se protokoluje do souborů, které se nacházejí v následujících adresářích:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [nasadit aplikaci do sady škálování virtuálních počítačů Azure](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Nastavte webové testy dostupnosti](monitor-web-app-availability.md) , které se mají upozornit, pokud je koncový bod mimo provoz.