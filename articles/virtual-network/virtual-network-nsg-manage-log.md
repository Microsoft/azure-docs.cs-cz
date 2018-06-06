---
title: Událost skupiny zabezpečení sítě a pravidlo čítače Azure diagnostické protokoly | Microsoft Docs
description: Informace o povolení událostí a pravidlo čítače diagnostické protokoly pro skupinu zabezpečení sítě Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757055"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Protokolování diagnostiky pro skupinu zabezpečení sítě

Skupina zabezpečení sítě (NSG) obsahuje pravidla, která povolí nebo zakážou provoz na podsíti virtuální sítě, síťové rozhraní nebo obojí. Pokud povolíte protokolování pro skupinu NSG diagnostiky, můžete protokolovat následující kategorie informací:

* **Události:** položky se zaznamenávají, pro které skupina NSG pravidla se používají pro virtuální počítače, na základě adresy MAC. Stav pro tato pravidla se shromažďují každých 60 sekund.
* **Čítač pravidel:** obsahuje položky pro jednotlivé skupiny NSG počet použití pravidla se aplikuje zakázat nebo povolit provoz.

Diagnostické protokoly jsou dostupné pouze pro skupiny Nsg nasazené prostřednictvím modelu nasazení Azure Resource Manager. Nelze povolit protokolování diagnostiky pro skupiny Nsg nasazené prostřednictvím modelu nasazení classic. Lépe pochopit dva modely, najdete v části [modelech nasazení Azure Principy](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostické je povoleno protokolování samostatně *každý* NSG, které chcete shromažďovat diagnostická data pro. Pokud vás zajímá provozní, nebo místo toho protokoly aktivity, najdete v tématu Azure [protokolování aktivit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Povolit protokolování

Můžete použít [portálu Azure](#azure-portal), nebo [prostředí PowerShell](#powershell), chcete-li povolit protokolování diagnostiky.

### <a name="azure-portal"></a>Azure Portal

1. Přihlaste se k [portál](https://portal.azure.com).
2. Vyberte **všechny služby**, pak zadejte *skupin zabezpečení sítě*. Když **skupin zabezpečení sítě** nezobrazí ve výsledcích hledání, vyberte ho.
3. Vyberte NSG, které chcete povolit protokolování.
4. V části **monitorování**, vyberte **protokolů diagnostiky**a potom vyberte **zapněte diagnostiku**, jak je znázorněno na následujícím obrázku:
 
    ![Zapnout diagnostiku](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. V části **nastavení diagnostiky**, zadejte, nebo vyberte následující informace a potom vyberte **Uložit**:

    | Nastavení                                                                                     | Hodnota                                                          |
    | ---------                                                                                   |---------                                                       |
    | Název                                                                                        | Název vašeho výběru.  Příklad: *myNsgDiagnostics*      |
    | **Archiv na účet úložiště**, **datový proud do centra událostí**, a **poslat analýzy protokolů** | Můžete vybrat libovolný počet cílů a zvolte. Další informace o jednotlivých najdete v tématu [protokolu cíle](#log-destinations).                                                                                                                                           |
    | PROTOKOLU                                                                                         | Vyberte jednoho nebo obou protokolů kategorie. Další informace o data protokolovaná pro každou kategorii najdete v tématu [protokolu kategorie](#log-categories).                                                                                                                                             |
6. Zobrazení a analýza protokolů. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Můžete spouštět příkazy, které následují v [prostředí cloudu Azure](https://shell.azure.com/powershell), nebo pomocí spouštění prostředí PowerShell z vašeho počítače. Prostředí cloudové služby Azure je interaktivní prostředí volné. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Pokud spustíte prostředí PowerShell z vašeho počítače, musíte *AzureRM* modul prostředí PowerShell, verze 6.1.1 nebo novější. Spustit `Get-Module -ListAvailable AzureRM` na počítači s nainstalovanou verzi. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, musíte také spustit `Login-AzureRmAccount` k přihlášení do Azure pomocí účtu, který má [potřebná oprávnění](virtual-network-network-interface.md#permissions)].

Chcete-li povolit protokolování diagnostiky, je třeba Id existující skupiny NSG. Pokud nemáte existující skupina NSG, můžete vytvořit s [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Načíst skupinu zabezpečení sítě, který chcete povolit protokolování pro s diagnostiky [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Například pokud chcete načíst skupinu NSG s názvem *myNsg* které existuje ve skupině prostředků s názvem *myResourceGroup*, zadejte následující příkaz:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Diagnostické protokoly můžete zapisovat do tři typy cílový. Další informace najdete v tématu [protokolu cíle](#log-destinations). V tomto článku se posílá protokoly *analýzy protokolů* cíl, jako příklad. Načtení existujícímu pracovnímu prostoru analýzy protokolů s [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Například k načtení existujícímu pracovnímu prostoru s názvem *myLaWorkspace* ve skupině prostředků s názvem *LaWorkspaces*, zadejte následující příkaz:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Pokud nemáte existujícímu pracovnímu prostoru, můžete vytvořit s [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Existují dvě kategorie protokolování, které můžete povolit v protokolech. Další informace najdete v tématu [protokolu kategorie](#log-categories). Povolit protokolování diagnostiky skupiny nsg s [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). Následující příklad protokolů událostí a čítač kategorie dat do pracovního prostoru pro skupinu NSG pomocí ID pro NSG a pracovní prostor, který jste získali dříve:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Pokud chcete protokolovat data pro jednu kategorii nebo dalších, nikoli obě, přidejte `-Categories` možnost předchozí příkaz, za nímž následuje *NetworkSecurityGroupEvent* nebo *NetworkSecurityGroupRuleCounter*. Pokud chcete k přihlášení na jiný [cílové](#log-destinations) než pracovní prostor analýzy protokolů, použijte příslušné parametry Azure [účet úložiště](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [centra událostí](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Zobrazení a analýza protokolů. Další informace najdete v tématu [zobrazení a analýza protokolů](#view-and-analyze-logs).

## <a name="log-destinations"></a>Cíle protokolu

Diagnostická data může být:
- [Zapisovat do účtu Azure Storage](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), pro auditování nebo ruční kontroly. Můžete zadat dobu uchování (ve dnech), používá nastavení diagnostiky pro prostředek.
- [Streamování do centra událostí](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.
- [Zapisovat do Azure Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Kategorie protokolu

Data ve formátu JSON je napsán pro následujících kategorií protokolu:

### <a name="event"></a>Událost

Protokol událostí obsahuje informace o tom, které se použijí pravidla NSG na virtuálních počítačích, na základě adresy MAC. Pro všechny události se protokolují tato data příklad:

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
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Čítač pravidel

Protokol čítač pravidlo obsahuje informace o každé pravidlo použito pro prostředky. Pokaždé, když se použije pravidlo se protokolují tato data příklad:

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
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Zdrojová adresa IP pro komunikaci se neprotokolují. Můžete povolit [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) pro skupinu NSG ale který zaznamená všechny informace o pravidle čítače, jakož i zdrojovou IP adresu, která iniciovala komunikace. Data protokolu toku NSG se zapisují do účtu Azure Storage. Data s můžete analyzovat [provozu analytics](../network-watcher/traffic-analytics.md) schopností sledovací proces sítě Azure.

## <a name="view-and-analyze-logs"></a>Zobrazení a analýza protokolů

Další postupy k zobrazení protokolů diagnostiky dat najdete v tématu [diagnostických protokolů Azure přehled](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pokud budete odesílat data diagnostiky:
- **Protokolu analýzy**: můžete použít [analytics skupiny zabezpečení sítě](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) řešení pro rozšířené statistiky. Řešení poskytuje vizualizace pro pravidla NSG, které povolí nebo zakážou provoz na adresu MAC síťového rozhraní na virtuálním počítači.
- **Účet služby Azure Storage**: Data se zapisují do souboru PT1H.json. Můžete najít:
    - Protokol událostí v následující cestě: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Pravidlo protokolu čítačů v následující cestě: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Další postup

- Další informace o [protokolování aktivit](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), dříve označovaný jako auditování nebo provozní protokoly. Ve výchozím nastavení je u skupiny Nsg vytvořena prostřednictvím buď modelu nasazení Azure povoleno protokolování aktivity. Pokud chcete zjistit, které operace byly dokončeny podle skupin Nsg v protokolu aktivit, vyhledejte položky, které obsahují následující typy prostředků:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Další informace o protokolu diagnostické informace zahrnují zdrojovou IP adresu pro každý tok, najdete v části [protokolování toku NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).