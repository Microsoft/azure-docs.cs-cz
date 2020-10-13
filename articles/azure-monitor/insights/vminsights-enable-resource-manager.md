---
title: Povolení Azure Monitor pro virtuální počítače pomocí šablon Správce prostředků
description: Tento článek popisuje, jak povolíte Azure Monitor pro virtuální počítače pro jeden nebo několik virtuálních počítačů Azure nebo sady škálování virtuálních počítačů pomocí šablon Azure PowerShell nebo Azure Resource Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328164"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Povolení Azure Monitor pro virtuální počítače pomocí šablon Správce prostředků
Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítač nebo sadu škálování virtuálního počítače pomocí šablon Správce prostředků. Tento postup lze použít pro následující:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure

## <a name="prerequisites"></a>Požadavky

- [Vytvořte a nakonfigurujte Log Analytics pracovní prostor](vminsights-configure-workspace.md). 
- V části [podporované operační systémy](vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je podporovaný operační systém virtuálního počítače nebo sady škálování virtuálních počítačů, které chcete povolit. 

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Vytvořili jsme příklady Azure Resource Manager šablon pro připojování virtuálních počítačů a sady škálování virtuálních počítačů. Tyto šablony obsahují scénáře, pomocí kterých můžete povolit monitorování stávajícího prostředku a vytvořit nový prostředek, který má povolené monitorování.

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků, ve které je virtuální počítač nebo sada škálování virtuálního počítače povolená.


Šablony Azure Resource Manager jsou k dispozici v souboru archivu (. zip), který si můžete [Stáhnout](https://aka.ms/VmInsightsARMTemplates) z našeho úložiště GitHub. Obsah souboru zahrnuje složky, které reprezentují jednotlivé scénáře nasazení, pomocí šablony a souboru parametrů. Než je spustíte, upravte soubor parametrů a zadejte požadované hodnoty. 

Soubor ke stažení obsahuje následující šablony pro různé scénáře:

- Šablona **ExistingVmOnboarding** povoluje Azure monitor pro virtuální počítače, pokud virtuální počítač už existuje.
- Šablona **NewVmOnboarding** vytvoří virtuální počítač a povolí Azure monitor pro virtuální počítače k jeho monitorování.
- Šablona **ExistingVmssOnboarding** povoluje Azure monitor pro virtuální počítače, pokud už existuje sada škálování virtuálního počítače.
- **NewVmssOnboarding** Template vytvoří virtuální počítač Scale Sets a povolí Azure monitor pro virtuální počítače k jejich monitorování.
- Šablona **ConfigureWorkspace** konfiguruje pracovní prostor Log Analytics tak, aby podporoval Azure monitor pro virtuální počítače tím, že umožňuje řešení a shromažďování čítačů výkonu operačního systému Linux a Windows.

>[!NOTE]
>Pokud se sada škálování virtuálních počítačů už nastavila a zásada upgradu je nastavená na **Ruční**, Azure monitor pro virtuální počítače po spuštění šablony Azure Resource Manager **ExistingVmssOnboarding** nebude ve výchozím nastavení povolená. Je nutné ručně upgradovat instance.

## <a name="deploy-templates"></a>Nasazení šablon
Šablony lze nasadit pomocí [libovolné metody nasazení pro správce prostředků šablony](../../azure-resource-manager/templates/deploy-powershell.md) , včetně následujících příkladů pomocí PowerShellu a rozhraní příkazového řádku.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Další kroky

Teď, když je monitorování povolené pro vaše virtuální počítače, jsou tyto informace k dispozici pro analýzu pomocí Azure Monitor pro virtuální počítače.

- Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení Azure monitor pro virtuální počítače mapa](vminsights-maps.md).

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).
