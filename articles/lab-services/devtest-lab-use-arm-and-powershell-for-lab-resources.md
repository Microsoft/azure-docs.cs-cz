---
title: Vytváření nebo úpravy testovacích prostředí pomocí šablon Azure Resource Manager
description: Zjistěte, jak pomocí šablon Azure Resource Manageru s PowerShellem automaticky vytvářet nebo upravovat testovací prostředí v testovacím prostředí DevTest.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170311"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatické vytváření nebo úpravy testovacích prostředí pomocí šablon Azure Resource Manager a PowerShellu

DevTest Labs poskytuje mnoho šablon Azure Resource Manager a skripty PowerShellu, které vám pomůžou rychle a automaticky vytvářet nová testovací prostředí nebo upravovat stávající testovací prostředí a pak tyto prostředky nasadit.

Tento článek vám pomůže provést proces pomocí těchto šablon a skriptů k automatizaci vytváření, úprav a nasazení testovacích prostředí. Tento článek také ukazuje, kde můžete najít další informace o tom, jak používat Prostředí PowerShell k provádění některých běžných úkolů v DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Shromáždění šablon a skriptů
Přednastavené šablony [Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) a [skripty PowerShellu](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) najdete v našem veřejném [úložišti GitHub](https://github.com/Azure/azure-devtestlab). Používejte je tak, jak jsou, nebo je přizpůsobte svým potřebám a uložte je do svého [soukromého úložiště Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Úprava šablony Azure Resource Manageru
Postup můžete sledovat na [webu Vytvoření první šablony Azure Resource Manageru,](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) pokud jste šablonu ještě nikdy nevytvořili.

Doporučené [postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) navíc nabízí mnoho pokynů a návrhů, které vám pomohou vytvořit šablony Azure Resource Manageru, které jsou spolehlivé a snadno použitelné. Obvykle použijete variantu jednoho z uvedených přístupů nebo příkladů a upravíte šablonu podle svých potřeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Nasazení prostředků pomocí PowerShellu
Po přizpůsobení šablon a skriptů postupujte podle pokynů nezbytných k [nasazení prostředků pomocí šablon Správce prostředků a prostředí Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Tento článek obsahuje obecné informace o používání Azure PowerShellu se šablonami Azure Resource Managerka k nasazení prostředků do Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Běžné úlohy, které můžete provádět v testovacích prostředích DevTest pomocí prostředí PowerShell
Existuje mnoho dalších běžných úloh, které můžete automatizovat pomocí prostředí PowerShell. Následující části dokumentace popisují kroky potřebné k provedení těchto úkolů.

* [Vytvoření vlastního obrazu ze souboru VHD pomocí PowerShellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí prostředí PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Přidání externího uživatele do testovacího prostředí pomocí PowerShellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Vytvoření vlastní role testovacího prostředí pomocí PowerShellu](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Další kroky
* Přečtěte si, jak vytvořit [soukromé úložiště Git,](devtest-lab-add-artifact-repo.md) do kterého budete ukládat vlastní šablony nebo skripty.
* Prozkoumejte [šablony Azure Resource Manageru z galerie šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
