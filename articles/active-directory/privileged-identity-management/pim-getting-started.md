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
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f8d41fb3e6233d319725cac924c0c0ed41b7ce4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234294"
---
# <a name="start-using-privileged-identity-management"></a>Zahájení využití technologie Privileged Identity Management

Tento článek popisuje, jak povolit Privileged Identity Management (PIM) a jak ho začít používat.

Pomocí Privileged Identity Management (PIM) můžete spravovat, řídit a monitorovat přístup v rámci vaší organizace Azure Active Directory (Azure AD). S PIM můžete poskytovat přístup k prostředkům Azure, prostředkům Azure AD a dalším službám Microsoft online služby jako je třeba sada Office 365 nebo Microsoft Intune, a to za běhu.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít Privileged Identity Management, je nutné mít jednu z následujících licencí:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Další informace najdete v tématu [licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md).

## <a name="prepare-pim-for-azure-ad-roles"></a>Příprava PIM pro role Azure AD

Jakmile pro svůj adresář povolíte Privileged Identity Management, můžete připravit Privileged Identity Management na správu rolí Azure AD.

Tady jsou úkoly, které doporučujeme pro přípravu na role Azure AD, a to v uvedeném pořadí:

1. [Nakonfigurujte nastavení role Azure AD](pim-how-to-change-default-settings.md).
1. [Poskytněte oprávněná přiřazení](pim-how-to-add-role-to-user.md).
1. [Umožněte oprávněným uživatelům aktivovat své role Azure AD za běhu](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Příprava PIM pro role Azure

Jakmile pro svůj adresář povolíte Privileged Identity Management, můžete připravit Privileged Identity Management ke správě rolí Azure pro přístup k prostředkům Azure v rámci předplatného.

Tady jsou úkoly, které doporučujeme pro přípravu na role Azure, a to v uvedeném pořadí:

1. [Zjišťování prostředků Azure](pim-resource-roles-discover-resources.md)
1. [Nakonfigurujte nastavení role Azure](pim-resource-roles-configure-role-settings.md).
1. [Poskytněte oprávněná přiřazení](pim-resource-roles-assign-roles.md).
1. [Umožněte oprávněným uživatelům aktivovat své role Azure za běhu](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Jakmile Privileged Identity Management nastavíte, můžete se naučit, jak se pohybovat.

![Navigační okno v Privileged Identity Management znázorňující úlohy a spravovat možnosti](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úkol a Správa | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí, které vám jsou přiřazeny. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše nedokončené žádosti o aktivaci přiřazení oprávněných rolí. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživateli ve vašem adresáři, který jste určili ke schválení. |
| **Kontrola přístupu** | Zobrazí seznam aktivních kontrol přístupu, které jste přiřadili k dokončení, ať už kontrolujete přístup sami nebo někomu jinému. |
| **Role Azure AD** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje jenom informace o uživateli, který přistupuje k řídicímu panelu, ne k celé organizaci. |
| **Prostředky Azure** | Zobrazí řídicí panel a nastavení pro správce privilegovaných rolí ke správě přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje jenom informace o uživateli, který přistupuje k řídicímu panelu, ne k celé organizaci. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Přidání dlaždice PIM na řídicí panel

Pokud chcete usnadnit otevírání Privileged Identity Management, přidejte do řídicího panelu Azure Portal dlaždici PIM.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **všechny služby** a vyhledejte službu **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management ve všech službách](./media/pim-getting-started/pim-all-services-find.png)

1. Vyberte Privileged Identity Management rychlý Start.

1. Chcete-li připnout okno rychlý Start Privileged Identity Management na řídicí panel, ověřte **okno připnout na řídicí panel** .

    ![Ikona připínáček pro připnutí Privileged Identity Management okna na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice podobná této:

    ![Dlaždice pro rychlý Start Privileged Identity Management na řídicím panelu](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD v Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Správa přístupu k prostředkům Azure v Privileged Identity Management](pim-resource-roles-discover-resources.md)
