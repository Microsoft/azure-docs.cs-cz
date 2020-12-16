---
title: Instalace agenta Azure Monitor
description: Možnosti pro instalaci agenta Azure Monitor (AMA) na virtuální počítače Azure a servery s podporou ARC Azure
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 893a04f5acd48cf1e6f34033c06a758492e70abf
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516714"
---
# <a name="install-the-azure-monitor-agent-preview"></a>Instalace agenta Azure Monitor (Preview)
Tento článek poskytuje různé možnosti, které jsou aktuálně k dispozici pro instalaci [agenta Azure monitor](azure-monitor-agent-overview.md) na virtuálních počítačích Azure i serverech s podporou ARC Azure a také možnosti vytváření [přidružení s pravidly shromažďování dat](data-collection-rule-azure-monitor-agent.md) , která určují, která data má agent shromažďovat.

## <a name="prerequisites"></a>Předpoklady
Před instalací agenta Azure Monitor se vyžaduje splnění následujících požadavků.

- Na virtuálních počítačích Azure musí být povolená [Identita spravovaného systému](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) . To není vyžadováno pro servery s podporou ARC Azure. Identita systému bude povolena automaticky, pokud je agent nainstalován jako součást procesu pro [Vytvoření a přiřazení pravidla shromažďování dat pomocí Azure Portal](#install-with-azure-portal).
- Ve virtuální síti pro virtuální počítač musí být povolená [značka služby AzureResourceManager](../../virtual-network/service-tags-overview.md) .

## <a name="virtual-machine-extension-details"></a>Podrobnosti o rozšíření virtuálního počítače
Agent Azure Monitor se implementuje jako [rozšíření virtuálního počítače Azure](../../virtual-machines/extensions/overview.md) s podrobnostmi v následující tabulce. Dá se nainstalovat pomocí kterékoli z metod instalace rozšíření virtuálních počítačů, včetně těch, které jsou popsané v tomto článku.

| Vlastnost | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft. Azure. monitor  | Microsoft. Azure. monitor |
| Typ      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Instalace pomocí Azure Portal
Chcete-li nainstalovat agenta Azure Monitor pomocí Azure Portal, postupujte podle kroků v části [Vytvoření pravidla shromažďování dat](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) v Azure Portal. To vám umožní přidružit pravidlo shromažďování dat k jednomu nebo několika virtuálním počítačům Azure nebo serverům s povoleným obloukem Azure. Agent se nainstaluje na některý z těchto virtuálních počítačů, které ještě nemají.


## <a name="install-with-resource-manager-template"></a>Instalace pomocí šablony Správce prostředků
Pomocí Správce prostředků šablon můžete nainstalovat agenta Azure Monitor na virtuálních počítačích Azure a na serverech s podporou ARC Azure a vytvořit přidružení s pravidly shromažďování dat. Před vytvořením přidružení je nutné vytvořit jakékoli pravidlo shromažďování dat.

Získejte ukázkové šablony pro instalaci agenta a vytvoření asociace z následujících možností: 

- [Šablona pro instalaci agenta Azure Monitor (Azure a ARC Azure)](../samples/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Šablona pro vytvoření přidružení s pravidlem shromažďování dat](../samples/resource-manager-data-collection-rules.md)

Nainstalujte šablony pomocí [libovolné metody nasazení pro správce prostředků šablony](../../azure-resource-manager/templates/deploy-powershell.md) , například následující příkazy.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Instalace pomocí PowerShellu
Agenta Azure Monitor můžete nainstalovat na virtuální počítače Azure a na servery s podporou ARC Azure pomocí příkazu PowerShellu pro přidání rozšíření virtuálního počítače. 

### <a name="azure-virtual-machines"></a>Virtuální počítače Azure
Pomocí následujících příkazů PowerShellu nainstalujte agenta Azure Monitor na virtuálních počítačích Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location>
```
---

### <a name="azure-arc-enabled-servers"></a>Servery s podporou služby Azure Arc
Pomocí následujících příkazů PowerShellu nainstalujte servery služby Azure Monitor Agent na Azure ARC s podporou.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
Agenta Azure Monitor můžete nainstalovat na virtuální počítače Azure a na servery s podporou ARC Azure pomocí příkazu rozhraní příkazového řádku Azure pro přidání rozšíření virtuálního počítače. 

### <a name="azure-virtual-machines"></a>Virtuální počítače Azure
Pomocí následujících příkazů rozhraní příkazového řádku nainstalujte agenta Azure Monitor na virtuální počítače Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Servery s podporou služby Azure Arc
Pomocí následujících příkazů rozhraní příkazového řádku můžete nainstalovat servery služby Azure Monitor Agent na Azure ARC s podporou.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Další kroky

- [Vytvořte pravidlo shromažďování dat](data-collection-rule-azure-monitor-agent.md) pro shromažďování dat z agenta a jeho odeslání na Azure monitor.
