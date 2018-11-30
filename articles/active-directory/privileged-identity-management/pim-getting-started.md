---
title: Začněte používat PIM – Azure | Dokumentace Microsoftu
description: Zjistěte, jak povolit a začít používat Azure AD Privileged Identity Management (PIM) na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 11/09/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 95582fb62f19dcc34aa03a6550f4dbc46257cf7f
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494343"
---
# <a name="start-using-pim"></a>Zahájení práce s využitím PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete spravovat, řídit a monitorovat přístup ve vaší organizaci. Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalších online služeb Microsoftu jako Office 365 nebo Microsoft Intune.

Tento článek popisuje, jak povolit a začít používat PIM.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat PIM, musí mít jeden z těchto licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [požadavky na předplatné používat PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>První, kdo používat PIM

Pokud jste první, kdo ve vašem adresáři používat PIM, se automaticky přiřazují [správce zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) v daném adresáři. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře Azure AD. Kromě toho můžete spustit [Průvodce zabezpečením](pim-security-wizard.md) , který vás provede počátečním zjišťováním a přiřazováním prostředí.

## <a name="enable-pim"></a>Povolení PIM

Pokud chcete začít používat PIM ve vašem adresáři, musíte nejprve povolit PIM.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce adresáře.

    Musíte být globální správce s účtem organizace (například @yourdomain.com), ne účet Microsoft (třeba @outlook.com), abyste umožnili PIM pro adresář.

1. Klikněte na tlačítko **všechny služby** a najít **Azure AD Privileged Identity Management** služby.

    ![Azure AD Privileged Identity Management ve všech služeb](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete rychlý start PIM.

1. V seznamu, klikněte na tlačítko **souhlas s PIM**.

    ![Souhlas s PIM](./media/pim-getting-started/consent-pim.png)

1. Klikněte na tlačítko **ověřit moji identitu** ověřit svoji identitu pomocí Azure MFA. Budete vyzváni k výběru účtu.

    ![Vyberte účet](./media/pim-getting-started/pick-account.png)

1. Je-li další informace jsou nezbytné k ověření, provedeme vás procesem. Další informace najdete v tématu [potřebujete pomoc s dvoustupňovým ověřováním](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Musí se zadat další informace](./media/pim-getting-started/more-information-required.png)

    Například může být požádáni o zadání ověření pomocí telefonu.

    ![Ověření pro další úroveň zabezpečení](./media/pim-getting-started/additional-security-verification.png)

1. Po dokončení procesu ověřování, klikněte na tlačítko **souhlas** tlačítko.

1. Ve zprávě, která se zobrazí, klikněte na tlačítko **Ano** souhlas ke službě PIM.

    ![Souhlas s PIM zprávy](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Zaregistrovat PIM pro role Azure AD

Jakmile pro svůj adresář povolíte PIM, budete muset zaregistrovat PIM pro role Azure AD spravovat.

1. Otevřít **Azure AD Privileged Identity Management**.

1. Klikněte na tlačítko **role Azure AD**.

    ![Zaregistrovat PIM pro role Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klikněte na tlačítko **zaregistrovat**.

1. Ve zprávě, která se zobrazí, klikněte na tlačítko **Ano** přihlášení ke správě rolí Azure AD PIM.

    ![Zaregistrovat PIM pro role zpráv služby Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po dokončení registrace se povolí možnosti Azure AD. Můžete potřebovat po aktualizaci portálu.

    Informace o tom, jak vyhledat a vybrat prostředky Azure k ochraně pomocí služby PIM, naleznete v tématu [prostředků zjistit Azure ke správě v PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení PIM můžete provádět úkoly správy identity.

![Úkoly nejvyšší úrovně v PIM – snímek obrazovky](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úlohy a spravovat | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí přiřazených vám. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše žádosti čekající na aktivaci přiřazení oprávněné role. |
| **Přístup k aplikacím** | Umožňuje omezit potenciální zpoždění a použít roli okamžitě po aktivaci. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživatelů v adresáři, které jsou určené ke schválení. |
| **Zkontrolovat přístup** | Zobrazí seznam aktivních kontrol přístupu, které jsou přiřazeny k dokončení, ať už kontrolujete přístup sobě nebo někomu jinému. |
| **Role Azure AD** | Zobrazí se řídicí panel a nastavení správci privilegovaných rolí můžou Spravovat přiřazení rolí adresáře Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |
| **Prostředky Azure** | Zobrazí se řídicí panel a nastavení správci privilegovaných rolí můžou Spravovat přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidat dlaždici PIM do řídicího panelu

Aby bylo snazší otevřete PIM, měli byste přidat PIM dlaždice na řídicí panel portálu Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Klikněte na tlačítko **všechny služby** a najít **Azure AD Privileged Identity Management** služby.

    ![Azure AD Privileged Identity Management ve všech služeb](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete rychlý start PIM.

1. Zkontrolujte **Připnout okno na řídicí panel** připnutí v okně rychlého PIM do řídicího panelu.

    ![Připnout okno na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice s tímto způsobem:

    ![Rychlý start PIM dlaždice](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další postup

- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)
- [Vyhledejte prostředky Azure ke správě v PIM](pim-resource-roles-discover-resources.md)
