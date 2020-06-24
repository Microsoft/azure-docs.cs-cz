---
title: Vytvoření nebo úprava cvičení pomocí šablon Azure Resource Manager
description: Naučte se používat šablony Azure Resource Manager s PowerShellem k automatickému vytváření nebo úpravě testovacích prostředí v DevTest laboratoři.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898539"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatické vytváření a úpravy testovacích prostředí pomocí šablon Azure Resource Manager a PowerShellu

DevTest Labs poskytuje mnoho šablon Azure Resource Manager a skriptů PowerShellu, které vám můžou pomáhat rychle a automaticky vytvářet nové laboratoře nebo měnit existující laboratoře a následně tyto prostředky nasadit.

Tento článek vám pomůže s procesem použití těchto šablon a skriptů k automatizaci vytváření, úprav a nasazení vašich cvičení. Tento článek také ukazuje, kde najdete další informace o tom, jak pomocí PowerShellu provádět některé běžné úkoly v DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: shromáždění šablon a skriptů
Předem připravené [šablony Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) a [skripty PowerShellu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) najdete v našem veřejném [úložišti GitHub](https://github.com/Azure/azure-devtestlab). Můžete je použít tak, jak jsou, nebo si je přizpůsobit podle svých potřeb a ukládat je do vlastního [privátního úložiště Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Úprava šablony Azure Resource Manager
Pokud jste šablonu nikdy předtím nevytvořili, můžete postupovat podle kroků v části [Vytvoření první šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) .

Kromě toho [osvědčené postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokynů a návrhů, které vám pomohou vytvořit Azure Resource Manager šablony, které jsou spolehlivé a snadno použitelné. Obvykle použijete variaci jednoho z uvedených přístupů nebo příkladů a upravíte šablonu podle svých potřeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: nasazení prostředků pomocí PowerShellu
Po přizpůsobení šablon a skriptů postupujte podle kroků nezbytných k [nasazení prostředků pomocí Správce prostředků šablon a Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). V tomto článku najdete obecné informace o použití Azure PowerShell s Azure Resource Manager šablonami k nasazení prostředků do Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Běžné úlohy, které můžete provádět v DevTest Labs pomocí PowerShellu
K dispozici je mnoho dalších běžných úloh, které můžete automatizovat pomocí prostředí PowerShell. Následující části dokumentace obsahují přehled kroků potřebných k provedení těchto úloh.

* [Vytvoření vlastní image ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí PowerShellu](devtest-lab-upload-vhd-using-powershell.md)
* [Přidání externího uživatele do testovacího prostředí pomocí PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Vytvoření vlastní role testovacího prostředí pomocí prostředí PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Další kroky
* Naučte se vytvářet [privátní úložiště Git](devtest-lab-add-artifact-repo.md) , kam budete ukládat své přizpůsobené šablony nebo skripty.
* Prozkoumejte [šablony Azure Resource Manager z galerie šablon pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates).
