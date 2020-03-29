---
title: Konfigurace a používání veřejných prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nakonfigurovat a používat veřejná prostředí (šablony Azure Resource Manager v úložišti Git) v Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 127a6986e04cf90f69b2a8ec70b90b877e534708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721689"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurace a používání veřejných prostředí v laboratořích Azure DevTest Labs
Azure DevTest Labs má [veřejné úložiště šablon Azure Resource Manageru,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) které můžete použít k vytvoření prostředí, aniž byste se museli sami připojovat k externímu zdroji GitHubu. Toto úložiště obsahuje často používané šablony, jako jsou Azure Web Apps, Cluster Service Fabric a vývojové prostředí SharePoint Farm. Tato funkce je podobná veřejnému úložišti artefaktů, které je součástí každého testovacího prostředí, které vytvoříte. Úložiště prostředí umožňuje rychle začít s předem napájenými šablonami prostředí s minimálními vstupními parametry, které vám poskytnou bezproblémové začínáme s prostředky PaaS v rámci testovacích prostředí. 

## <a name="configuring-public-environments"></a>Konfigurace veřejných prostředí
Jako vlastník testovacího prostředí můžete povolit úložiště veřejného prostředí pro vaše testovací prostředí během vytváření testovacího prostředí. Chcete-li povolit veřejná prostředí pro testovací prostředí, vyberte **možnost Zapnuto** pro pole **Veřejné prostředí** při vytváření testovacího prostředí. 

![Povolení veřejného prostředí pro novou testovací prostředí](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Pro existující testovací prostředí není povoleno úložiště veřejného prostředí. Ručně povolit použití šablon v úložišti. Pro testovací prostředí vytvořené pomocí šablon Správce prostředků je úložiště ve výchozím nastavení také zakázáno.

Můžete povolit nebo zakázat veřejná prostředí pro testovací prostředí a také zpřístupnit pouze určitá prostředí uživatelům testovacího prostředí pomocí následujících kroků: 

1. Vyberte **Konfigurace a zásady** pro testovací prostředí. 
2. V části **ZÁKLADY VIRTUÁLNÍCH STROJŮ** vyberte **Veřejná prostředí**.
3. Chcete-li povolit veřejná prostředí pro testovací prostředí, vyberte **možnost Ano**. Jinak vyberte **No** (Ne). 
4. Pokud jste povolili veřejná prostředí, všechna prostředí v úložišti jsou povolena ve výchozím nastavení. Můžete zrušit výběr prostředí, aby nebylo k dispozici uživatelům testovacího prostředí. 

![Stránka Veřejná prostředí](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Použití šablon prostředí jako uživatele testovacího prostředí
Jako uživatel testovacího prostředí můžete vytvořit nové prostředí z povoleného seznamu šablon prostředí pouhým výběrem **možnosti +Přidat** z panelu nástrojů na stránce testovacího prostředí. Seznam základen obsahuje šablony veřejných prostředí povolené správcem testovacího prostředí v horní části seznamu.

![Šablony veřejného prostředí](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Další kroky
Toto úložiště je úložiště s otevřeným zdrojovým kódem, které můžete přispět k přidání často používaných a užitečných šablon Správce prostředků. Chcete-li přispět, jednoduše odešlete žádost o přijetí vyžádat proti úložišti.  
