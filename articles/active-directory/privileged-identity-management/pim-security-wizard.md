---
title: Průvodce zabezpečením rolí Azure AD v PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Popisuje průvodce zabezpečením, který můžete použít k převodu trvalých privilegovaných přiřazení rolí Azure AD na způsobilé pomocí správy privilegovaných identit Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266569"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Průvodce zabezpečením rolí Azure AD ve správě privilegovaných identit

Pokud jste první osoba, která používá privilegovanou správu identit (PIM) ve vaší organizaci Azure Active Directory (Azure AD), zobrazí se vám průvodce, který vám pomůže začít. Průvodce vám pomůže pochopit bezpečnostní rizika privilegovaných identit a jak pomocí správy privilegovaných identit tato rizika snížit. Pokud chcete provést později, nemusíte v existujících přiřazeních rolí v průvodci provádět žádné změny.

## <a name="wizard-overview"></a>Průvodce – přehled

Než vaše organizace začne používat správu privilegovaných identit, jsou všechna přiřazení rolí trvalá: uživatelé jsou vždy v těchto rolích, i když v současné době nepotřebují svá oprávnění. První krok průvodce zobrazí seznam vysoce privilegovaných rolí a počet uživatelů, kteří jsou aktuálně v těchto rolích. Můžete přejít k podrobnostem konkrétní role a dozvědět se více o uživatelích, pokud jeden nebo více z nich nejsou obeznámeni.

Druhý krok průvodce umožňuje změnit přiřazení rolí správce.  

> [!WARNING]
> Je důležité, abyste měli alespoň jednoho globálního správce a více než jednoho správce privilegovaných rolí s účtem organizace (nikoli účtem Microsoft). Pokud existuje pouze jeden správce privilegovaných rolí, organizace nemůže spravovat správu privilegovaných identit, pokud je tento účet odstraněn.
> Přiřazení rolí také ponechte trvalá, pokud má uživatel účet Microsoft (jinými slovy účet, který používá k přihlášení ke službám Microsoftu, jako je Skype a Outlook.com). Pokud plánujete vyžadovat vícefaktorové ověřování pro aktivaci pro tuto roli, bude tento uživatel uzamčen.

## <a name="run-the-wizard"></a>Spuštění průvodce

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD** a pak vyberte **Průvodce**.

    ![Role Azure AD – stránka Průvodce zobrazující 3 kroky ke spuštění průvodce](./media/pim-security-wizard/wizard-start.png)

1. Vyberte **1 Zjišťování privilegovaných rolí**.

1. Projděte si seznam privilegovaných rolí a zjistěte, kteří uživatelé jsou stálí nebo způsobilí.

    ![Zjišťování privilegovaných rolí – podokno rolí zobrazující stálé a způsobilé členy](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Výběrem **možnosti Další** vyberte uživatele nebo skupiny, které chcete učinit způsobilými.

    ![Převést členy na způsobilou stránku s možnostmi výběru členů, které chcete, aby byli způsobilí pro role](./media/pim-security-wizard/convert-members-eligible.png)

1. Po výběru uživatelů nebo skupin vyberte **možnost Další**.

    ![Stránka Revize změn zobrazující členy s trvalými přiřazeními rolí, která budou převedena](./media/pim-security-wizard/review-changes.png)

1. Výběrem **možnosti OK** převedete trvalá přiřazení na způsobilá.

    Po dokončení konverze se zobrazí oznámení.

    ![Oznámení o stavu konverze](./media/pim-security-wizard/notification-completion.png)

Pokud potřebujete převést další přiřazení privilegovaných rolí na způsobilá, můžete průvodce spustit znovu. Pokud chcete místo průvodce použít rozhraní správy privilegovaných identit, přečtěte si informace [o přiřazení rolí Azure AD v části Správa privilegovaných identit](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Udělit přístup ostatním správcům ke správě privilegovaných identit](pim-how-to-give-access-to-pim.md)
