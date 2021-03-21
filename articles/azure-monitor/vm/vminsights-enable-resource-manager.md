---
title: Povolení přehledů virtuálních počítačů pomocí šablon Správce prostředků
description: Tento článek popisuje, jak povolit analytické informace pro jeden nebo několik virtuálních počítačů Azure nebo služby Virtual Machine Scale Sets pomocí šablon Azure PowerShell nebo Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031856"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Povolení přehledů virtuálních počítačů pomocí šablon Správce prostředků
Tento článek popisuje, jak povolit přehledy virtuálních počítačů pro virtuální počítač nebo sadu škálování virtuálních počítačů pomocí Správce prostředků šablon. Tento postup lze použít pro následující:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure

## <a name="prerequisites"></a>Předpoklady

- [Vytvořte a nakonfigurujte Log Analytics pracovní prostor](./vminsights-configure-workspace.md). 
- V části [podporované operační systémy](./vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je podporovaný operační systém virtuálního počítače nebo sady škálování virtuálních počítačů, které chcete povolit. 

## <a name="resource-manager-templates"></a>Šablony Resource Manageru

Vytvořili jsme příklady Azure Resource Manager šablon pro připojování virtuálních počítačů a sady škálování virtuálních počítačů. Tyto šablony obsahují scénáře, pomocí kterých můžete povolit monitorování stávajícího prostředku a vytvořit nový prostředek, který má povolené monitorování.

>[!NOTE]
>Šablona musí být nasazená ve stejné skupině prostředků, ve které je virtuální počítač nebo sada škálování virtuálního počítače povolená.


Šablony Azure Resource Manager jsou k dispozici v souboru archivu (. zip), který si můžete [Stáhnout](https://aka.ms/VmInsightsARMTemplates) z našeho úložiště GitHub. Obsah souboru zahrnuje složky, které reprezentují jednotlivé scénáře nasazení, pomocí šablony a souboru parametrů. Než je spustíte, upravte soubor parametrů a zadejte požadované hodnoty. 

Soubor ke stažení obsahuje následující šablony pro různé scénáře:

- Šablona **ExistingVmOnboarding** umožňuje virtuálnímu počítači přehledy virtuálních počítačů, pokud už existuje.
- **NewVmOnboarding** Template vytvoří virtuální počítač, který umožňuje přehledy pro monitorování virtuálního počítače.
- **ExistingVmssOnboarding** šablona povoluje přehledy virtuálních počítačů, pokud už existuje sada škálování virtuálního počítače.
- **NewVmssOnboarding** šablona vytvoří službu Virtual Machine Scale Sets, která umožňuje přehledy monitorování.
- Šablona **ConfigureWorkspace** konfiguruje váš pracovní prostor Log Analytics pro podporu přehledů virtuálních počítačů tím, že umožňuje řešení a shromažďování čítačů výkonu operačního systému Linux a Windows.

>[!NOTE]
>Pokud jste už sadu škálování virtuálních počítačů nastavili a zásada upgradu je nastavená na **Ruční**, služba VM Insights po spuštění šablony **ExistingVmssOnboarding** Azure Resource Manager nebude ve výchozím nastavení povolená pro instance. Je nutné ručně upgradovat instance.

## <a name="deploy-templates"></a>Nasazení šablon
Šablony lze nasadit pomocí [libovolné metody nasazení pro správce prostředků šablony](../../azure-resource-manager/templates/deploy-powershell.md) , včetně následujících příkladů pomocí PowerShellu a rozhraní příkazového řádku.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Další kroky

Teď, když je monitorování pro vaše virtuální počítače povolené, jsou tyto informace k dispozici pro analýzu pomocí služby VM Insights.

- Pokud si chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [zobrazení mapy virtuálních počítačů](vminsights-maps.md).

- Pokud chcete zjistit kritické body a celkové využití výkonu vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).