---
title: Začínáme používat PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit a začít používat Azure AD Privileged Identity Management (PIM) na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472856"
---
# <a name="start-using-privileged-identity-management"></a>Zahájení využití technologie Privileged Identity Management

Pomocí správy privilegovaných identit (PIM) můžete spravovat, řídit a monitorovat přístup v rámci organizace Azure Active Directory (Azure AD). Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalším online službám Microsoftu, jako je Office 365 nebo Microsoft Intune.

Tento článek popisuje, jak povolit a začít používat správu privilegovaných identit.

## <a name="prerequisites"></a>Požadavky

Chcete-li používat správu privilegovaných identit, musíte mít jednu z následujících licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace naleznete v [tématu Licenční požadavky na používání správy privilegovaných identit](subscription-requirements.md).

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrace PIM pro role Azure AD

Jakmile pro svůj adresář aktivujete správu privilegovaných identit, budete muset zaregistrovat správu privilegovaných identit, abyste mohli spravovat role Azure AD.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

    ![Registrace privilegované správy identit pro role Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Vyberte **Zaregistrovat .**

1. Ve zprávě, která se zobrazí, klikněte na **Ano** a zaregistrujte privilegovanou správu identit ke správě rolí Azure AD.

    ![Zpráva o registraci rolí privilegované identity pro Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Po dokončení registrace budou povoleny možnosti Azure AD. Možná budete muset aktualizovat portál.

    Informace o tom, jak zjistit a vybrat prostředky Azure k ochraně pomocí správy privilegovaných identit, najdete [v tématu Zjišťování prostředků Azure ke správě v privileged identity management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení správy privilegovaných identit můžete spustit úlohy správy identit.

![Navigační okno ve správě privilegovaných identit zobrazující chod úkolů a možností správy](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úkol + Správa | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam způsobilých a aktivních rolí, které jsou vám přiřazeny. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje požadavky** | Zobrazí nevyřízené žádosti o aktivaci způsobilých přiřazení rolí. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci způsobilých rolí uživatelů ve vašem adresáři, který jste určeni ke schválení. |
| **Kontrola přístupu** | Zobrazí seznam recenzí aktivního přístupu, k jehož dokončení jste přiřazeni, ať už kontrolujete přístup pro sebe nebo pro někoho jiného. |
| **Role Azure AD** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí pro správu přiřazení rolí Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |
| **Prostředky Azure** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí pro správu přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidání dlaždice PIM na řídicí panel

Chcete-li usnadnit otevření správy privilegovaných identit, přidejte dlaždici Správa privilegovaných identit na řídicí panel portálu Azure.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **Všechny služby** a najděte službu **Azure AD Privileged Identity Management.**

    ![Správa privilegovaných identit Azure AD ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Vyberte rychlý start správy privilegovaných identit.

1. **Zaškrtnutím políčka Pin na řídicí panel** připnete na řídicí panel okno Pro správu privilegovaných identit.

    ![Ikona připínáčku pro připnutí okna Privileged Identity Management na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure uvidíte dlaždici, jako je tato:

    ![Dlaždice rychlého startu správy privilegovaných identit na řídicím panelu](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD ve správě privilegovaných identit](pim-how-to-add-role-to-user.md)
- [Seznamte se s prostředky Azure, které chcete spravovat v privilegované správě identit](pim-resource-roles-discover-resources.md)
