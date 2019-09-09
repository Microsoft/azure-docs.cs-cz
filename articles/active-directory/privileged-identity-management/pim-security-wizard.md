---
title: Průvodce zabezpečením rolí Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Popisuje Průvodce zabezpečením, který můžete použít k převodu trvalých přiřazení rolí Azure AD na oprávněné pomocí Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d6d94df29ba16ecee06d70f5edac15a96a4299d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804014"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>Průvodce zabezpečením rolí Azure AD v PIM

Pokud jste první, kdo bude pro vaši organizaci používat službu Azure Active Directory (Azure AD) Privileged Identity Management (PIM), zobrazí se průvodce. Průvodce vám pomůže porozumět bezpečnostním rizikům privilegovaných identit a používání PIM k omezení těchto rizik. V průvodci nemusíte dělat žádné změny v existujících přiřazeních rolí, pokud to budete chtít udělat později.

## <a name="wizard-overview"></a>Přehled Průvodce

Předtím, než se vaše organizace začne používat PIM, jsou všechna přiřazení rolí trvalá: uživatelé jsou vždycky v těchto rolích, i když k nim zatím nepotřebují jejich oprávnění. V prvním kroku průvodce se zobrazí seznam rolí s vysokou úrovní oprávnění a počet uživatelů, kteří jsou v těchto rolích aktuálně. Můžete přejít k určité roli a získat další informace o uživatelích, pokud některé z nich nejsou známé.

Druhý krok průvodce vám nabídne možnost změnit přiřazení rolí správce.  

> [!WARNING]
> Je důležité, abyste měli aspoň jednoho globálního správce a více než jednoho správce privilegované role s účtem organizace (ne účet Microsoft). Pokud je k dispozici pouze jeden správce privilegovaných rolí, organizace nebude moci spravovat PIM, pokud je tento účet odstraněn.
> I když má uživatel účet Microsoft (účet, který se používá pro přihlášení ke službám Microsoftu, jako je Skype nebo Outlook.com), zachovejte i přiřazení role trvalé. Pokud máte v úmyslu pro aktivaci pro tuto roli vyžadovat MFA, bude tento uživatel uzamčen.

## <a name="run-the-wizard"></a>Spuštění průvodce

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD** a potom klikněte na **Průvodce**.

    ![Role Azure AD – stránka průvodce zobrazující 3 kroky pro spuštění Průvodce](./media/pim-security-wizard/wizard-start.png)

1. Klikněte na **1 zjistit privilegované role**.

1. Projděte si seznam privilegovaných rolí a zjistěte, kteří uživatelé jsou trvalé nebo oprávněné.

    ![Vyhledat privilegované role – podokno role zobrazující trvalé a oprávněné členy](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Kliknutím na tlačítko **Další** vyberte členy, které chcete nastavit jako oprávněné.

    ![Převeďte členy na stránku s nárokem s možnostmi výběru členů, které chcete nastavit jako oprávněné pro role.](./media/pim-security-wizard/convert-members-eligible.png)

1. Po výběru členů klikněte na **Další**.

    ![Stránka zkontrolovat změny zobrazující členy s trvalými přiřazeními rolí, které se budou převádět](./media/pim-security-wizard/review-changes.png)

1. Kliknutím na tlačítko **OK** převedete trvalá přiřazení na oprávněné.

    Po dokončení převodu se zobrazí oznámení.

    ![Oznámení znázorňující stav převodu](./media/pim-security-wizard/notification-completion.png)

Pokud potřebujete převést další přiřazení privilegovaných rolí na oprávněné, můžete průvodce spustit znovu. Pokud chcete použít rozhraní PIM místo průvodce, přečtěte si téma [přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Další postup

- [Přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Udělení přístupu jiným správcům pro správu PIM](pim-how-to-give-access-to-pim.md)
