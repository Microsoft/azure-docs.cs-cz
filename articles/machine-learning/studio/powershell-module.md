---
title: Moduly prostředí PowerShell pro Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Pomocí Powershellu vytvářet a spravovat Azure Machine Learning Studio pracovní prostory, experimenty, webové služby a další.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: bee42f8a9582908963c0eef95a2fd04742cd425e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205668"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Moduly Powershellu pro Azure Machine Learning Studio

Používání modulů Powershellu, můžete programově spravovat Studio prostředky a prostředky, jako jsou pracovní prostory, datové sady a webových služeb.

Můžete pracovat s prostředky Studio pomocí tří modulů prostředí Powershell:

* [Azure PowerShell Az](#az-rm) vydána v roce 2018, zahrnuje všechny funkce AzureRM, i když se názvy různých rutin
* [AzureRM](#az-rm) vydána v roce 2016 nahrazuje Az prostředí PowerShell
* [Azure Machine Learning PowerShell classic](#classic) vydané v 2016

I když tyto moduly prostředí PowerShell mají některé podobné rysy, jednotlivé jsou nastavené pro určité scénáře. Tento článek popisuje rozdíly mezi moduly Powershellu a pomůže vám při rozhodování jaké vizualizace zvolit.  

Zkontrolujte [podporu tabulky](#support-table) dole můžete zjistit, které prostředky jsou podporovány každý modul. 

## <a name="az-rm"></a> Azure PowerShell Az a AzureRM

AZ je určený modul prostředí PowerShell pro interakci s Azure a obsahuje všechny předchozí funkce AzureRM. AzureRM budou dál dostávat opravy chyb, ale bude přijímat žádné nové rutiny nebo funkce.  AZ a AzureRM i spravovat nasazené pomocí řešení **Azure Resource Manageru** modelu nasazení. Tyto prostředky zahrnují pracovní prostory Studio a Studio "Nové" webové služby. 

PowerShell – classic je možné nainstalovat souběžně s Az nebo AzureRM zahrnují oba typy zdrojů "nové" a "klasickém". Však není doporučeno mít Az a AzureRM nainstalované ve stejnou dobu. Při rozhodování mezi Az a AzureRM, společnost Microsoft doporučuje Az pro všechny budoucí nasazení.  Další informace o Az oproti AzureRM i migrační cestu v [Úvod do prostředí Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Pokud chcete začít pracovat s Az, postupujte [pokyny k instalaci pro Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell – classic

Sady Studio [modulu PowerShell classic](https://aka.ms/amlps) umožňuje spravovat prostředky nasazené pomocí **modelu nasazení classic**. Tyto prostředky zahrnují Studio prostředky uživatele, "klasickém" webových služeb a koncových bodů "klasickém" webové služby.

Společnost Microsoft doporučuje, Zjednodušte nasazování a správu prostředků pomocí modelu nasazení Resource Manager pro všechny budoucí prostředky. Pokud chcete získat další informace o modelech nasazení, najdete v článku [Azure Resource Manageru a klasického nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) článku.

Chcete-li začít používat PowerShell – classic, stáhněte si [balíček verze](https://github.com/hning86/azuremlps/releases) z Githubu a postupujte podle [pokyny pro instalaci](https://github.com/hning86/azuremlps/blob/master/README.md). Pokyny vysvětlují, jak odblokovat DLL stažené/odblokujte a potom ho importujte do prostředí PowerShell.

PowerShell – classic je možné nainstalovat souběžně s Az nebo AzureRM zahrnují oba typy zdrojů "nové" a "klasickém".

## <a name="support-table"></a> Tabulka podporu prostředí PowerShell


| | **Az** |  **PowerShell – Classic** |
| --- | --- | --- |
| Vytvoření nebo odstranění pracovních prostorů | [Šablony Resource Manageru](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Spravovat plány závazků pracovního prostoru | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Správa uživatelů pracovního prostoru |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Správa webových služeb | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>("nové" web services)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasickém" web services) |
| Správa koncových bodů/klíče webové služby |  [Get-AzMlWebServiceKey](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Správa modelů trénovaných/datových sad uživatele| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Spravovat uživatele experimentů |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Správa vlastních modulů | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Další postup
Obraťte se na úplné dokumentaci tyto modul prostředí PowerShell:
* [PowerShell – Classic](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
