---
title: Průvodce zabezpečením rolí Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Popisuje Průvodce zabezpečením, který můžete použít k převodu trvalých přiřazení rolí Azure AD na oprávněné pomocí Azure AD Privileged Identity Management (PIM).
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
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847037"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Průvodce zabezpečením rolí Azure AD v Privileged Identity Management

Pokud jste první, kdo používá Privileged Identity Management (PIM) ve vaší organizaci Azure Active Directory (Azure AD), zobrazí se vám průvodce, abyste mohli začít. Průvodce vám pomůže porozumět bezpečnostním rizikům privilegovaných identit a způsobu použití Privileged Identity Management ke snížení těchto rizik. V průvodci nemusíte dělat žádné změny v existujících přiřazeních rolí, pokud to budete chtít udělat později.

## <a name="wizard-overview"></a>Přehled Průvodce

Předtím, než se vaše organizace začne používat Privileged Identity Management, jsou všechna přiřazení rolí trvalá: uživatelé jsou vždycky v těchto rolích, i když k nim zatím nepotřebují jejich oprávnění. V prvním kroku průvodce se zobrazí seznam rolí s vysokou úrovní oprávnění a počet uživatelů, kteří jsou v těchto rolích aktuálně. Můžete přejít k určité roli a získat další informace o uživatelích, pokud některé z nich nejsou známé.

Druhý krok průvodce vám nabídne možnost změnit přiřazení rolí správce.  

> [!WARNING]
> Je důležité, abyste měli aspoň jednoho globálního správce a více než jednoho správce privilegované role s účtem organizace (ne účet Microsoft). Pokud je k dispozici pouze jeden správce privilegovaných rolí, organizace nemůže spravovat Privileged Identity Management, pokud se tento účet odstraní.
> I když má uživatel účet Microsoft (jinými slovy, účet, který se používá pro přihlášení ke službám Microsoftu, jako je Skype a Outlook.com), zachovejte i přiřazení rolí na trvalé. Pokud máte v úmyslu vyžadovat službu Multi-Factor Authentication pro aktivaci této role, bude tento uživatel uzamčen.

## <a name="run-the-wizard"></a>Spuštění průvodce

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD** a pak vyberte **Průvodce**.

    ![Role Azure AD – stránka průvodce zobrazující 3 kroky pro spuštění Průvodce](./media/pim-security-wizard/wizard-start.png)

1. Vyberte **1 zjistit privilegované role**.

1. Projděte si seznam privilegovaných rolí a zjistěte, kteří uživatelé jsou trvalé nebo oprávněné.

    ![Vyhledat privilegované role – podokno role zobrazující trvalé a oprávněné členy](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Výběrem **Další** vyberte uživatele nebo skupiny, které chcete nastavit jako oprávněné.

    ![Převeďte členy na stránku s nárokem s možnostmi výběru členů, které chcete nastavit jako oprávněné pro role.](./media/pim-security-wizard/convert-members-eligible.png)

1. Jakmile vyberete uživatele nebo skupiny, vyberte **Další**.

    ![Stránka zkontrolovat změny zobrazující členy s trvalými přiřazeními rolí, které se budou převádět](./media/pim-security-wizard/review-changes.png)

1. Výběrem **OK** převedete trvalá přiřazení na oprávněné.

    Po dokončení převodu se zobrazí oznámení.

    ![Oznámení znázorňující stav převodu](./media/pim-security-wizard/notification-completion.png)

Pokud potřebujete převést další přiřazení privilegovaných rolí na oprávněné, můžete průvodce spustit znovu. Pokud chcete místo průvodce použít rozhraní Privileged Identity Management, přečtěte si téma [přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Udělte ostatním správcům přístup ke správě Privileged Identity Management](pim-how-to-give-access-to-pim.md)
