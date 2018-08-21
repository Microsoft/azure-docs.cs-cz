---
title: Konfigurace a používání veřejných prostředích ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat a používat veřejných prostředích ve službě Azure DevTest Labs.
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
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: d93818cd875c4050b1b35f21ce580933776c5bc5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234881"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Konfigurace a používání veřejných prostředích ve službě Azure DevTest Labs
Azure DevTest Labs má [veřejném úložišti šablon Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , můžete použít k vytvoření prostředí bez nutnosti připojení k externímu zdroji Githubu sami. Toto úložiště obsahuje často používané šablony, jako je Azure Web Apps, Service Fabric Cluster a vývojové prostředí farmy služby SharePoint. Tato funkce je podobný veřejné úložiště artefaktů, který je součástí pro každý testovací prostředí, který vytvoříte. Úložiště prostředí umožňuje rychle začít s předem vytvořené prostředí šablony s minimální vstupní parametry, kde přinášejí smooth úvodní prostředí pro PaaS prostředky v rámci testovacích prostředí. 

## <a name="configuring-public-environments"></a>Konfigurace veřejných prostředích
Jako vlastník testovacího prostředí můžete povolit veřejné prostředí úložiště pro vaše testovací prostředí při vytváření testovacího prostředí. Chcete-li povolit veřejné prostředí pro testovací prostředí, vyberte **na** pro **veřejných prostředích** pole při vytváření testovacího prostředí. 

![Povolení veřejného prostředí pro nové testovací prostředí](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Pro existující testovací prostředí není povolené prostředí veřejného úložiště. Ručně povolte ho na použití šablony v úložišti. Pro testovací prostředí vytvořené pomocí šablon Resource Manageru je také standardně zakázáno úložiště.

Můžete povolit nebo zakázat veřejných prostředích pro testovací prostředí a také zpřístupnit jenom pro konkrétní prostředí uživatelům testovacího prostředí pomocí následujících kroků: 

1. Vyberte **konfigurace a zásad** testovacího prostředí. 
2. V **BÁZE pro virtuální počítače** vyberte **veřejných prostředích**.
3. Chcete-li povolit veřejné prostředí pro testovací prostředí, vyberte **Ano**. V opačném případě vyberte **ne**. 
4. Pokud jste povolili veřejných prostředích, ve výchozí hodnoty jsou povolené všechna prostředí v úložišti. Můžete zrušit výběr prostředí, aby byl není k dispozici uživatelům testovacího prostředí. 

![Stránka veřejných prostředích](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Použití šablon prostředí jako uživatele testovacího prostředí.
Jako uživatel, který testovací prostředí, můžete vytvořit nové prostředí ze seznamu povolených šablony prostředí tak, že jednoduše vyberete **+ přidat** z panelu nástrojů na stránce testovacího prostředí. Seznam základních tříd obsahuje šablony veřejných prostředích, stará správce vašeho testovacího prostředí v horní části seznamu.

![Šablony veřejná prostředí](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Další postup
Toto úložiště je open source úložiště, které chcete přispět k přidání často používané a užitečné šablon Resource Manageru vlastní. Abyste mohli přispívat, jednoduše odešlete žádost o přijetí změn pro úložiště.  
