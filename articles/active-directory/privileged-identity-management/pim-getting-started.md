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
ms.topic: how-to
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bcfb21ab15355653780355f1b5e459bc806ec8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90600717"
---
# <a name="start-using-privileged-identity-management"></a>Zahájení využití technologie Privileged Identity Management

Tento článek popisuje, jak povolit Privileged Identity Management (PIM) a jak ho začít používat.

Pomocí Privileged Identity Management (PIM) můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace Azure Active Directory (Azure AD). S PIM můžete poskytovat přístup k prostředkům Azure, prostředkům Azure AD a jiným online služby Microsoftu, jako je třeba Microsoft 365 nebo Microsoft Intune, pomocí přístupu za běhu.

## <a name="prerequisites"></a>Předpoklady

Chcete-li použít Privileged Identity Management, je nutné mít jednu z následujících licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

> [!Note]
> Když se uživatel, který je aktivní v privilegované roli v organizaci Azure AD s licencí Premium P2, dostane do **rolí a správců** v Azure AD a vybere roli (nebo dokonce jenom návštěvy Privileged Identity Management):
>
> - Pro organizaci automaticky povolíme PIM.
> - Jejich prostředí teď může přiřadit "pravidelnou" přiřazení role nebo přiřazení oprávněné role.
>
> Pokud je PIM povolený, nemá ve vaší organizaci žádný jiný vliv na to, co se vám bude muset starat. Nabízí další možnosti přiřazení, jako je aktivní vs – nárok s počátečním a koncovým časem. PIM taky umožňuje definovat rozsah přiřazení rolí pomocí jednotek pro správu a vlastních rolí. Pokud jste správcem globálního správce nebo privilegovaného správce role, můžete začít s několika dalšími e-maily, jako je týdenní přehled o PIM. V protokolu auditu souvisejícím s přiřazením role můžete také vidět instanční objekt MS-PIM. Jedná se o očekávanou změnu, která by neměla mít žádný vliv na váš pracovní postup.

## <a name="prepare-pim-for-azure-ad-roles"></a>Příprava PIM pro role Azure AD

Tady jsou úkoly, které doporučujeme pro přípravu Privileged Identity Management pro správu rolí Azure AD:

1. [Nakonfigurujte nastavení role Azure AD](pim-how-to-change-default-settings.md).
1. [Poskytněte oprávněná přiřazení](pim-how-to-add-role-to-user.md).
1. [Umožněte oprávněným uživatelům aktivovat své role Azure AD za běhu](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Příprava PIM pro role Azure

Tady jsou úkoly, které doporučujeme pro přípravu Privileged Identity Management pro správu rolí Azure v rámci předplatného:

1. [Zjišťování prostředků Azure](pim-resource-roles-discover-resources.md)
1. [Nakonfigurujte nastavení role Azure](pim-resource-roles-configure-role-settings.md).
1. [Poskytněte oprávněná přiřazení](pim-resource-roles-assign-roles.md).
1. [Umožněte oprávněným uživatelům aktivovat své role Azure za běhu](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Jakmile Privileged Identity Management nastavíte, můžete se naučit, jak se pohybovat.

![Navigační okno v Privileged Identity Management znázorňující úlohy a spravovat možnosti](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úkol a Správa | Description |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí, které vám jsou přiřazeny. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše nedokončené žádosti o aktivaci přiřazení oprávněných rolí. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživateli ve vašem adresáři, který jste určili ke schválení. |
| **Kontrola přístupu** | Zobrazí seznam aktivních kontrol přístupu, které jste přiřadili k dokončení, ať už kontrolujete přístup sami nebo někomu jinému. |
| **Role Azure AD** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje jenom informace o uživateli, který přistupuje k řídicímu panelu, ne k celé organizaci. |
| **Prostředky Azure** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje jenom informace o uživateli, který přistupuje k řídicímu panelu, ne k celé organizaci. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidání dlaždice PIM na řídicí panel

Pokud chcete usnadnit otevírání Privileged Identity Management, přidejte do řídicího panelu Azure Portal dlaždici PIM.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Vyberte **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Vyberte Privileged Identity Management **rychlý Start**.

1. Vyberte **připnout okno na řídicí panel** , abyste mohli připnout Privileged Identity Management stránku **rychlý Start** na řídicí panel.

    ![Ikona připínáček pro připnutí Privileged Identity Management stránky na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice podobná této:

    ![Dlaždice rychlý Start na řídicím panelu Privileged Identity Management](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Správa přístupu k prostředkům Azure v Privileged Identity Management](pim-resource-roles-discover-resources.md)
