---
title: Diagnostické protokolování pro skupinu zabezpečení sítě
titlesuffix: Azure Virtual Network
description: Zjistěte, jak povolit diagnostické protokoly čítačů událostí a pravidel pro skupinu zabezpečení sítě Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751180"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Diagnostické protokolování pro skupinu zabezpečení sítě

Skupina zabezpečení sítě (NSG) obsahuje pravidla, která povolují nebo odepírají provoz do podsítě virtuální sítě, síťového rozhraní nebo obojího. Pokud povolíte protokolování diagnostiky pro skupinu nsg, můžete protokolovat následující kategorie informací:

* **Událost:** Položky jsou protokolovány, pro které jsou pravidla nsg použita pro virtuální počítače, na základě adresy MAC.
* **Čítač pravidel:** Obsahuje položky, kolikrát je použito každé pravidlo skupiny nSG pro odepření nebo povolení provozu. Stav těchto pravidel se shromažďuje každých 60 sekund.

Diagnostické protokoly jsou k dispozici jenom pro skupiny zabezpečení sítě nasazené prostřednictvím modelu nasazení Azure Resource Manager. Nelze povolit protokolování diagnostiky pro sítě sítě sítě nasazené prostřednictvím klasického modelu nasazení. Pro lepší pochopení těchto dvou modelů, [najdete v tématu principy modelů nasazení Azure](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostické protokolování je povoleno samostatně pro *každý* soubor zabezpečení sítě, pro který chcete shromažďovat diagnostická data. Pokud máte zájem o provozní nebo aktivity, protokoly místo toho, najdete v tématu [protokolování aktivit](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)y Azure .

## <a name="enable-logging"></a>Povolit protokolování

K povolení diagnostického protokolování můžete použít [portál Azure Portal](#azure-portal), [PowerShell](#powershell)nebo [azure cli.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portálu](https://portal.azure.com).
2. Vyberte **Všechny služby**a zadejte *skupiny zabezpečení sítě*. Pokud se ve výsledcích hledání zobrazí **skupiny zabezpečení sítě,** vyberte je.
3. Vyberte soubor nsg, pro který chcete protokolování povolit.
4. V části **MONITORING**vyberte **Diagnostické protokoly**a pak vyberte **Zapnout diagnostiku**, jak je znázorněno na následujícím obrázku:

   ![Zapnout diagnostiku](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. V části **Nastavení diagnostiky**zadejte nebo vyberte následující informace a pak vyberte **Uložit**:

    | Nastavení                                                                                     | Hodnota                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name (Název)                                                                                        | Jméno, které si vyberete.  Například: *myNsgDiagnostics*      |
    | **Archivujte do účtu úložiště**, **streamujte do centra událostí**a **odešlete do analýzy protokolů** | Můžete vybrat libovolný počet cílů. Další informace o jednotlivých položky naleznete v [tématu Protokolování cílů](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Vyberte jednu nebo obě kategorie protokolu. Další informace o datech protokolovaných pro jednotlivé kategorie naleznete v [tématu Kategorie protokolů](#log-categories).                                                                                                                                             |
6. Zobrazení a analýza protokolů. Další informace naleznete v tématu [Zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/powershell), nebo spuštěním Prostředí PowerShell z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte PowerShell z počítače, budete potřebovat modul Azure PowerShell, verze 1.0.0 nebo novější. Spuštěním `Get-Module -ListAvailable Az` v počítači vyhledejte nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, musíte se `Connect-AzAccount` taky spustit a přihlásit se k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Chcete-li povolit protokolování diagnostiky, potřebujete ID existujícího souboru nsg. Pokud nemáte existující skupinu zabezpečení sítě, můžete ji vytvořit pomocí [skupiny New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Načtěte skupinu zabezpečení sítě, pro kterou chcete povolit protokolování diagnostiky pomocí [skupiny Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Chcete-li například načíst skupinu nsg s názvem *myNsg,* která existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Diagnostické protokoly můžete zapsat do tří cílových typů. Další informace naleznete v [tématu Log destinations](#log-destinations). V tomto článku protokoly jsou odesílány do cíle *Log Analytics,* jako příklad. Načtěte existující pracovní prostor Log Analytics pomocí [get-azoperationalinsightsworkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Chcete-li například načíst existující pracovní prostor s názvem *myWorkspace* ve skupině prostředků s názvem *myWorkspaces*, zadejte následující příkaz:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Pokud nemáte existující pracovní prostor, můžete jej vytvořit pomocí [aplikace New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Protokoly můžete povolit dvěma kategoriemi protokolů. Další informace naleznete v [tématu Log categories](#log-categories). Povolte protokolování diagnostiky pro soubor NSG pomocí [nastavení Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Následující příklad protokoluje data kategorie událostí i čítačů do pracovního prostoru pro skupinu zabezpečení zabezpečení zabezpečení, pomocí ID pro skupinu zabezpečení zabezpečení a pracovní prostor, který jste dříve načetli:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Pokud chcete protokolovat data pouze pro jednu kategorii nebo jinou, nikoli pro obě, přidejte `-Categories` možnost k předchozímu příkazu následovanému *networksecuritygroupevent* nebo *networksecuritygrouprule*. Pokud se chcete přihlásit k jinému [cíli](#log-destinations) než do pracovního prostoru Log Analytics, použijte příslušné parametry pro [účet Úložiště](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure nebo [Centrum událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zobrazení a analýza protokolů. Další informace naleznete v tématu [Zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním Azure CLI z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte cli z počítače, budete potřebovat verzi 2.0.38 nebo novější. Spuštěním `az --version` v počítači vyhledejte nainstalovanou verzi. Pokud potřebujete upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Pokud používáte příkazové příkazové příkazy místně, musíte také spustit `az login` pro přihlášení k Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions).

Chcete-li povolit protokolování diagnostiky, potřebujete ID existujícího souboru nsg. Pokud nemáte existující soubor zabezpečení sítě, můžete jej vytvořit pomocí [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create).

Načtěte skupinu zabezpečení sítě, pro kterou chcete povolit diagnostické protokolování pomocí [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show). Chcete-li například načíst skupinu nsg s názvem *myNsg,* která existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Diagnostické protokoly můžete zapsat do tří cílových typů. Další informace naleznete v [tématu Log destinations](#log-destinations). V tomto článku protokoly jsou odesílány do cíle *Log Analytics,* jako příklad. Další informace naleznete v [tématu Log categories](#log-categories).

Povolte protokolování diagnostiky pro soubor NSG pomocí [vytvoření nastavení diagnostiky monitorování az](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). Následující příklad protokoluje data kategorie událostí i čítačů do existujícího pracovního prostoru s názvem *myWorkspace*, který existuje ve skupině prostředků s názvem *myWorkspaces*, a ID skupiny zabezpečení, kterou jste dříve načetli:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Pokud nemáte existující pracovní prostor, můžete ho vytvořit pomocí [portálu Azure nebo](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShellu](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Protokoly můžete povolit dvěma kategoriemi protokolů.

Pokud chcete protokolovat data pouze pro jednu nebo druhou kategorii, odeberte kategorii, pro kterou nechcete protokolovat data v předchozím příkazu. Pokud se chcete přihlásit k jinému [cíli](#log-destinations) než do pracovního prostoru Log Analytics, použijte příslušné parametry pro [účet Úložiště](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure nebo [Centrum událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zobrazení a analýza protokolů. Další informace naleznete v tématu [Zobrazení a analýza protokolů](#view-and-analyze-logs).

## <a name="log-destinations"></a>Protokolovat cíle

Diagnostická data mohou být:
- [Zapsaná na účet úložiště Azure](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), pro auditování nebo ruční kontrolu. Můžete určit čas uchování (ve dnech) pomocí nastavení diagnostiky prostředků.
- [Streamované do centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro příjem službou třetí strany nebo vlastní analytické řešení, jako je PowerBI.
- [Zapsaná do protokolů Azure Monitor .](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="log-categories"></a>Kategorie protokolů

Data formátovaná společností JSON jsou zapisována pro následující kategorie protokolů:

### <a name="event"></a>Událost

Protokol událostí obsahuje informace o tom, která pravidla skupiny nsg se použijí na virtuální počítače, na základě adresy MAC. Pro každou událost jsou zaznamenána následující data. V následujícím příkladu jsou data zaznamenána pro virtuální počítač s IP adresou 192.168.1.4 a MAC adresou 00-0D-3A-6A-7C:

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

### <a name="rule-counter"></a>Čítač pravidel

Protokol čítačů pravidel obsahuje informace o každém pravidle použitém pro prostředky. Následující ukázková data jsou protokolována při každém použití pravidla. V následujícím příkladu jsou data zaznamenána pro virtuální počítač s IP adresou 192.168.1.4 a MAC adresou 00-0D-3A-6A-7C:

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
> Zdrojová ADRESA IP pro komunikaci není zaznamenána. Můžete však povolit [protokolování toku nsg](../network-watcher/network-watcher-nsg-flow-logging-portal.md) pro soubor nsg, který protokoluje všechny informace čítače pravidel, stejně jako zdrojovou ADRESU IP, která iniciovala komunikaci. Data protokolu toku NSG se zapisují do účtu Azure Storage. Data můžete analyzovat pomocí funkce [analýzy provozu služby](../network-watcher/traffic-analytics.md) Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Zobrazení a analýza protokolů

Informace o tom, jak zobrazit diagnostická data protokolu, najdete v [tématu přehled diagnostických protokolů Azure](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud odesíláte diagnostická data do:
- **Protokoly Azure Monitor**: Pro lepší přehledy můžete použít [řešení analýzy skupiny zabezpečení sítě.](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) Řešení poskytuje vizualizace pro pravidla sítě zabezpečení sítě, která povolují nebo zapírají provoz síťového rozhraní ve virtuálním počítači na jedno MAC.
- **Účet Azure Storage**: Data se zapisují do souboru PT1H.json. Můžete najít:
  - Protokol událostí v následující cestě:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Protokol čítače pravidel v následující cestě:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [protokolování aktivit](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), dříve označované jako protokoly auditu nebo provozní protokoly. Protokolování aktivit je ve výchozím nastavení povoleno pro sítě zabezpečení sítě vytvořené buď pomocí modelu nasazení Azure. Chcete-li zjistit, které operace byly dokončeny na skupinách nsg v protokolu aktivit, vyhledejte položky, které obsahují následující typy prostředků:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Informace o protokolování diagnostických informací, včetně zdrojové adresy IP pro každý tok, naleznete v [tématu protokolování toku nsg](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
