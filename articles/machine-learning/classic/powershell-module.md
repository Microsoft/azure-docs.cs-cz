---
title: 'ML Studio (Classic): moduly PowerShellu – Azure'
description: Pomocí PowerShellu můžete vytvářet a spravovat Azure Machine Learning Studio (klasické) pracovní prostory, experimenty, webové služby a další.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 684299d61ba6e9e27e16a162c9f226a7ea3b5f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518007"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduly PowerShellu pro Azure Machine Learning Studio (klasické)

**platí pro:** ![ Platí pro. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ neplatí pro.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Pomocí modulů prostředí PowerShell můžete programově spravovat prostředky studia (Classic) a prostředky, jako jsou pracovní prostory, datové sady a webové služby.

S prostředky studia (Classic) můžete pracovat pomocí tří modulů PowerShellu:

* [Azure PowerShell AZ](#az-rm) vydaná v 2018 zahrnuje všechny funkce AzureRM, ale s různými názvy rutin.
* [AzureRM](#az-rm) vydaná v 2016, nahrazeno prostředím PowerShell AZ
* [Azure Machine Learning PowerShell Classic](#classic) vydaný v 2016

I když tyto moduly PowerShellu mají nějaké podobnosti, každá je navržena pro konkrétní scénáře. Tento článek popisuje rozdíly mezi moduly PowerShellu a pomůže vám se rozhodnout, které z nich si vybrat.  

Podívejte se na následující [tabulku podpory](#support-table) , kde vidíte, které prostředky jsou podporovány každým modulem. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell AZ a AzureRM

AZ je teď zamýšlený modul PowerShellu pro interakci s Azure a zahrnuje všechny předchozí funkce AzureRM. AzureRM bude i nadále přijímat opravy chyb, ale neobdrží žádné nové rutiny nebo funkce.  AZ a AzureRM spravujte řešení nasazená pomocí modelu nasazení **Azure Resource Manager** . Mezi tyto prostředky patří pracovní prostory studia a aplikace Studio (Classic) "nové" webové služby. 

PowerShell Classic se dá nainstalovat současně buď AZ, nebo AzureRM, aby se pokryly i "nové" i "klasické" typy prostředků. Nedoporučuje se ale mít nainstalovanou AZ a AzureRM ve stejnou dobu. Pro rozhodování mezi AZ a AzureRM společnost Microsoft doporučuje AZ pro všechna budoucí nasazení.  Další informace o AZ AzureRM a cestě k migraci najdete v [úvodu k Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Pokud chcete začít používat AZ, postupujte podle [pokynů pro instalaci Azure AZ](/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell – Classic

[Klasický modul PowerShellu](https://aka.ms/amlps) pro Studio (Classic) umožňuje spravovat prostředky nasazené pomocí **modelu nasazení Classic**. Mezi tyto prostředky patří prostředky pro uživatele studia (Classic), klasické webové služby a klasické koncové body webové služby.

Společnost Microsoft však doporučuje použít model nasazení Správce prostředků pro všechny budoucí prostředky a zjednodušit tak nasazení a správu prostředků. Pokud se chcete dozvědět víc o modelech nasazení, přečtěte si článek [Azure Resource Manager vs. Classic pro nasazení](../../azure-resource-manager/management/deployment-models.md) .

Pokud chcete začít pracovat s prostředím PowerShell Classic, Stáhněte si [balíček pro vydání](https://github.com/hning86/azuremlps/releases) z GitHubu a postupujte podle [pokynů k instalaci](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny vysvětlují, jak odblokovat staženou a dezip knihovnu DLL, a pak ji importovat do prostředí PowerShell.

PowerShell Classic se dá nainstalovat současně buď AZ, nebo AzureRM, aby se pokryly i "nové" i "klasické" typy prostředků.

## <a name="powershell-support-table"></a><a name="support-table"></a> Tabulka podpory PowerShellu


| Úkol | **AZ** |  **PowerShell – Classic** |
| --- | --- | --- |
| Vytváření a odstraňování pracovních prostorů | [Šablony Resource Manageru](./deploy-with-resource-manager-template.md) |  |
| Správa plánů závazku pracovního prostoru | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Správa uživatelů v pracovním prostoru |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Správa webových služeb | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nové" webové služby)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasické" webové služby) |
| Správa koncových bodů a klíčů webové služby |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Správa uživatelských datových sad/poučených modelů| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Správa experimentů uživatelů |  | [Spustit – AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Spravovat vlastní moduly | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Další kroky
Projděte si kompletní dokumentaci k tomuto modulu PowerShellu:
* [PowerShell – Classic](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)