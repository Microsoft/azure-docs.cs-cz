---
title: Nabídka aplikací Azure | Azure Marketplace
description: Přehled procesu publikování nabídky aplikací Azure na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: pabutler
ms.openlocfilehash: 9125b5c71b63b27c58ea72b7bfd49f730854b33d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818791"
---
# <a name="azure-application-offer"></a>Nabídka aplikace Azure

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| <div class="body"> V této části se dozvíte, jak publikovat novou nabídku aplikace Azure na [Azure Marketplace](https://azuremarketplace.microsoft.com).  Každá aplikace Azure obsahuje šablonu Azure Resource Manager definující všechny technické prostředky používané aplikací, které obvykle zahrnují jeden nebo více virtuálních počítačů a další podpůrné služby Azure nebo webové služby. Všechny nabídky aplikací Azure musí povolit zabezpečení přístupu prostřednictvím [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).  </div> | ![Ikona aplikací Azure](./media/azureapp-icon1.png)  |

## <a name="publishing-overview"></a>Přehled publikování

Následující video, [sestavení šablon řešení a spravovaných aplikací pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), představuje Úvod: Jaké typy nabídek jsou k dispozici, jaké technické prostředky jsou požadovány, jak vytvořit šablonu Azure Resource Manager a vývoj a testování uživatelského rozhraní aplikace, publikování nabídky aplikace a procesu revize aplikace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


## <a name="types-of-azure-applications"></a>Typy aplikací Azure

Existují dva druhy aplikací Azure: spravované aplikace a šablony řešení. 

- Šablony řešení jsou jedním z hlavních způsobů publikování řešení na webu Marketplace. Tento typ nabídky se používá v případě, že vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jeden virtuální počítač (VM). Pomocí šablony řešení můžete automatizovat poskytování více než jednoho virtuálního počítače. Tato automatizace zahrnuje zřizování prostředků sítě a úložiště pro zajištění komplexních řešení IaaS. Přehled požadavků na šablonu řešení a účtovací model najdete v tématu [aplikace Azure: šablony řešení](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

- Spravované aplikace jsou podobné šablonám řešení s jedním hlavním rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Pomocí spravovaných aplikací Azure můžete svým zákazníkům snadno sestavovat a doručovat plně spravované klíč aplikace.

Kromě Azure Marketplace můžete také nabízet spravované aplikace v katalogu služeb. Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Katalog se používá ke splnění standardů organizace a zároveň nabízí řešení pro skupiny v organizaci. Zaměstnanci v katalogu snadno najdou aplikace doporučené a schválené oddělením IT.

>[!Note]
>Výslovný souhlas kanálu pro partnery Cloud Solution Providers (CSP) je teď k dispozici.  Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [Poskytovatelé cloudových řešení](../../cloud-solution-providers.md) .

Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Přehled spravovaných aplikací Azure](https://docs.microsoft.com/azure/managed-applications/overview).


## <a name="publishing-process-workflow"></a>Pracovní postup procesu publikování

Následující diagram znázorňuje proces vysoké úrovně pro publikování nabídky aplikací Azure.

![Pracovní postup pro nabídku publikování](./media/new-offer-process.png)

Mezi nejdůležitější kroky pro publikování nabídky aplikací Azure patří:

1. Splnění [požadavků](./cpp-prerequisites.md) – (nezobrazuje se) ověřte, že jste splnili obchodní a technické požadavky na publikování aplikace Azure na Azure Marketplace. 

1. [Vytvoření nabídky](./cpp-create-offer.md) – poskytněte podrobné informace o této nabídce. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace assetu.

1. [Vytvořte nebo Shromážděte stávající obchodní a technické prostředky](./cpp-create-technical-assets.md) – vytvořte si obchodní prostředky (právní dokumenty a marketingové materiály) a technické prostředky pro příslušné řešení.

1. [Vytvoření SKU](./cpp-skus-tab.md) – vytvořte SKU přidružené k této nabídce. Pro každou bitovou kopii, kterou plánujete publikovat, se vyžaduje jedinečná SKU.

1. Certifikace a [publikování nabídky](./cpp-publish-offer.md) – po dokončení nabídky a technických prostředků můžete nabídku odeslat. Toto odeslání spustí proces publikování. Během tohoto procesu je řešení testováno, ověřeno, certifikováno a pak "" bude fungovat "na Azure Marketplace.

## <a name="next-steps"></a>Další kroky

Než posuzujete tento postup, musíte splnit [technické a obchodní požadavky](./cpp-prerequisites.md) pro publikování spravované aplikace do Microsoft Azure Marketplace.
