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
ms.date: 12/07/2018
ms.author: pbutlerm
ms.openlocfilehash: 63b7ee4e0d9cb9d0d1f26119fe73573b124d04e8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196701"
---
# <a name="azure-application-offer"></a>Nabídky aplikace Azure

Tato část vysvětluje, jak publikovat novou nabídku aplikací Azure pro Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>.
|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Každá aplikace Azure obsahuje šablonu Azure Resource Manageru, který definuje všechny technické prostředky využívané aplikací, který obvykle obsahuje jeden nebo více virtuálních počítačů a další podpůrné služby Azure nebo webové. | ![Ikona aplikace Azure](./media/azureapp-icon1.png)  |

## <a name="benefits"></a>Výhody

Výpis vašich aplikací na webu Microsoft marketplace výhody patří:

* Oslovení 100 milionů uživatelů Azure Active Directory v Office 365 a Dynamics 365.

* Rozšíření prodejní tým: oslovit obchodní uživatele po celém světě a získejte prodejní kanál, který zaujme uživatele, pomáhá generovat zájemce a zahajuje konverzace s novým zákazníkům napříč odvětvími.

* Získání užitečných přehledů: budeme sdílet přehledy o jaký je výkon vaší aplikace na AppSource, co funguje dobře a jak můžete ještě zlepšit prodejní postupy.

## <a name="types-of-azure-applications"></a>Typy aplikací Azure

Existují dva druhy aplikací Azure: spravované aplikace a šablony řešení. Přestože je to podobné, existují některé důležité rozdíly.

### <a name="solution-template"></a>Šablona řešení

Šablony řešení jsou jedním z hlavních způsobů, jak publikování řešení na webu Marketplace. Tento typ nabídky se používá, pokud vaše řešení vyžaduje další nasazení a konfigurace služby automation za jeden virtuální počítač (VM). Můžete automatizovat poskytuje více než jednoho virtuálního počítače pomocí šablony řešení. To zahrnuje zřizování prostředků sítě a úložiště k poskytování komplexní řešení IaaS. Přehled požadavků šablony řešení a modelu fakturace, naleznete v tématu [aplikací Azure: šablony řešení](https://docs.microsoft.com/azure/marketplace/marketplace-solution-templates).

### <a name="managed-application"></a>Spravovaná aplikace

Spravovaná aplikace se podobá šabloně řešení v Marketplace, ale s jedním zásadním rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Azure Managed applications umožňuje snadno sestavovat a poskytovat plně spravovanou, kompletních aplikací pro vaše zákazníky.

Kromě na webu Marketplace můžete také nabízí spravované aplikace katalogu služeb. Katalog služeb je interní katalog schválených řešení určených pro uživatele v rámci organizace. Pomocí katalogu splňovat standardy organizace při nabízení řešení pro skupiny v organizaci. Zaměstnanci v katalogu snadno najdou aplikace doporučené a schválené oddělením IT.

Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Azure managed applications přehled](https://docs.microsoft.com/azure/managed-applications/overview).

## <a name="publishing-overview"></a>Přehled publikování

V následujícím videu [sestavování řešení šablony a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), je uveden přehled o tom, jak vytvořit šablonu Azure Resource Manageru pro definování řešení Azure aplikace a jak potom následně publikujte aplikaci na webu Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]

## <a name="publishing-process-workflow"></a>Publikování procesů pracovního postupu

Následující diagram znázorňuje proces vysoké úrovně pro uvedení nabídky aplikace Azure.

![Pracovní postup pro publikování nabídky](./media/new-offer-process.png)

## <a name="offer-components"></a>Nabídka komponenty

Tato část popisuje elementy publikování nabídka spravované aplikace a slouží jako vodítko pro vydavatele na webu Azure Marketplace. Publikování na rozdělena na následující hlavní oddíly: 

* [Požadavky](./cpp-prerequisites.md) – je uveden seznam technických a obchodních požadavků před vytvořením nebo publikování nabídka spravované aplikace. 
* [Vytvořte svou nabídku](./cpp-create-offer.md) – obsahuje kroky potřebné k vytvoření položky nabídky spravované aplikace pomocí portálu partnerů cloudu. 
* [Publikování nabídky](./cpp-publish-offer.md)– popisuje, jak odeslat nabídku k publikování na webu Azure Marketplace.

## <a name="steps-in-the-publishing-process"></a>Kroky v procesu publikování

Postup vysoké úrovně pro uvedení nabídky aplikace Azure jsou:

1. Vytvořte svou nabídku – poskytují podrobné informace týkající se nabídky. Tyto informace zahrnují: popis nabídky, marketingové materiály, informace o podpoře a specifikace asset.

2. Vytvořit obchodní a technické prostředky – vytvoření prostředků (právní dokumenty a marketingové materiály) a technických prostředků přidružené řešení.

3. Vytvoření skladové Položce – vytvoření skladové jednotky spojené s nabídkou. Je vyžadována pro každý obrázek, který se chystáte publikovat jedinečný skladová položka.

4. Certifikace a publikujte nabídku – po dokončení nabídka a technických prostředků, můžete odeslat nabídku. Toto odeslání spustí se proces publikování. Během tohoto procesu řešení je testována ověřen, certifikace, pak "místo pro živé" na webu Azure Marketplace.

## <a name="next-steps"></a>Další postup

Předtím, než byste zvážit tyto kroky, je nutné splnit [technické a podnikové požadavky](./cpp-prerequisites.md) pro publikování spravované aplikace na webu Microsoft Azure Marketplace.