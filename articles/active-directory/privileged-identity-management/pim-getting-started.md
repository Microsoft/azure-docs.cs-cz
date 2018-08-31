---
title: Začněte používat PIM – Azure | Dokumentace Microsoftu
description: Zjistěte, jak začít používat Azure AD Privileged Identity Management (PIM) na webu Azure Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190085"
---
# <a name="start-using-pim"></a>Zahájení práce s využitím PIM

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM), můžete spravovat, řídit a monitorovat přístup ve vaší organizaci. Tento obor zahrnuje přístup k prostředkům Azure, Azure AD a dalších online služeb Microsoftu jako Office 365 nebo Microsoft Intune.

Tento článek vysvětluje, jak přidat aplikaci Azure AD PIM do řídicího panelu webu Azure Portal.

## <a name="first-person-to-use-pim"></a>První, kdo používat PIM

Pokud jste první, kdo ve vašem adresáři používat PIM, se automaticky přiřazují [správce zabezpečení](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) a [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) v daném adresáři. Pouze správci privilegovaných rolí můžou spravovat přiřazení rolí uživatelů adresáře Azure AD. Kromě toho můžete spustit [průvodce zabezpečením](pim-security-wizard.md), který vás provede počátečním zjišťováním a přiřazováním.

## <a name="add-pim-tile-to-the-dashboard"></a>Přidat dlaždici s PIM do řídicího panelu

Aby bylo snazší otevřete PIM, měli byste přidat PIM dlaždice na řídicí panel portálu Azure.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce adresáře.

1. Klikněte na tlačítko **všechny služby** a najít **Azure AD Privileged Identity Management** služby.

    ![Azure AD Privileged Identity Management ve všech služeb](./media/pim-getting-started/pim-all-services-find.png)

1. Kliknutím otevřete rychlý start PIM.

1. Zkontrolujte **Připnout okno na řídicí panel** připnutí v okně rychlého PIM do řídicího panelu.

    ![Připnout okno na řídicí panel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na řídicím panelu Azure se zobrazí dlaždice s tímto způsobem:

    ![Rychlý start PIM dlaždice](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Přechod k úkolům

Po nastavení PIM můžete toto okno provádět úkoly správy identity.

![Úkoly nejvyšší úrovně v PIM – snímek obrazovky](./media/pim-getting-started/pim-quickstart-tasks.png)

| Úlohy a spravovat | Popis |
| --- | --- |
| **Moje role**  | Zobrazí seznam oprávněných a aktivních rolí přiřazených vám. Toto je místo, kde můžete aktivovat jakoukoli přiřazenou oprávněnou roli. |
| **Moje žádosti** | Zobrazí vaše žádosti čekající na aktivaci přiřazení oprávněné role. |
| **Přístup k aplikacím** | Umožňuje omezit potenciální zpoždění a použít roli okamžitě po aktivaci. |
| **Schvalování žádostí** | Zobrazí seznam žádostí o aktivaci oprávněných rolí uživatelů v adresáři, které jsou určené ke schválení. |
| **Zkontrolovat přístup** | Zobrazí seznam aktivních kontrol přístupu, které jsou přiřazeny k dokončení, ať už kontrolujete přístup sobě nebo někomu jinému. |
| **Role adresáře Azure AD** | Zobrazí se řídicí panel a nastavení správci privilegovaných rolí můžou Spravovat přiřazení rolí adresáře Azure AD. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |
| **Prostředky Azure** | Zobrazí se řídicí panel a nastavení správci privilegovaných rolí můžou Spravovat přiřazení rolí prostředků Azure. Tento řídicí panel se zobrazuje jen správcům privilegovaných rolí. Ostatní uživatelé mají přístup ke speciálnímu řídicímu panelu s názvem Moje zobrazení. Řídicí panel Moje zobrazení zobrazuje informace pouze o uživateli přistupujícím k řídicím panelu, ne pro celého tenanta. |

## <a name="next-steps"></a>Další postup

- [Aktivovat Moje role adresáře Azure AD v PIM](pim-how-to-activate-role.md)
- [Aktivovat Moje role prostředků Azure v PIM](pim-resource-roles-activate-your-roles.md)
