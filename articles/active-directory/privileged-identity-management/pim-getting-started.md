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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 018cc54a770a0d18fa9af3d93fec2f5f720ce17b
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804528"
---
# <a name="start-using-pim"></a>Zahájení práce s využitím PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace. Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalších online služeb Microsoftu jako Office 365 nebo Microsoft Intune.

Tento článek popisuje, jak povolit a začít používat PIM.

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít PIM, musíte mít jednu z těchto licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [licenční požadavky pro použití PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>První osoba pro použití PIM

Pokud jste první, kdo bude ve vašem adresáři používat PIM, přiřadíte automaticky role správce [zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) v adresáři. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí Azure AD pro uživatele. Kromě toho se můžete rozhodnout spustit [Průvodce zabezpečením](pim-security-wizard.md) , který vás provede počátečním zjišťováním a přiřazením.

## <a name="enable-pim"></a>Povolit PIM

Pokud chcete ve svém adresáři začít používat PIM, musíte nejdřív povolit PIM.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce vašeho adresáře.

    Chcete-li povolit PIM pro adresář, musíte být globálním správcem s účtem @yourdomain.comorganizace (například), nikoli účet Microsoft ( @outlook.comnapříklad).

1. Klikněte na **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete rychlý Start PIM.

1. V seznamu klikněte na **souhlas s PIM**.

    ![Souhlas s PIM pro povolení PIM](./media/pim-getting-started/consent-pim.png)

1. Klikněte na **ověřit moji identitu** a ověřte svoji identitu pomocí Azure MFA. Zobrazí se výzva k výběru účtu.

    ![Vyberte okno účtu a ověřte svoji identitu.](./media/pim-getting-started/pick-account.png)

1. Pokud se k ověření požaduje více informací, provedete vás procesem. Další informace najdete v tématu [získání pomoc s dvoustupňové ověřováním](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Pokud vaše organizace potřebuje další informace, požádejte o další informace požadované okno.](./media/pim-getting-started/more-information-required.png)

    Můžete být například požádáni o poskytnutí ověření pro telefon.

    ![Stránka pro další ověření zabezpečení s dotazem, jak vás kontaktovat](./media/pim-getting-started/additional-security-verification.png)

1. Po dokončení procesu ověřování klikněte na tlačítko **souhlasu** .

1. Ve zprávě, která se zobrazí, klikněte na **Ano** a odsouhlaste službu PIM.

    ![Souhlas se zprávou PIM k dokončení procesu souhlasu](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrace PIM pro role Azure AD

Jakmile pro svůj adresář povolíte PIM, budete se muset ke správě rolí Azure AD zaregistrovat PIM.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

    ![Registrace PIM pro role Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klikněte na **zaregistrovat**se.

1. Ve zprávě, která se zobrazí, se kliknutím na tlačítko **Ano** Zaregistrujte PIM a spravujte role Azure AD.

    ![Zaregistrujte si zprávu PIM pro role Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po dokončení registrace se povolí možnosti Azure AD. Může být potřeba aktualizovat portál.

    Informace o tom, jak zjistit a vybrat prostředky Azure, které se mají chránit pomocí PIM, najdete v tématu [zjištění prostředků Azure pro správu v PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení PIM můžete provádět úlohy správy identit.

![Navigační okno v PIM zobrazující úkoly a spravovat možnosti](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úkol a Správa | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí, které vám jsou přiřazeny. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše nedokončené žádosti o aktivaci přiřazení oprávněných rolí. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživateli ve vašem adresáři, který jste určili ke schválení. |
| **Zkontrolovat přístup** | Zobrazí seznam aktivních kontrol přístupu, které jste přiřadili k dokončení, ať už kontrolujete přístup sami nebo někomu jinému. |
| **Role Azure AD** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |
| **Prostředky Azure** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidání dlaždice PIM na řídicí panel

Chcete-li usnadnit otevírání PIM, měli byste na řídicí panel Azure Portal přidat dlaždici PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Klikněte na **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete rychlý Start PIM.

1. Ověřte **okno připnout na řídicí panel** , abyste mohli připnout okno rychlý Start PIM na řídicí panel.

    ![Ikona připínáček pro připnutí okna PIM na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice podobná této:

    ![Dlaždice PIM pro rychlý Start na řídicím panelu](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další postup

- [Přiřazení rolí Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Zjištění prostředků Azure pro správu v PIM](pim-resource-roles-discover-resources.md)
