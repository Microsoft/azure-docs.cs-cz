---
title: Konfigurace a použití veřejných prostředí v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat a používat veřejná prostředí (Azure Resource Manager šablony v úložišti Git) v Azure DevTest Labs.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721689"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurace a použití veřejných prostředí v Azure DevTest Labs
Azure DevTest Labs má [veřejné úložiště Azure Resource Managerch šablon](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , které můžete použít k vytváření prostředí, aniž byste se museli připojovat k externímu zdroji GitHubu sami. Toto úložiště obsahuje často používané šablony, jako je Azure Web Apps, Service Fabric cluster a vývojové prostředí farmy služby SharePoint. Tato funkce je podobná veřejnému úložišti artefaktů, které jsou zahrnuty pro každé testovací prostředí, které vytvoříte. Úložiště prostředí vám umožní rychle začít s předdefinovanými šablonami prostředí s minimálními vstupními parametry, aby vám poskytovala hladké prostředí Začínáme s PaaS prostředky v rámci Labs. 

## <a name="configuring-public-environments"></a>Konfigurace veřejných prostředí
Jako vlastník testovacího prostředí můžete povolit úložiště veřejného prostředí pro vaše testovací prostředí během vytváření testovacího prostředí. Pokud chcete pro testovací prostředí povolit veřejná prostředí, vyberte pro pole **veřejné prostředí** při vytváření testovacího prostředí možnost **zapnuto** . 

![Povolit veřejné prostředí pro nové testovací prostředí](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Pro existující cvičení není úložiště veřejné prostředí povolené. Ručně ho povolte pro použití šablon v úložišti. Pro laboratoře vytvořené pomocí šablon Správce prostředků je úložiště ve výchozím nastavení zakázané.

Můžete povolit nebo zakázat veřejná prostředí pro testovací prostředí a také zpřístupnit uživatelům testovacího prostředí pouze konkrétní prostředí pomocí následujících kroků: 

1. Vyberte **konfiguraci a zásady** pro testovací prostředí. 
2. V části **základy virtuálního počítače** vyberte **Veřejná prostředí**.
3. Pokud chcete pro testovací prostředí povolit veřejná prostředí, vyberte **Ano**. V opačném případě vyberte možnost **ne**. 
4. Pokud jste povolili veřejná prostředí, všechna prostředí v úložišti jsou ve výchozím nastavení povolená. Můžete zrušit výběr prostředí, aby ho uživatelé testovacího prostředí nemohli mít k dispozici. 

![Stránka veřejné prostředí](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Použití šablon prostředí jako uživatele testovacího prostředí
Jako uživatel testovacího prostředí můžete vytvořit nové prostředí ze seznamu povolených šablon prostředí pouhým výběrem možnosti **+ Přidat** na panelu nástrojů na stránce testovací prostředí. Seznam základů zahrnuje šablony pro veřejné prostředí, které vám povolil správce testovacího prostředí v horní části seznamu.

![Šablony pro veřejné prostředí](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Další kroky
Toto úložiště je open source úložiště, které můžete přispívat k přidávání často používaných a užitečných Správce prostředků šablon. Aby bylo možné přispívat, stačí odeslat žádost o přijetí změn z úložiště.  
