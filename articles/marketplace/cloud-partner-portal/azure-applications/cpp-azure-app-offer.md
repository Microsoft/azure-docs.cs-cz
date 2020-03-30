---
title: Nabídka aplikací Azure | Azure Marketplace
description: Přehled procesu publikování nabídky aplikací Azure na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: dsindona
ms.openlocfilehash: ed086ffdc49e21b819c0ee05b38ad882b4e269d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285312"
---
# <a name="azure-application-offer"></a>Nabídka aplikace Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> Tato část vysvětluje, jak publikovat novou nabídku aplikací Azure na [Azure Marketplace](https://azuremarketplace.microsoft.com).  Každá aplikace Azure obsahuje šablonu Azure Resource Manager, která definuje všechny technické prostředky používané aplikací, která obvykle zahrnuje jeden nebo více virtuálních počítačů a další podpůrné služby Azure nebo webové. Všechny nabídky aplikací Azure musí umožňovat zabezpečení přístupu prostřednictvím [služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikona aplikací Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Přehled publikování

Následující video, [vytváření šablon řešení a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603)je úvodem: jaké typy nabídek jsou k dispozici, jaké technické prostředky jsou vyžadovány, jak vytvořit šablonu Azure Resource Manager, vyvíjet a testovat uživatelské rozhraní aplikace, jak publikovat nabídku aplikace a proces kontroly aplikací.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikací Azure

Existují dva druhy aplikací Azure: spravované aplikace a šablony řešení. 

- Šablony řešení jsou jedním z hlavních způsobů publikování řešení na marketplace. Tento typ nabídky se používá, když vaše řešení vyžaduje další nasazení a automatizaci konfigurace nad rámec jednoho virtuálního počítače (VM). Pomocí šablony řešení můžete automatizovat poskytování více než jednoho virtuálního počítače. Tato automatizace zahrnuje zřizování síťových prostředků a prostředků úložiště pro poskytování komplexních řešení IaaS. Přehled požadavků na šablony řešení a fakturační model najdete v tématu [Azure Applications: solution templates](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Spravované aplikace jsou podobné šablonám řešení s jedním klíčovým rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Pomocí spravovaných aplikací Azure můžete snadno vytvářet a doručovat plně spravované aplikace na klíč svým zákazníkům.

Kromě Azure Marketplace můžete také nabízet spravované aplikace v katalogu služeb. Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Katalog slouží ke splnění organizačních standardů a zároveň nabízíte řešení pro skupiny v organizaci. Zaměstnanci v katalogu snadno najdou aplikace doporučené a schválené oddělením IT.

>[!Note]
>Přihlášení partnerského kanálu zprostředkovatelů cloudových řešení (CSP) je nyní k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím partnerských kanálů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení.](../../cloud-solution-providers.md)

Další informace o výhodách a typech spravovaných aplikací najdete v přehledu [spravovaných aplikací Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Pracovní postup procesu publikování

Následující diagram znázorňuje proces vysoké úrovně pro publikování nabídky aplikace Azure.

![Pracovní postup pro publikování nabídky](./media/new-offer-process.png)

Kroky vysoké úrovně pro publikování nabídky aplikací Azure jsou:

1. Splnění [požadavků](./cpp-prerequisites.md) – (nezobrazeno) Ověřte, že jste splnili obchodní a technické požadavky na publikování aplikace Azure na Azure Marketplace. 

1. [Vytvořit nabídku](./cpp-create-offer.md) - Poskytněte podrobné informace o nabídce. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace aktiv.

1. [Vytvořit nebo sbírat stávající obchodní a technický majetek](./cpp-create-technical-assets.md) – Vytvořte obchodní majetek (právní dokumenty a marketingové materiály) a technický majetek pro přidružené řešení.

1. [Vytvoření skladové položky](./cpp-skus-tab.md) – vytvořte skladové položky přidružené k nabídce. Pro každý obrázek, který plánujete publikovat, je vyžadována jedinečná skladová položka.

1. Certifikovat a [publikovat nabídku](./cpp-publish-offer.md) - Po dokončení nabídky a technických prostředků můžete nabídku odeslat. Toto odeslání spustí proces publikování. Během tohoto procesu se řešení testuje, ověřuje, certifikuje a pak "přejde do provozu" na Azure Marketplace.

## <a name="next-steps"></a>Další kroky

Než začnete tyto kroky zvážit, musíte splnit [technické a obchodní požadavky](./cpp-prerequisites.md) pro publikování spravované aplikace na webu Microsoft Azure Marketplace.
