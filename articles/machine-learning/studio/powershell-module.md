---
title: Moduly PowerShellu
titleSuffix: ML Studio (classic) - Azure
description: Pomocí PowerShellu můžete vytvářet a spravovat pracovní prostory, experimenty, webové služby azure machine learningového studia (klasické) a další.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 6afd222730a9864e0b8edd681b1ce919b03c3be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204286"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Moduly PowerShellu pro Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Pomocí modulů Prostředí PowerShell můžete programově spravovat prostředky a prostředky studia (klasické), jako jsou pracovní prostory, datové sady a webové služby.

Se zdroji Studia (klasické) můžete pracovat pomocí tří modulů Powershellu:

* [Azure PowerShell Az](#az-rm) vydané v roce 2018, zahrnuje všechny funkce AzureRM, i když s různými názvy rutiny
* [AzureRM](#az-rm) vydané v roce 2016, nahrazeno PowerShell Az
* [Azure Machine Learning PowerShell classic](#classic) vydaný v roce 2016

Přestože tyto moduly prostředí PowerShell mají některé podobnosti, každý je určen pro konkrétní scénáře. Tento článek popisuje rozdíly mezi moduly Prostředí PowerShell a pomůže vám rozhodnout, které z nich zvolit.  

V [následující tabulce podpory](#support-table) zkontrolujte, které prostředky jsou podporovány jednotlivými moduly. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a>Azure PowerShell Az a AzureRM

Az je teď zamýšlený modul PowerShellu pro interakci s Azure a zahrnuje všechny předchozí funkce AzureRM. AzureRM bude nadále přijímat opravy chyb, ale neobdrží žádné nové rutiny nebo funkce.  Az i AzureRM spravují řešení nasazená pomocí modelu nasazení **Azure Resource Manageru.** Mezi tyto zdroje patří pracovní prostory studia (klasické) a webové služby Studio (klasické) "Nové". 

PowerShell classic lze nainstalovat vedle Az nebo AzureRM tak, aby pokrývaly typy "nových" i "klasických" prostředků. Však se nedoporučuje mít Az a AzureRM nainstalovány ve stejnou dobu. Chcete-li se rozhodnout mezi Az a AzureRM, Microsoft doporučuje Az pro všechna budoucí nasazení.  Další informace o Az versus AzureRM a cestě migrace v [úvodu k Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Chcete-li začít s Az, postupujte [podle pokynů pro instalaci pro Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="powershell-classic"></a><a name="classic"></a>PowerShell klasické

Klasický [modul prostředí PowerShell](https://aka.ms/amlps) umožňuje spravovat prostředky nasazené pomocí **klasického modelu nasazení**. Tyto prostředky zahrnují studio (klasické) uživatelské prostředky, "klasické" webové služby a "klasické" koncové body webové služby.

Společnost Microsoft však doporučuje použít model nasazení Resource Manager pro všechny budoucí prostředky ke zjednodušení nasazení a správy prostředků. Pokud se chcete dozvědět více o modelech nasazení, přečtěte si článek [Azure Resource Manager vs. klasické nasazení.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)

Chcete-li začít s PowerShell classic, stáhněte si [balíček vydání](https://github.com/hning86/azuremlps/releases) z GitHubu a postupujte [podle pokynů pro instalaci](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny vysvětlují, jak odblokovat staženou/rozbalenou dll a pak ji importovat do prostředí PowerShell.

PowerShell classic lze nainstalovat vedle Az nebo AzureRM tak, aby pokrývaly typy "nových" i "klasických" prostředků.

## <a name="powershell-support-table"></a><a name="support-table"></a>Tabulka podpory prostředí PowerShell


| | **Az** |  **PowerShell klasické** |
| --- | --- | --- |
| Vytvoření nebo odstranění pracovních prostorů | [Šablony Resource Manageru](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Správa plánů závazků pracovního prostoru | [Nový-AzMiCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Správa uživatelů pracovního prostoru |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Správa webových služeb | [Nová služba AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nové" webové služby)|| [Nová služba AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasické" webové služby) |
| Správa koncových bodů/klíčů webové služby |  [Klíč Get-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Doplněk AmlWebServiceKoncovýbod](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Správa uživatelských datových sad/trénovaných modelů| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Správa uživatelských experimentů |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Správa vlastních modulů | | [Nový modul AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Další kroky
V úplné dokumentaci naleznete v tomto modulu prostředí PowerShell:
* [PowerShell klasické](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
