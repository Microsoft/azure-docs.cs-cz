---
title: Správa rozšíření virtuálních počítačů pomocí serverů s podporou ARC Azure
description: Servery s podporou ARC Azure můžou spravovat nasazení rozšíření virtuálních počítačů, která poskytují konfiguraci po nasazení a úlohy automatizace s virtuálními počítači mimo Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: e9865761fd3e5897ee3f01cd3d6ca620d5ea2f4b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460882"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>Správa rozšíření virtuálních počítačů pomocí serverů s podporou ARC Azure

Rozšíření virtuálních počítačů jsou malé aplikace, které poskytují konfiguraci po nasazení a úlohy automatizace na virtuálních počítačích Azure. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače.

Servery s podporou ARC Azure vám umožňují nasadit rozšíření virtuálních počítačů Azure do virtuálních počítačů s jiným než Azure a Linux, což zjednodušuje správu hybridního počítače v místním prostředí, hraničních zařízeních a dalších cloudových prostředích v životním cyklu. Rozšíření virtuálních počítačů je možné spravovat pomocí následujících metod na hybridních počítačích nebo serverech spravovaných servery s podporou ARC:

- [Azure Portal](manage-vm-extensions-portal.md)
- Rozhraní příkazového [řádku Azure](manage-vm-extensions-cli.md)
- [Azure PowerShell](manage-vm-extensions-powershell.md)
- [Šablony Azure správce prostředků](manage-vm-extensions-template.md)

## <a name="key-benefits"></a>Klíčové výhody

Podpora rozšíření virtuálních počítačů serverů s podporou ARC Azure nabízí následující klíčové výhody:

- [Konfigurace stavu Azure Automation](../../automation/automation-dsc-overview.md) slouží k centrálnímu ukládání konfigurací a údržbě požadovaného stavu hybridních připojených počítačů povolených prostřednictvím rozšíření virtuálního počítače DSC.

- Shromažďovat data protokolu pro analýzu s [protokoly v Azure monitor](../../azure-monitor/platform/data-platform-logs.md) povolené prostřednictvím rozšíření virtuálního počítače agenta Log Analytics. To je užitečné při provádění složitých analýz napříč daty z nejrůznějších zdrojů.

- Díky [Azure monitor pro virtuální počítače](../../azure-monitor/insights/vminsights-overview.md)analyzuje výkon virtuálních počítačů s Windows a Linux a monitoruje jejich procesy a závislosti na dalších prostředcích a externích procesech. Toho dosáhnete tím, že povolíte rozšíření Log Analytics agenta a rozšíření virtuálního počítače agenta závislostí.

- Stahovat a spouštět skripty na hybridních připojených počítačích pomocí rozšíření vlastních skriptů. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy.

## <a name="availability"></a>Dostupnost

Funkce rozšíření virtuálních počítačů jsou k dispozici pouze v seznamu [podporovaných oblastí](overview.md#supported-regions). Ujistěte se, že jste počítač připojili do jedné z těchto oblastí.

## <a name="extensions"></a>Rozšíření

V této verzi podporujeme následující rozšíření virtuálních počítačů na počítačích s Windows a Linux.

|Rozšíření |Operační systém |Publisher |Další informace |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Rozšíření vlastních skriptů pro Windows](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Rozšíření Windows PowerShell DSC](../../virtual-machines/extensions/dsc-windows.md)|
|Agent Log Analytics |Windows |Microsoft. EnterpriseCloud. Monitoring |[Log Analytics rozšíření virtuálního počítače pro Windows](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft Dependency Agent | Windows |Microsoft.Compute | [Rozšíření pro virtuální počítače s agentem závislosti pro Windows](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure. Extension |[Rozšíření vlastních skriptů pro Linux verze 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft. OSTCExtensions |[Rozšíření PowerShell DSC pro Linux](../../virtual-machines/extensions/dsc-linux.md) |
|Agent Log Analytics |Linux |Microsoft. EnterpriseCloud. Monitoring |[Rozšíření virtuálního počítače s Log Analytics pro Linux](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft Dependency Agent | Linux |Microsoft.Compute | [Rozšíření pro virtuální počítače s agentem závislosti pro Linux](../../virtual-machines/extensions/agent-dependency-linux.md) |

Další informace o balíčku agenta připojeného počítače Azure a podrobnostech o komponentě agenta rozšíření najdete v tématu [Přehled agenta](agent-overview.md#agent-component-details).

## <a name="prerequisites"></a>Požadavky

Tato funkce závisí na následujících poskytovatelích prostředků Azure v rámci vašeho předplatného:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Pokud ještě nejsou zaregistrované, postupujte podle kroků v části [registrace poskytovatelů prostředků Azure](agent-overview.md#register-azure-resource-providers).

Rozšíření virtuálního počítače agenta Log Analytics pro Linux vyžaduje Python 2. x na cílovém počítači.

### <a name="connected-machine-agent"></a>Agent připojeného počítače

Ověřte, že počítač odpovídá [podporovaným verzím](agent-overview.md#supported-operating-systems) operačního systému Windows a Linux pro agenta připojeného počítače Azure.

Minimální verze agenta připojeného počítače, který je podporovaný touto funkcí v systému Windows a Linux, je verze 1,0.

Pokud chcete upgradovat počítač na požadovanou verzi agenta, přečtěte si téma [Upgrade agenta](manage-agent.md#upgrading-agent).

## <a name="next-steps"></a>Další kroky

Rozšíření virtuálních počítačů můžete nasadit, spravovat a odebírat pomocí [Azure CLI](manage-vm-extensions-cli.md), [PowerShellu](manage-vm-extensions-powershell.md), z [Azure Portal](manage-vm-extensions-portal.md)nebo [šablon Azure Resource Manager](manage-vm-extensions-template.md).
