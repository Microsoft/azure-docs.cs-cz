---
title: Událost skupiny zabezpečení sítě a pravidlo čítače diagnostické protokoly Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, jak povolit protokoly událostí a pravidlo čítače diagnostiky pro skupinu zabezpečení sítě Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: 63dd7b0aad3ce42eff3c135bd2fdccc8fcb4ed61
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014076"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Protokolování diagnostiky pro skupiny zabezpečení sítě

Skupina zabezpečení sítě (NSG) obsahuje pravidla, která povolují nebo zakazují provoz do podsítě virtuální sítě, síťové rozhraní nebo obojí. Když povolíte diagnostiku protokolování pro skupinu zabezpečení sítě, se můžete přihlásit následující kategorie informací:

* **událost:** Položky jsou zaznamenány, pro které skupina NSG pravidla se použijí u virtuálních počítačů, na základě adresy MAC. Stav pro tato pravidla shromažďovaných každých 60 sekund.
* **Pravidlo čítače:** Obsahuje položky pro kolikrát se každé pravidlo NSG se použije pro odepření nebo povolení provozu.

Diagnostické protokoly jsou dostupné jenom pro skupiny zabezpečení sítě nasazené pomocí modelu nasazení Azure Resource Manageru. Nelze povolit protokolování diagnostiky pro skupiny zabezpečení sítě nasazené pomocí modelu nasazení classic. Lépe pochopit ze dvou modelů, naleznete v tématu [modelů nasazení Azure Principy](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Protokolování diagnostiky zvlášť zapnutá *každý* NSG, které chcete shromažďovat diagnostická data. Pokud vás zajímá provozní, nebo protokoly aktivit, místo toho, podívejte se na Azure [protokolování aktivit](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Povolit protokolování

Můžete použít [webu Azure Portal](#azure-portal), [PowerShell](#powershell), nebo [rozhraní příkazového řádku Azure](#azure-cli) povolit protokolování diagnostiky.

### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu](https://portal.azure.com).
2. Vyberte **všechny služby**, zadejte *skupiny zabezpečení sítě*. Když **skupiny zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ji.
3. Vyberte skupiny zabezpečení sítě, které chcete povolit protokolování.
4. V části **monitorování**vyberte **diagnostické protokoly**a pak vyberte **zapnout diagnostiku**, jak je znázorněno na následujícím obrázku:
 
    ![Zapnout diagnostiku](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. V části **nastavení diagnostiky**, zadejte nebo vyberte následující informace a pak vyberte **Uložit**:

    | Nastavení                                                                                     | Hodnota                                                          |
    | ---------                                                                                   |---------                                                       |
    | Název                                                                                        | Název, podle vašeho výběru.  Příklad: *myNsgDiagnostics*      |
    | **Archivovat do účtu úložiště**, **Stream do centra událostí**, a **odesílat do Log Analytics** | Můžete vybrat libovolný počet cílů, zvolíte. Další informace o jednotlivých najdete v tématu [protokolu cíle](#log-destinations).                                                                                                                                           |
    | PROTOKOL                                                                                         | Vyberte jednoho nebo obou protokolů kategorie. Další informace o data protokolovaná pro každou kategorii, naleznete v tématu [protokolu kategorie](#log-categories).                                                                                                                                             |
6. Zobrazení a analýza protokolů. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo pomocí prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Při spuštění PowerShell z počítače, je nutné *AzureRM* modul prostředí PowerShell, verze 6.1.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` v počítači nainstalovanou verzi zjistíte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions)].

Pokud chcete povolit protokolování diagnostiky, budete potřebovat Id existující skupině NSG. Pokud nemáte existující skupině NSG, můžete vytvořit s [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Načíst skupinu zabezpečení sítě, které chcete povolit diagnostické protokolování pomocí [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Například načíst skupinu zabezpečení sítě s názvem *myNsg* , který existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Můžete zaznamenat diagnostické protokoly na tři cílové typů. Další informace najdete v tématu [protokolu cíle](#log-destinations). V tomto článku jsou odesílat protokoly *Log Analytics* cíl jako příklad. Načíst existující pracovní prostor Log Analytics s [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Například se načíst existující pracovní prostor s názvem *myWorkspace* ve skupině prostředků s názvem *myWorkspaces*, zadejte následující příkaz:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Pokud nemáte existující pracovní prostor, můžete vytvořit s [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Existují dvě kategorie protokolování můžete povolit protokoly. Další informace najdete v tématu [protokolu kategorie](#log-categories). Povolit protokolování diagnostiky pro skupiny zabezpečení sítě s [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). Následující příklad protokoly událostí a čítače kategorie dat do pracovního prostoru pro skupinu zabezpečení sítě pomocí ID pro skupiny zabezpečení sítě a pracovní prostor, který jste získali dříve:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Pokud chcete protokolovat data pro jednu kategorii nebo druhé, nikoli obojí, přidejte `-Categories` možnost předchozího příkazu, za nímž následuje *NetworkSecurityGroupEvent* nebo *NetworkSecurityGroupRuleCounter*. Pokud chcete protokolovat do jiného [cílové](#log-destinations) než pracovní prostor Log Analytics, použijte příslušné parametry Azure [účtu úložiště](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [centra událostí](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zobrazení a analýza protokolů. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku Azure z vašeho počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte z počítače rozhraní příkazového řádku, musíte verze 2.0.38 nebo novější. Spustit `az --version` v počítači nainstalovanou verzi zjistíte. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). Pokud používáte rozhraní příkazového řádku místně, musíte také spustit `az login` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Pokud chcete povolit protokolování diagnostiky, budete potřebovat Id existující skupině NSG. Pokud nemáte existující skupině NSG, můžete vytvořit s [az network nsg vytvořit](/cli/azure/network/nsg#az-network-nsg-create).

Načíst skupinu zabezpečení sítě, které chcete povolit diagnostické protokolování pomocí [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show). Například načíst skupinu zabezpečení sítě s názvem *myNsg* , který existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Můžete zaznamenat diagnostické protokoly na tři cílové typů. Další informace najdete v tématu [protokolu cíle](#log-destinations). V tomto článku jsou odesílat protokoly *Log Analytics* cíl jako příklad. Další informace najdete v tématu [protokolu kategorie](#log-categories). 

Povolit protokolování diagnostiky pro skupiny zabezpečení sítě s [az monitor diagnostiky – nastavení vytváření](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). Následující příklad protokoly událostí a čítače kategorie dat do existujícího pracovního prostoru s názvem *myWorkspace*, který existuje ve skupině prostředků s názvem *myWorkspaces*a ID skupiny zabezpečení sítě, který jste získali dříve:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Pokud nemáte existující pracovní prostor, můžete vytvořit jednu [webu Azure portal](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [Powershellu](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace). Existují dvě kategorie protokolování můžete povolit protokoly. 

Pokud chcete protokolovat data pro jednu kategorii z nich, odeberte kategorie, které nechcete vytvářet protokoly dat pro v předchozím příkazu. Pokud chcete protokolovat do jiného [cílové](#log-destinations) než pracovní prostor Log Analytics, použijte příslušné parametry Azure [účtu úložiště](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [centra událostí](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zobrazení a analýza protokolů. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

## <a name="log-destinations"></a>Log cílů

Diagnostická data může být:
- [Zapsat do účtu služby Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), pro auditování nebo ruční kontrolu. Můžete zadat dobu uchování (ve dnech), pomocí nastavení diagnostiky prostředku.
- [Streamovat do centra událostí](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) za účelem ingestování datových služby třetích stran nebo vlastních analýzy řešení, jako je například Power BI.
- [Zapsat do služby Azure Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Kategorie protokolu

Data ve formátu JSON je určené pro následující kategorie protokolu:

### <a name="event"></a>Událost

Protokol událostí obsahuje informace o tom, které se použijí pravidla skupiny zabezpečení sítě pro virtuální počítače, na základě adresy MAC. Pro všechny události se protokolují tato data. V následujícím příkladu se pro virtuální počítač s 192.168.1.4 IP adresu a adresu MAC 00-0D-3A-92-6A-7C protokoluje data:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":[PRIORITY-SPECIFIED-IN-RULE],
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Pravidlo čítače

Protokol čítač pravidlo obsahuje informace o každé pravidlo použito pro prostředky. Následující příklad data se protokoluje pokaždé, když se pravidlo použije. V následujícím příkladu se pro virtuální počítač s 192.168.1.4 IP adresu a adresu MAC 00-0D-3A-92-6A-7C protokoluje data:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Zdrojová IP adresa pro komunikaci se neprotokolují. Můžete povolit [protokolování toků NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) pro skupinu zabezpečení sítě, které zaznamená všechny informace o pravidle čítače, stejně jako zdrojové IP adresy, který spustil komunikace. Data protokolu toku NSG se zapisují do účtu Azure Storage. Můžete analyzovat data s využitím [traffic analytics](../network-watcher/traffic-analytics.md) funkce služby Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Zobrazení a analýza protokolů

Další postup zobrazení diagnostických protokolů dat najdete v tématu [přehled diagnostických protokolů Azure](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud budete posílat diagnostická data pro:
- **Log Analytics**: Můžete použít [analýzy skupin zabezpečení sítě](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) řešení pokročilých přehledů. Řešení poskytuje vizualizace pro pravidla NSG, která povolují nebo zakazují provoz na adresu MAC síťového rozhraní na virtuálním počítači.
- **Účet služby Azure Storage**: Data se zapisují do souboru PT1H.json. Můžete najít:
    - Protokol událostí v následující cestě: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Pravidlo protokolu čítačů v následující cestě: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Další postup

- Další informace o [protokolování aktivit](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), dříve označovaný jako audit nebo provozní protokoly. Ve výchozím nastavení je povoleno protokolování aktivit pro skupiny zabezpečení sítě vytvořených prostřednictvím obou modelů nasazení Azure. Pokud chcete zjistit, operace, které byly dokončeny na skupiny zabezpečení sítě v protokolu aktivit, vyhledejte položky, které obsahují následující typy prostředků:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Zjistěte, jak protokolovat diagnostické informace, včetně zdrojovou IP adresu pro každý tok, najdete v článku [protokolování toků NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
