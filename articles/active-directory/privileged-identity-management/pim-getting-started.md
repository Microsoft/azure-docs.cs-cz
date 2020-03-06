---
title: Začněte používat PIM-Azure Active Directory | Microsoft Docs
description: Naučte se, jak povolit a začít používat Azure AD Privileged Identity Management (PIM) v Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733743f4680f3197a1754d87b4672641b2321907
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399334"
---
# <a name="start-using-privileged-identity-management"></a>Zahájení využití technologie Privileged Identity Management

S Privileged Identity Management (PIM) můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace Azure Active Directory (Azure AD). Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalším online služby, jako je Office 365 nebo Microsoft Intune.

Tento článek popisuje, jak povolit a začít používat Privileged Identity Management.

## <a name="prerequisites"></a>Předpoklady

Chcete-li použít Privileged Identity Management, je nutné mít jednu z následujících licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>První osoba pro použití PIM

Pokud jste první, kdo bude používat Privileged Identity Management ve vašem adresáři, automaticky se vám v adresáři přiřadí role správce [zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) . Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí Azure AD pro uživatele. Kromě toho se můžete rozhodnout spustit [Průvodce zabezpečením](pim-security-wizard.md) , který vás provede počátečním zjišťováním a přiřazením.

## <a name="enable-pim"></a>Povolit PIM

Pokud chcete ve svém adresáři začít používat Privileged Identity Management, musíte nejdřív povolit Privileged Identity Management.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce vašeho adresáře.

    Pokud chcete povolit Privileged Identity Management pro adresář, musíte být globálním správcem s účtem organizace (například @yourdomain.com), ne účet Microsoft (například @outlook.com).

1. Klikněte na **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete Privileged Identity Management rychlý Start.

1. V seznamu klikněte na **souhlas s PIM**.

    ![Souhlas s Privileged Identity Management povolení Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. Klikněte na **ověřit moji identitu** a ověřte svoji identitu pomocí Azure MFA. Zobrazí se výzva k výběru účtu.

    ![Vyberte okno účtu a ověřte svoji identitu.](./media/pim-getting-started/pick-account.png)

1. Pokud se k ověření požaduje více informací, provedete vás procesem. Další informace najdete v tématu [získání pomoc s dvoustupňové ověřováním](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

    ![Pokud vaše organizace potřebuje další informace, požádejte o další informace požadované okno.](./media/pim-getting-started/more-information-required.png)

    Můžete být například požádáni o poskytnutí ověření pro telefon.

    ![Stránka pro další ověření zabezpečení s dotazem, jak vás kontaktovat](./media/pim-getting-started/additional-security-verification.png)

1. Po dokončení procesu ověřování klikněte na tlačítko **souhlasu** .

1. Ve zprávě, která se zobrazí, klikněte na **Ano** a odsouhlaste službu Privileged Identity Management.

    ![Souhlas s Privileged Identity Managementovou zprávou k dokončení procesu souhlasu](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrace PIM pro role Azure AD

Jakmile pro svůj adresář povolíte Privileged Identity Management, budete se muset zaregistrovat Privileged Identity Management a spravovat role Azure AD.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

    ![Registrace Privileged Identity Management pro role Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klikněte na **zaregistrovat**se.

1. Ve zprávě, která se zobrazí, klikněte na **Ano** a zaregistrujte se Privileged Identity Management ke správě rolí Azure AD.

    ![Registrace Privileged Identity Management pro zprávu o rolích služby Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po dokončení registrace se povolí možnosti Azure AD. Může být potřeba aktualizovat portál.

    Informace o tom, jak zjišťovat a vybírat prostředky Azure, které se mají chránit pomocí Privileged Identity Management, najdete [v tématu zjištění prostředků Azure pro správu v Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Jakmile Privileged Identity Management nastavíte, můžete začít s úlohami správy identit.

![Navigační okno v Privileged Identity Management znázorňující úlohy a spravovat možnosti](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úkol a Správa | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí, které vám jsou přiřazeny. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše nedokončené žádosti o aktivaci přiřazení oprávněných rolí. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživateli ve vašem adresáři, který jste určili ke schválení. |
| **Zkontrolovat přístup** | Zobrazí seznam aktivních kontrol přístupu, které jste přiřadili k dokončení, ať už kontrolujete přístup sami nebo někomu jinému. |
| **Role Azure AD** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |
| **Prostředky Azure** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidání dlaždice PIM na řídicí panel

Aby bylo snazší otevřít Privileged Identity Management, měli byste do řídicího panelu Azure Portal přidat dlaždici Privileged Identity Management.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Klikněte na **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete Privileged Identity Management rychlý Start.

1. Chcete-li připnout okno rychlý Start Privileged Identity Management na řídicí panel, ověřte **okno připnout na řídicí panel** .

    ![Ikona připínáček pro připnutí Privileged Identity Management okna na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice podobná této:

    ![Dlaždice pro rychlý Start Privileged Identity Management na řídicím panelu](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Zjištění prostředků Azure pro správu v Privileged Identity Management](pim-resource-roles-discover-resources.md)
