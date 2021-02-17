---
title: Monitorování řešení v Azure Monitor | Microsoft Docs
description: Řešení monitorování v Azure Monitor jsou kolekce pravidel pro logiku, vizualizaci a získávání dat, která poskytují metriky v určité oblasti problému.  Tento článek poskytuje informace o instalaci a používání řešení monitorování.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: d2e898c6401ff9959298e5e435d68b4c4a452c4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587283"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Monitorování řešení v Azure Monitor

Monitorování řešení v Azure Monitor poskytují analýzu provozu konkrétní aplikace nebo služby Azure. Tento článek poskytuje stručný přehled řešení monitorování v Azure a podrobnosti o jejich používání a instalaci. Můžete přidat řešení monitorování Azure Monitor pro všechny aplikace a služby, které používáte. Jsou obvykle k dispozici zdarma, ale shromažďují data, která by mohla vyvolat poplatky za využití.

## <a name="use-monitoring-solutions"></a>Použití řešení monitorování

Stránka **Přehled** řešení v Azure monitor zobrazuje dlaždici pro každé řešení nainstalované v pracovním prostoru Log Analytics. Tuto stránku otevřete tak, že v [Azure Portal](https://ms.portal.azure.com)přejdete na **Azure monitor** . V nabídce **přehledy** vyberte **Další** a otevřete **centrum Insights** a pak klikněte na **Log Analytics pracovní prostory**.

[![Centrum Insights](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Pomocí rozevíracích seznamů v horní části obrazovky změňte pracovní prostor nebo časový rozsah použitý pro dlaždice. Klikněte na dlaždici pro řešení a otevřete jeho zobrazení, které obsahuje podrobnější analýzu shromážděných dat.

[![Snímek obrazovky se zobrazí v nabídce Azure Portal s vybranými řešeními a řešeními, která se zobrazují v podokně řešení.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

Řešení monitorování mohou obsahovat více typů prostředků Azure a můžete si Zobrazit všechny prostředky, které jsou součástí řešení, stejně jako všechny ostatní prostředky. Například všechny dotazy protokolu zahrnuté v řešení jsou uvedeny v části **dotazy řešení** v [Průzkumníku dotazů](../logs/log-analytics-tutorial.md). Tyto dotazy můžete použít při provádění analýzy ad hoc pomocí [dotazů protokolu](../logs/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Výpis nainstalovaných řešení monitorování

### <a name="portal"></a>[Azure Portal](#tab/portal)

Pomocí následujícího postupu můžete zobrazit seznam řešení monitorování nainstalovaných ve vašem předplatném.

1. Přejděte na [Azure Portal](https://ms.portal.azure.com). Vyhledejte a vyberte **řešení**.
1. Jsou uvedena řešení nainstalovaná ve všech pracovních prostorech. Za názvem řešení následuje název pracovního prostoru, ve kterém je nainstalovaný.
1. Pomocí rozevíracích seznamů v horní části obrazovky můžete filtrovat podle předplatného nebo skupiny prostředků.

![Vypsat všechna řešení](media/solutions/list-solutions-all.png)

Kliknutím na název řešení otevřete jeho stránku Shrnutí. Tato stránka zobrazuje všechna zobrazení zahrnutá v řešení a poskytuje různé možnosti pro samotné řešení a jeho pracovní prostor. Zobrazte stránku souhrnu pro řešení pomocí jednoho z výše uvedených postupů pro výpis řešení a potom klikněte na název řešení.

![Vlastnosti řešení](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazu [AZ monitor Log-Analytics Solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) můžete zobrazit seznam řešení monitorování nainstalovaných ve vašem předplatném.   Před spuštěním `list` příkazu použijte požadavky zjištěné v části [instalace řešení monitorování](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Pomocí rutiny [Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) můžete zobrazit seznam řešení monitorování nainstalovaných ve vašem předplatném. Před spuštěním těchto příkazů postupujte podle požadavků uvedených v části [instalace řešení monitorování](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Instalace řešení monitorování

### <a name="portal"></a>[Azure Portal](#tab/portal)

Řešení monitorování od Microsoftu a partnerů jsou k dispozici na [Azure Marketplace](https://azuremarketplace.microsoft.com). Dostupná řešení můžete vyhledat a nainstalovat pomocí následujícího postupu. Když nainstalujete řešení, musíte vybrat [Log Analytics pracovní prostor](../logs/manage-access.md) , kde se bude řešení instalovat a kde se budou shromažďovat jeho data.

1. V [seznamu řešení pro vaše předplatné](#list-installed-monitoring-solutions)klikněte na **Přidat**.
1. Vyhledejte řešení nebo ho vyhledejte. Řešení můžete také procházet pomocí [tohoto odkazu pro hledání](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions).
1. Vyhledejte řešení monitorování, které chcete, a přečtěte si jeho popis.
1. Kliknutím na tlačítko **vytvořit** spusťte proces instalace.
1. Po spuštění procesu instalace budete vyzváni k zadání pracovního prostoru Log Analytics a k poskytnutí požadované konfigurace pro řešení.

![Instalace řešení](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalace řešení z komunity

Členové komunity mohou odesílat řešení pro správu do šablon pro rychlý Start Azure. Tato řešení můžete nainstalovat přímo nebo stáhnout šablony pro pozdější instalaci.

1. Pokud chcete propojit pracovní prostor a účet, postupujte podle kroků popsaných v [Log Analytics pracovního prostoru a účtu Automation](#log-analytics-workspace-and-automation-account) .
2. Přejít na [šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/)
3. Vyhledejte řešení, které vás zajímá.
4. Výběrem řešení z výsledků zobrazíte jeho podrobnosti.
5. Klikněte na tlačítko **nasadit do Azure** .
6. Budete vyzváni k zadání informací, jako je například skupina prostředků a umístění, spolu s hodnotami pro všechny parametry v řešení.
7. Pro instalaci řešení klikněte na **koupit** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Příprava prostředí

1. Instalace Azure CLI

   Před spuštěním příkazů odkazů CLI musíte [nainstalovat rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) .  Pokud budete chtít, můžete k dokončení kroků v tomto článku použít také Azure Cloud Shell.  Azure Cloud Shell je interaktivní prostředí prostředí, které používáte v prohlížeči.  Spusťte Cloud Shell pomocí jedné z následujících metod:

   - Otevřete Cloud Shell tak, že na [https://shell.azure.com](https://shell.azure.com)

   - Vyberte tlačítko **Cloud Shell** na řádku nabídek v pravém horním rohu [Azure Portal](https://portal.azure.com)

1. Přihlaste se.

   Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [AZ Login](/cli/azure/reference-index#az-login) .  Dokončete proces ověřování podle kroků zobrazených v terminálu.

    ```azurecli
    az login
    ```

1. Nainstalovat `log-analytics-solution` rozšíření

   `log-analytics-solution`Příkaz je experimentálním rozšířením základního rozhraní příkazového řádku Azure. Další informace o odkazech na rozšíření v [používání rozšíření pomocí Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Očekává se následující upozornění.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Instalace řešení pomocí Azure CLI

Když nainstalujete řešení, musíte vybrat [Log Analytics pracovní prostor](../logs/manage-access.md) , kde se bude řešení instalovat a kde se budou shromažďovat jeho data.  Pomocí Azure CLI můžete pracovní prostory spravovat pomocí příkazů [AZ monitor Log-Analytics pracovní prostor](/cli/azure/monitor/log-analytics/workspace) .  Pokud chcete propojit pracovní prostor a účet, postupujte podle kroků popsaných v [Log Analytics pracovního prostoru a účtu Automation](#log-analytics-workspace-and-automation-account) .

K instalaci řešení monitorování použijte [řešení AZ monitor Log-Analytics Create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) .  Parametry v hranatých závorkách jsou volitelné.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Zde je příklad kódu, který vytváří řešení pro analýzu protokolů pro produkt plánu OMSGallery/Containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Příprava prostředí

1. Instalace prostředí Azure PowerShell

   Před spuštěním příkazů Azure PowerShell odkazů je potřeba [nainstalovat Azure PowerShell](/powershell/azure/install-az-ps) . Pokud budete chtít, můžete k dokončení kroků v tomto článku použít také Azure Cloud Shell. Azure Cloud Shell je interaktivní prostředí prostředí, které používáte v prohlížeči. Spusťte Cloud Shell pomocí jedné z následujících metod:

   - Otevřete Cloud Shell tak, že na [https://shell.azure.com](https://shell.azure.com)

   - Vyberte tlačítko **Cloud Shell** na řádku nabídek v pravém horním rohu [Azure Portal](https://portal.azure.com)

   > [!IMPORTANT]
   > I když je modul PowerShell **AZ. MonitoringSolutions** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Přihlaste se.

   Pokud používáte místní instalaci PowerShellu, přihlaste se pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Proces ověřování dokončíte podle kroků zobrazených v prostředí PowerShell.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Instalace řešení pomocí Azure PowerShell

Když nainstalujete řešení, musíte vybrat [Log Analytics pracovní prostor](../logs/manage-access.md) , kde se bude řešení instalovat a kde se budou shromažďovat jeho data. Pomocí Azure PowerShell můžete pracovní prostory spravovat pomocí rutin v modulu PowerShell [AZ. MonitoringSolutions](/powershell/module/az.monitoringsolutions) . Pokud chcete propojit pracovní prostor a účet, postupujte podle kroků popsaných v [Log Analytics pracovního prostoru a účtu Automation](#log-analytics-workspace-and-automation-account) .

K instalaci řešení monitorování použijte rutinu [New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) . Parametry v hranatých závorkách jsou volitelné.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

Následující příklad vytvoří řešení monitorování Log Analytics pro pracovní prostor Log Analytics.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics pracovní prostor a účet Automation

Všechna řešení monitorování vyžadují [Log Analytics pracovní prostor](../logs/manage-access.md) k ukládání dat shromažďovaných řešením a k hostování jeho prohledávání a zobrazení protokolů. Některá řešení také vyžadují [účet Automation](../../automation/automation-security-overview.md) , který obsahuje Runbooky a související prostředky. Pracovní prostor a účet musí splňovat následující požadavky.

* Každá instalace řešení může použít jenom jeden Log Analytics pracovní prostor a jeden účet Automation. Řešení můžete nainstalovat samostatně do několika pracovních prostorů.
* Pokud řešení vyžaduje účet Automation, musí být tento pracovní prostor Log Analytics a účet Automation vzájemně propojený. Log Analytics pracovní prostor může být propojený jenom s jedním účtem Automation a účet Automation se dá propojit jenom s jedním pracovním prostorem Log Analytics.

Když nainstalujete řešení prostřednictvím Azure Marketplace, budete vyzváni k zadání pracovního prostoru a účtu Automation. Propojení mezi nimi je vytvořeno, pokud již nejsou propojena.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Ověření propojení mezi Log Analyticsm pracovním prostorem a účtem služby Automation

Pomocí následujícího postupu můžete ověřit propojení mezi Log Analytics pracovním prostorem a účtem služby Automation.

1. V Azure Portal vyberte účet Automation.
1. Přejděte do části **související prostředky** v nabídce a vyberte **propojený pracovní prostor**.
1. Pokud je **pracovní prostor** propojený s účtem služby Automation, pak na této stránce je uveden pracovní prostor, se kterým je propojen. Pokud vyberete název pracovního prostoru v seznamu, budete přesměrováni na stránku Přehled pro daný pracovní prostor.

## <a name="remove-a-monitoring-solution"></a>Odebrání řešení monitorování

### <a name="portal"></a>[Azure Portal](#tab/portal)

Pokud chcete nainstalované řešení odebrat pomocí portálu, vyhledejte ho v [seznamu nainstalovaných řešení](#list-installed-monitoring-solutions). Kliknutím na název řešení otevřete jeho stránku Souhrn a pak klikněte na **Odstranit**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nainstalované řešení odebrat pomocí Azure CLI, použijte příkaz [AZ monitor Log-Analytics Solution Delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete) .

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

K odebrání nainstalovaného řešení pomocí Azure PowerShell použijte rutinu [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Další kroky

* Získejte [seznam řešení monitorování od Microsoftu](../monitor-reference.md).
* Naučte se [vytvářet dotazy](../logs/log-query-overview.md) k analýze dat shromažďovaných monitorovacími řešeními.
* Podívejte se [na všechny příkazy rozhraní příkazového řádku Azure pro Azure monitor](/cli/azure/azure-cli-reference-for-monitor).