---
title: Protokolování diagnostických prostředků pro skupinu zabezpečení sítě
titlesuffix: Azure Virtual Network
description: Naučte se, jak povolit protokoly událostí a pravidel diagnostiky čítače prostředků pro skupinu zabezpečení sítě Azure.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 42ce7a1760ecdb1dcbd5275927f351bef5da07a8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531165"
---
# <a name="resource-logging-for-a-network-security-group"></a>Protokolování prostředků pro skupinu zabezpečení sítě

Skupina zabezpečení sítě (NSG) obsahuje pravidla, která povolují nebo zakazují provoz do podsítě virtuální sítě, síťového rozhraní nebo obojího. 

Pokud povolíte protokolování pro NSG, můžete shromáždit následující typy informací o protokolu prostředků:

* **Událost:** Zaznamenávají se záznamy, pro které se pravidla NSG aplikují na virtuální počítače na základě adresy MAC.
* **Čítač pravidla:** Obsahuje položky pro počet, kolikrát se každé pravidlo NSG použije pro odepření nebo povolení provozu. Stav těchto pravidel se shromáždí každých 300 sekund.

Protokoly prostředků jsou k dispozici pouze pro skupin zabezpečení sítě nasazené prostřednictvím modelu nasazení Azure Resource Manager. Nemůžete povolit protokolování prostředků pro skupin zabezpečení sítě nasazené prostřednictvím modelu nasazení Classic. Lepší porozumění těmto dvěma modelům najdete v tématu [Principy modelů nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Protokolování prostředků je povoleno samostatně pro *každý* NSG, pro který chcete shromažďovat diagnostická data. Pokud chcete místo toho zajímat protokoly o aktivitách (provozní), přečtěte si téma [protokolování aktivit](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure. Pokud vás zajímá provoz IP toků přes skupin zabezpečení sítě, přečtěte si téma [protokoly toku](../network-watcher/network-watcher-nsg-flow-logging-overview.md) služby Azure Network Watcher NSG. 

## <a name="enable-logging"></a>Povolit protokolování

Pomocí webu [Azure Portal](#azure-portal), [PowerShellu](#powershell)nebo rozhraní příkazového [řádku Azure](#azure-cli) můžete povolit protokolování prostředků.

### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu](https://portal.azure.com).
2. Vyberte **všechny služby** a pak zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě** , vyberte ji.
3. Vyberte NSG, pro který chcete povolit protokolování.
4. V části **monitorování** vyberte **diagnostické protokoly** a pak vyberte **zapnout diagnostiku**, jak je znázorněno na následujícím obrázku:

   ![Zapnout diagnostiku](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. V části **nastavení diagnostiky** zadejte nebo vyberte následující informace a pak vyberte **Uložit**:

    | Nastavení                                                                                     | Hodnota                                                          |
    | ---------                                                                                   |---------                                                       |
    | Název                                                                                        | Název, který zvolíte.  Příklad: *myNsgDiagnostics*      |
    | **Archivace do účtu úložiště**, **streamování do centra událostí** a **odeslání do Log Analytics** | Můžete vybrat tolik míst, kolik si zvolíte. Další informace o každé z nich najdete v tématu [cíle protokolu](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Vyberte jednu nebo obě kategorie protokolu. Další informace o datech protokolovaných pro jednotlivé kategorie najdete v tématu [Kategorie protokolů](#log-categories).                                                                                                                                             |
6. Zobrazit a analyzovat protokoly. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním PowerShellu z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Pokud `Get-Module -ListAvailable Az` chcete najít nainstalovanou verzi, spusťte v počítači. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte prostředí PowerShell místně, je také třeba spustit `Connect-AzAccount` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Pokud chcete povolit protokolování prostředků, potřebujete ID existující NSG. Pokud nemáte existující NSG, můžete ho vytvořit pomocí [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Načtěte skupinu zabezpečení sítě, u které chcete povolit protokolování prostředků pomocí [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Pokud například chcete načíst NSG s názvem *myNsg* , který existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Můžete zapsat protokoly prostředků do tří cílových typů. Další informace najdete v tématu [cíle protokolu](#log-destinations). V tomto článku jsou protokoly odesílány do cíle *Log Analytics* jako příklad. Načtěte existující Log Analytics pracovní prostor pomocí [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Pokud například chcete načíst existující pracovní prostor s názvem *myWorkspace* ve skupině prostředků s názvem *myWorkspaces*, zadejte následující příkaz:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Pokud nemáte existující pracovní prostor, můžete ho vytvořit pomocí [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Existují dvě kategorie protokolování, pro které můžete protokoly povolit. Další informace najdete v tématu [Kategorie protokolů](#log-categories). Povolte protokolování prostředků pro NSG pomocí [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). V následujícím příkladu se protokolují data kategorií událostí a čítačů do pracovního prostoru pro NSG pomocí ID pro NSG a pracovní prostor, který jste dříve získali:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Pokud chcete pouze protokolovat data pro jednu kategorii nebo druhou, nikoli pro obě, přidejte `-Categories` možnost do předchozího příkazu následovaný *NetworkSecurityGroupEvent* nebo *NetworkSecurityGroupRuleCounter*. Pokud se chcete přihlásit do jiného [cílového umístění](#log-destinations) než do Log Analytics pracovního prostoru, použijte příslušné parametry pro účet služby Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) nebo [centrum událostí](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Zobrazit a analyzovat protokoly. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spustit Azure CLI z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spouštíte rozhraní příkazového řádku z počítače, budete potřebovat verzi 2.0.38 nebo novější. Pokud `az --version` chcete najít nainstalovanou verzi, spusťte v počítači. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli). Pokud spouštíte rozhraní příkazového řádku místně, musíte také spustit `az login` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Pokud chcete povolit protokolování prostředků, potřebujete ID existující NSG. Pokud nemáte existující NSG, můžete ho vytvořit pomocí [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

Načtěte skupinu zabezpečení sítě, pro kterou chcete povolit protokolování prostředků pomocí [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show). Pokud například chcete načíst NSG s názvem *myNsg* , který existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Můžete zapsat protokoly prostředků do tří cílových typů. Další informace najdete v tématu [cíle protokolu](#log-destinations). V tomto článku jsou protokoly odesílány do cíle *Log Analytics* jako příklad. Další informace najdete v tématu [Kategorie protokolů](#log-categories).

Povolení protokolování prostředků pro NSG pomocí [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) V následujícím příkladu se protokoluje data kategorií událostí a čítačů do existujícího pracovního prostoru s názvem *myWorkspace*, který existuje ve skupině prostředků s názvem *MYWORKSPACES*, a ID NSG, který jste dříve získali:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Pokud nemáte existující pracovní prostor, můžete ho vytvořit pomocí [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [PowerShellu](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Existují dvě kategorie protokolování, pro které můžete protokoly povolit.

Pokud chcete pouze protokolovat data pro jednu kategorii nebo druhou, odeberte kategorii, pro kterou nechcete protokolovat data v předchozím příkazu. Pokud se chcete přihlásit do jiného [cílového umístění](#log-destinations) než do Log Analytics pracovního prostoru, použijte příslušné parametry pro účet služby Azure [Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) nebo [centrum událostí](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Zobrazit a analyzovat protokoly. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

## <a name="log-destinations"></a>Cíle protokolu

Diagnostická data můžou být:
- [Zápis na účet Azure Storage](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)pro auditování nebo ruční kontrolu. Dobu uchování (ve dnech) můžete určit pomocí nastavení diagnostiky prostředků.
- [Streamování do centra událostí](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) pro ingestování prostřednictvím služby třetí strany nebo řešení pro vlastní analýzu, jako je PowerBI.
- [Zapsáno do protokolu Azure monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Kategorie protokolů

Data ve formátu JSON se napíší pro následující kategorie protokolů:

### <a name="event"></a>Událost

Protokol událostí obsahuje informace o tom, která pravidla NSG se na virtuální počítače aplikují na základě adresy MAC. Pro každou událost jsou protokolována následující data. V následujícím příkladu se zaprotokolují data pro virtuální počítač s IP adresou 192.168.1.4 a adresou MAC 00-0D-3A-92-6A-7C:

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
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
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

### <a name="rule-counter"></a>Čítač pravidla

Protokol čítače pravidla obsahuje informace o každém pravidle použitém u prostředků. Následující příklad data se protokolují při každém použití pravidla. V následujícím příkladu se zaprotokolují data pro virtuální počítač s IP adresou 192.168.1.4 a adresou MAC 00-0D-3A-92-6A-7C:

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
> Zdrojová IP adresa pro komunikaci není protokolována. Můžete povolit [protokolování NSG Flow](../network-watcher/network-watcher-nsg-flow-logging-portal.md) pro NSG, který protokoluje všechny informace čítače pravidla a také zdrojovou IP adresu, která iniciovala komunikaci. Data protokolu toku NSG se zapisují do účtu Azure Storage. Data můžete analyzovat pomocí možnosti [Analýza provozu](../network-watcher/traffic-analytics.md) Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Zobrazení a analýza protokolů

Informace o tom, jak zobrazit data protokolu prostředku, najdete v tématu [Přehled protokolů platformy Azure](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud odešlete diagnostická data do:
- **Protokoly Azure monitor**: pro rozšířené přehledy můžete využít řešení pro [analýzu skupin zabezpečení sítě](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) . Řešení poskytuje vizualizace pro pravidla NSG, která povolují nebo zakazují provoz síťového rozhraní ve virtuálním počítači na adrese MAC.
- **Azure Storage účet**: data jsou zapsána do PT1H.jsv souboru. Můžete najít:
  - Protokol událostí v následující cestě: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Protokol čítače pravidla v následující cestě: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolování aktivit](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Protokolování aktivit je ve výchozím nastavení povolené pro skupin zabezpečení sítě vytvořené prostřednictvím modelu nasazení Azure. Pokud chcete zjistit, které operace byly dokončeny v skupin zabezpečení sítě v protokolu aktivit, hledejte položky, které obsahují následující typy prostředků:
  - Microsoft. ClassicNetwork/networkSecurityGroups
  - Microsoft. ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft. Network/networkSecurityGroups
  - Microsoft. Network/networkSecurityGroups/securityRules
- Informace o tom, jak protokolovat diagnostické informace, aby zahrnovaly zdrojovou IP adresu pro každý tok, najdete v tématu [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
