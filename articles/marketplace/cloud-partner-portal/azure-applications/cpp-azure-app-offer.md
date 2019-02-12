---
title: Nabídka Azure aplikace | Dokumentace Microsoftu
description: Přehled procesu k publikování aplikace Azure nabízejí na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pbutlerm
ms.openlocfilehash: 82d072cc6f86ae758bd0fdd4d02b68b1ac1de53a
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097146"
---
# <a name="azure-application-offer"></a>Nabídky aplikace Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Tato část vysvětluje, jak publikovat novou nabídku Azure aplikace [Azure Marketplace](https://azuremarketplace.microsoft.com).  Každá aplikace Azure obsahuje šablonu Azure Resource Manageru, který definuje všechny technické prostředky využívané aplikací, který obvykle obsahuje jeden nebo více virtuálních počítačů a další podpůrné služby Azure nebo webové. Všechny nabídky aplikace Azure, musíte povolit přístup k zabezpečení prostřednictvím [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikona aplikace Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Přehled publikování

V následujícím videu [sestavování řešení šablony a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), slouží jako úvod: co nabízí typy jsou k dispozici, jaké technické prostředky jsou povinné, jak si můžete vytvořit Azure Resource Manageru Šablona, vyvíjet a testovat aplikace uživatelského rozhraní, jak publikovat aplikaci a procesu kontroly aplikací.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikací Azure

Existují dva druhy aplikací Azure: spravované aplikace a šablony řešení. 

- Šablony řešení jsou jedním z hlavních způsobů, jak publikování řešení na webu Marketplace. Tento typ nabídky se používá, pokud vaše řešení vyžaduje další nasazení a konfigurace služby automation za jeden virtuální počítač (VM). Můžete automatizovat poskytuje více než jednoho virtuálního počítače pomocí šablony řešení. Tato automatizace zahrnuje zřizování prostředků sítě a úložiště k poskytování komplexní řešení IaaS. Přehled požadavků šablony řešení a modelu fakturace, naleznete v tématu [aplikací Azure: šablony řešení](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Spravované aplikace jsou podobné šablony řešení s jedním klíčovým rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Azure Managed applications umožňuje snadno sestavovat a poskytovat plně spravovanou, kompletních aplikací pro vaše zákazníky.

Kromě webu Azure Marketplace nabízí také spravované aplikace katalogu služeb. Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Pomocí katalogu splňovat standardy organizace při nabízení řešení pro skupiny v organizaci. Zaměstnanci v katalogu snadno najdou aplikace doporučené a schválené oddělením IT.

Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Azure managed applications přehled](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Publikování procesů pracovního postupu

Následující diagram znázorňuje proces vysoké úrovně pro uvedení nabídky aplikace Azure.

![Pracovní postup pro publikování nabídky](./media/new-offer-process.png)

Postup vysoké úrovně pro uvedení nabídky aplikace Azure jsou:

0. Splnění [požadavky](./cpp-prerequisites.md) – (nejsou zobrazené) ověřte, že jste splnili obchodní a technické požadavky pro publikování aplikaci Azure na webu Azure Marketplace. 

1. [Vytvořte svou nabídku](./cpp-create-offer.md) -poskytují podrobné informace týkající se nabídky. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace asset.

2. [Vytvořit nebo shromažďovat existující obchodní a technické prostředky](./cpp-create-technical-assets.md) – vytváření podnikových prostředků (právní dokumenty a marketingové materiály) a technických prostředků přidružené řešení.

3. [Vytvoření skladové Položce](./cpp-skus-tab.md) – vytvoření skladové jednotky spojené s nabídkou. Je vyžadována pro každý obrázek, který se chystáte publikovat jedinečný skladová položka.

4. Certifikace a [publikování nabídky](./cpp-publish-offer.md) – po dokončení nabídka a technických prostředků, můžete odeslat nabídku. Toto odeslání spustí se proces publikování. Během tohoto procesu řešení je testována ověřen, certifikace, pak "místo pro živé" na webu Azure Marketplace.


## <a name="next-steps"></a>Další postup

Předtím, než byste zvážit tyto kroky, je nutné splnit [technické a podnikové požadavky](./cpp-prerequisites.md) pro publikování spravované aplikace na webu Microsoft Azure Marketplace.
