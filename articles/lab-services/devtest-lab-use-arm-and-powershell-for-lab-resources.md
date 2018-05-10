---
title: Vytvořit nebo upravit labs automaticky pomocí šablon Azure Resource Manageru pomocí prostředí PowerShell | Microsoft Docs
description: Další informace o použití šablon Azure Resource Manageru pomocí prostředí PowerShell k vytvoření nebo úpravě automaticky v testovacím prostředí DevTest labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7cf6e5569e9d47b2a279f39cf837a4c0558bf3c9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Vytvořit nebo upravit labs automaticky pomocí šablon Azure Resource Manageru a prostředí PowerShell

DevTest Labs poskytuje mnoho šablon Azure Resource Manageru a skriptů prostředí PowerShell, které můžete vám pomohou rychle a automaticky vytvořit nové labs nebo upravit existující labs a pak nasadit tyto prostředky.

Tento článek pomůže vás provede procesem pomocí těchto šablon a skripty pro automatizaci vytváření, úpravu a nasazení vaší laboratoře. Tento článek také ukazuje, kde můžete najít další informace o tom, jak pomocí prostředí PowerShell v DevTest Labs provádět některé běžné úlohy.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Shromáždění šablony a skripty
Můžete najít předem provedené [šablon Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) a [skriptů prostředí PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) v našeho veřejného [úložiště Github](https://github.com/Azure/azure-devtestlab). Použijte je jako-je, nebo si je přizpůsobit pro vaše potřeby a uložit je do vlastní [privátní úložiště Git](devtest-lab-add-artifact-repo.md). 

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Upravte šablony Azure Resource Manager
Můžete provést kroky v [vytvoření vaší první šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Pokud jste vytvořili šablonu před nikdy.

Kromě toho [osvědčené postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokyny a doporučení k vytváření šablon Azure Resource Manageru, která jsou spolehlivé a snadno se používá. Obvykle budete používat jeden z přístupů nebo ukázky poskytnuté varianta a upravovat šablony podle svých potřeb.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Nasazení prostředků v prostředí PowerShell
Po přizpůsobení šablony a skripty, postupujte podle kroků nutné [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Tento článek poskytuje obecné informace o použití prostředí Azure PowerShell k nasazení vašich prostředků Azure pomocí šablon Azure Resource Manager.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Běžné úlohy, které můžete provádět v DevTest labs pomocí prostředí PowerShell
Existuje mnoho dalších běžných úloh můžete automatizovat pomocí prostředí PowerShell. Dokumentace následující oddíly popisují kroky potřebné k provedení těchto úloh.

* [Vytvořit vlastní image ze souboru virtuálního pevného disku pomocí prostředí PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Nahrání souboru virtuálního pevného disku do testovacího prostředí na účet úložiště pomocí prostředí PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Přidat externího uživatele k testovacím prostředí pomocí prostředí PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Vytvoření testovacího prostředí vlastní role pomocí prostředí PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Další postup
* Naučte se vytvářet [privátní úložiště Git](devtest-lab-add-artifact-repo.md) kde budete ukládat vlastní šablony nebo skripty.
* Prozkoumejte [šablon Azure Resource Manageru z Galerie šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
