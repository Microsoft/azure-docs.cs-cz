---
title: Průvodce zabezpečením role Azure AD v PIM – Azure Active Directory | Dokumentace Microsoftu
description: Popisuje zabezpečení průvodce, který slouží k přiřazení role Azure AD převést trvalé privilegovaného na oprávněné pomocí Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4fd850ac2116dc7f353eea87845501fff020bb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476224"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Průvodce zabezpečením role Azure AD v PIM

Pokud jste první, kdo ke spuštění služby Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pro vaši organizaci, zobrazí se průvodce. Průvodce vám pomůže pochopit bezpečnostní rizika privilegované identity a jak používat PIM ke snížení rizika. Není nutné měnit stávající přiřazení rolí v průvodci, pokud chcete to udělat později.

## <a name="wizard-overview"></a>Průvodce – přehled

Před zahájením vaší organizace pomocí PIM jsou trvalé všechna přiřazení rolí: uživatelům se vždycky nacházejí v těchto rolích i v případě, že nejsou potřeba v současné době jejich oprávnění. První krok průvodce zobrazí seznam rolí vysokými a kolik uživatelů je aktuálně v těchto rolích. Můžete přejít na konkrétní roli Další informace o uživatelích, pokud jeden nebo více z nich neznáte.

Druhý krok průvodce vám dává možnost změnit přiřazení rolí správce.  

> [!WARNING]
> Je důležité, abyste měli aspoň jeden globální správce a více než jeden správce privilegovaných rolí pomocí účtu organizace (ne účet Microsoft). Pokud existuje jenom jeden správce privilegovaných rolí, organizaci nebude možné spravovat PIM, pokud tento účet je odstraněný.
> Také zachovat přiřazení rolí trvalé, pokud má uživatel účet Microsoft (účtu, které používají pro přihlášení ke službám Microsoftu, jako je Skype nebo Outlook.com). Pokud budete chtít vyžadovat vícefaktorové ověřování pro aktivaci pro tuto roli, tento uživatel bude uzamčena na.

## <a name="run-the-wizard"></a>Spuštění průvodce

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD** a potom klikněte na tlačítko **průvodce**.

    ![Role Azure AD – Průvodce stránky zobrazující 3 kroky ke spuštění Průvodce](./media/pim-security-wizard/wizard-start.png)

1. Klikněte na tlačítko **1 zjistit privilegované role**.

1. Projděte si seznam privilegovaných rolí, které chcete zjistit, kteří uživatelé jsou trvalé nebo oprávněné.

    ![Zjistit privilegované role – podokno Role zobrazující trvalý a bude možné členy](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klikněte na tlačítko **Další** vyberte členy, které chcete nastavit jako oprávněné.

    ![Převést členy oprávněné stránku s možnostmi a vyberte členy, které chcete nastavit jako oprávněné role](./media/pim-security-wizard/convert-members-eligible.png)

1. Po výběru členů, klikněte na tlačítko **Další**.

    ![Zkontrolujte změny stránky zobrazující členů se přiřazení trvalé rolí, které budou převedeny](./media/pim-security-wizard/review-changes.png)

1. Klikněte na tlačítko **OK** pro převod na trvalé přiřazení oprávněné.

    Po dokončení převodu, zobrazí se vám oznámení.

    ![Oznámení zobrazující stav převodu](./media/pim-security-wizard/notification-completion.png)

Pokud je potřeba převést na oprávněné. ostatní přiřazení privilegovaných rolí, můžete znovu spustit průvodce. Pokud chcete použít rozhraní PIM místo průvodce, přečtěte si téma [přiřazení role Azure AD v PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Další postup

- [Přiřazení role Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Udělení přístupu na jiné správce ke správě PIM](pim-how-to-give-access-to-pim.md)
