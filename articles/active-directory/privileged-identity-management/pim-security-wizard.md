---
title: Průvodce zabezpečením role Azure AD v PIM | Dokumentace Microsoftu
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
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: b66dcc834ab089f28e6bb47c2905e6a2a3907091
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208754"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Průvodce zabezpečením role Azure AD v PIM

Pokud jste první, kdo ke spuštění Azure AD Privileged Identity Management (PIM) pro vaši organizaci, zobrazí se průvodce. Průvodce vám pomůže pochopit bezpečnostní rizika privilegované identity a jak používat PIM ke snížení rizika. Není nutné měnit stávající přiřazení rolí v průvodci, pokud chcete to udělat později.

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

    ![Role Azure AD – Průvodce](./media/pim-security-wizard/wizard-start.png)

1. Klikněte na tlačítko **1 zjistit privilegované role**.

1. Projděte si seznam privilegovaných rolí, které chcete zjistit, kteří uživatelé jsou trvalé nebo oprávněné.

    ![Objevte privilegované role uživatele](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Klikněte na tlačítko **Další** vyberte členy, které chcete nastavit jako oprávněné.

    ![Převod členů na oprávněné](./media/pim-security-wizard/convert-members-eligible.png)

1. Po výběru členů, klikněte na tlačítko **Další**.

    ![Zkontrolovat změny](./media/pim-security-wizard/review-changes.png)

1. Klikněte na tlačítko **OK** pro převod na trvalé přiřazení oprávněné.

    Po dokončení převodu, zobrazí se vám oznámení.

    ![Oznámení](./media/pim-security-wizard/notification-completion.png)

Pokud je potřeba převést na oprávněné. ostatní přiřazení privilegovaných rolí, můžete znovu spustit průvodce. Pokud chcete použít rozhraní PIM místo průvodce, přečtěte si téma [přiřadit role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Další postup

- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Udělení přístupu na jiné správce ke správě PIM](pim-how-to-give-access-to-pim.md)
