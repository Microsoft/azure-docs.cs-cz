---
title: Vysvětlení vašeho vyúčtování služeb Azure Enterprise | Dokumentace Microsoftu
description: Zjistěte, jak číst a porozumět využití a vyúčtování Azure zákazníkům se smlouvou Enterprise
services: ''
documentationcenter: ''
author: adpick
manager: dougeby
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: banders
ms.openlocfilehash: 16feb41707e5afa07054d20b620f5822eedad773
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858908"
---
# <a name="understand-your-bill-for-azure-customers-with-an-enterprise-agreement"></a>Vysvětlení vašeho vyúčtování služeb Azure zákazníky se smlouvou Enterprise

Azure zákazníky se smlouvou Enterprise dostávat faktury, při překročení kreditu organizace nebo používání služeb, které nejsou pokryty všemi kreditu. 

Kredit vaší organizace zahrnuje svůj peněžní závazek. Peněžní závazek je částky zaplacené předem organizaci využití služeb Azure. Prostředky peněžních závazků můžete přidat k vaší smlouvě Enterprise kontaktujte vašeho account manažer Microsoftu nebo prodejce v systému.  

## <a name="when-credit-exceeded-or-doesnt-apply"></a>Při překročení kreditu platí či neplatí

Jeden nebo více faktur získáte, když dojde k následujícímu:

- **Nadlimitní využití služby**: Poplatky za využívání vaší organizace být vyšší než zůstatek vašeho kreditu.
- **Poplatky se účtují samostatně**: Služby, vaše organizace používá nejsou pokryty všemi uvedeného kreditu. Pro tyto služby se fakturují bez ohledu na to, váš zůstatek kreditu:
    - Canonical
    - Základy Citrix XenApp
    - Citrix XenDesktop 
    - Registrovaný uživatel
    - Openlogic
    - Vzdálený přístup práva XenApp Essentials registrovaný uživatel
    - Ubuntu Advantage
    - Visual Studio Enterprise (měsíčně)
    - Visual Studio Enterprise (roční)
    - Visual Studio Professional (měsíčně)
    - Visual Studio Professional (Annual)
- **Poplatky za Marketplace**: Nákupy na Marketplace a využití Azure nejsou pokryty ve vaší organizaci Dal a služby se účtují zvlášť. Správce rozlehlé sítě má možnost můžete povolit nebo zakázat nákupy na Marketplace pro svoji organizaci na portálu Enterprise. 

Pokud máte poplatky kvůli Nadlimitní využití služby a poplatky se fakturují samostatně během fakturačního období získáte jednu fakturu, obsahující oba typy poplatky. Poplatky Tržiště za se vždy fakturují samostatně.

## <a name="review-charges"></a>Kontrola nákladů

Ke kontrole a ověřte poplatky na faktuře, musíte být správce podnikové sítě. Další informace najdete v tématu [správních rolí pochopit smlouvy Azure Enterprise v Azure](billing-understand-ea-roles.md). Pokud si nejste jisti, kdo je správce rozlehlé sítě pro vaši organizaci [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="review-service-overage-invoice"></a>Zkontrolovat fakturu za Nadlimitní využití služby

Porovnejte vaše množství celkové využití na portálu Enterprise v **sestavy** > **využití: Souhrn** s faktuře za Nadlimitní využití služby. Faktura za Nadlimitní využití služby zahrnuje využití, která překračuje kredit vaší organizace a/nebo služby, které nejsou pokryty všemi uvedeného kreditu. Částky na **využití: Souhrn** jsou bez daně. 

1. Přihlaste se k [Enterprise portal](https://ea.azure.com).
1. Vyberte **sestavy**.
1. V pravém horním rohu karty, přepněte zobrazení z **M** k **C** a odpovídají doby na faktuře.
 
   ![Snímek obrazovky zobrazující M + C možnost na využití: Souhrn.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Celkové využití** velikost by měla odpovídat **celkovou velikost rozšířené** na vaší faktuře za Nadlimitní využití služby. V následující tabulce jsou uvedeny podmínky a popisy, které jsou uvedené na faktuře a v **využití: Souhrn** na webu Enterprise Portal:

   |Termín faktur|Využití souhrnný termín|Popis|
   |---|---|---|
   |Celková částka rozšířené|Celkové využití|Celkové využití před zdaněním poplatek za určité období, než se použije tento kredit.|
   |Využití závazků|Využití závazků|Tento kredit použije toto konkrétní období.|
   |Celkový prodej|Celkové nadlimitní využití|Celkové využití nákladů, která překračuje vašeho kreditu. Tato částka nezahrnuje daně.|
   |Částka daně|Neuvedeno|Daň, která se vztahuje na množství celkového prodeje pro konkrétní období.|
   |Celková částka|Neuvedeno|Dobu vypršení platnosti faktury poté, co se použije tento kredit a přidá daně.|
1. Chcete-li získat další informace o poplatcích, přejděte na **stáhnout využití** > **stažení Rozšířené sestavy**. Tato sestava neobsahuje, daní a poplatků za rezervace nebo poplatků za marketplace.

      ![Snímek obrazovky zobrazující pokročilé sestavy stáhnout na kartě stáhnout využití.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-advanced.png)

### <a name="review-marketplace-invoice"></a>Kontrola faktuře marketplace

Porovnejte celkový počet na Azure Marketplace **sestavy** > **využití: Souhrn** na webu Enterprise Portal s vaší faktuře marketplace. Faktura za marketplace je pouze za nákupy a využití na Azure Marketplace. Částky na **využití: Souhrn** jsou bez daně. 

1. Přihlaste se k [Enterprise portal](https://ea.azure.com).
1. Vyberte **sestavy**.
1. V pravém horním rohu karty, přepněte zobrazení z **M** k **C** a odpovídají doby na faktuře.

     ![Snímek obrazovky zobrazující M + C možnost na využití: Souhrn.](./media/billing-understand-your-bill-ea/ea-portal-usage-sumary-cm-option.png)

1. **Azure Marketplace** celkový počet by měl odpovídat **celkový prodej** na vaší faktuře marketplace.
1. Chcete-li získat další informace o poplatcích podle použití, přejděte na **stáhnout využití**. V části **poplatků za Marketplace**vyberte **Stáhnout**. Tato sestava není zahrnuté daně, nebo zobrazit jednorázové nákupy.

     ![Stáhnout snímek obrazovky zobrazující možnost poplatků za Marketplace.](./media/billing-understand-your-bill-ea/ea-portal-download-usage-marketplace.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
