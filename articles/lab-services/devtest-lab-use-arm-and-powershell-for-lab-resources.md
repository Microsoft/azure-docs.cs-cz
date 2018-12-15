---
title: Vytvoření a úpravy testovacích prostředí automaticky pomocí šablon Azure Resource Manageru pomocí Powershellu | Dokumentace Microsoftu
description: Další informace o použití šablon Azure Resource Manageru pomocí prostředí PowerShell k vytvoření nebo úpravy testovacích prostředí v prostředí DevTest lab automaticky
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
ms.openlocfilehash: 2c15cac7897e25018057aa47cc2043050ed0ae5b
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413196"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Vytvoření a úpravy testovacích prostředí automaticky pomocí šablon Azure Resource Manageru a Powershellu

DevTest Labs poskytuje mnoho šablon Azure Resource Manageru a skripty prostředí PowerShell, které můžete vám pomohou rychle a automaticky vytvářet nové testovací prostředí nebo úpravy existujících testovacích prostředí a potom nasazovat tyto prostředky.

Tento článek pomůže vás provede procesem použití těchto šablon a skripty pro automatizaci vytváření, úpravu a nasazení vaší laboratoře. Tento článek také se dozvíte, kde najdete další informace o tom, jak pomocí prostředí PowerShell provádět několik běžných úloh ve službě DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Krok 1: Shromážděte šablony a skripty
Můžete najít předem vytvořených [šablon Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) a [skripty prostředí PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/Scripts) v našich veřejných [úložiště GitHub](https://github.com/Azure/azure-devtestlab). Použijte je jako-je, nebo si je přizpůsobit pro vaše potřeby a uložit je ve vašem vlastním [privátní úložiště Git](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Krok 2: Úprava šablony Azure Resource Manageru
Můžete postupovat podle kroků uvedených v [vytvoření první šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) Pokud nikdy jste vytvořili šablonu před.

Kromě toho [osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokyny a návrhy k vytváření šablon Azure Resource Manageru, které je spolehlivé a snadné použití. Obvykle budete používat jeden z přístupů nebo příkladů varianta a šablonu upravit pro vaše potřeby.

## <a name="step-3-deploy-resources-with-powershell"></a>Krok 3: Nasazení prostředků pomocí Powershellu
Po provedení úprav šablony a skripty provedl nezbytné kroky pro [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Tento článek poskytuje obecné informace o použití Azure Powershellu s šablonami Azure Resource Manageru k nasazení prostředků do Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Běžné úkoly, které můžete provádět v DevTest labs s využitím Powershellu
Existují spoustu dalších běžných úkolů, které můžete automatizovat pomocí prostředí PowerShell. Dokumentace ke službě následující oddíly popisují kroky potřebné k provedení těchto úloh.

* [Vytvoření vlastní image ze souboru VHD pomocí Powershellu](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Nahrání souboru VHD do účtu úložiště testovacího prostředí pomocí Powershellu](devtest-lab-upload-vhd-using-powershell.md)
* [Přidat externího uživatele do testovacího prostředí pomocí Powershellu](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Vytvořit vlastní roli testovacího prostředí pomocí Powershellu](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Další postup
* Zjistěte, jak vytvořit [privátní úložiště Git](devtest-lab-add-artifact-repo.md) ve kterém budete ukládat přizpůsobené šablony nebo skripty.
* Prozkoumejte [šablon Azure Resource Manageru z Galerie šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
