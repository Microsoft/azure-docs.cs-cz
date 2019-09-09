---
title: Zobrazení historie auditu pro role Azure AD v PIM-Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak zobrazit historii auditu pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/10/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3547ff218e9a15151e4abe2ceff53292d3f01ac0
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804324"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Zobrazení historie auditu pro role Azure AD v PIM

Můžete použít historii auditu Azure Active Directory (Azure AD) Privileged Identity Management (PIM) k zobrazení všech přiřazení rolí a aktivací za posledních 30 dní pro všechny privilegované role. Chcete-li zobrazit úplnou historii auditu aktivity v adresáři, včetně oprávnění správce, koncový uživatel a aktivita synchronizace, můžete použít [sestavy Azure Active Directory zabezpečení a aktivita](../reports-monitoring/overview-reports.md).

## <a name="view-audit-history"></a>Zobrazení historie auditů

Pomocí těchto kroků zobrazíte historii auditu pro role Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Klikněte na **role Azure AD**.

1. Klikněte na možnost **Historie auditu pro role adresáře**.

    V závislosti na historii auditu se zobrazí sloupcový graf spolu s celkovými aktivacemi, maximální počet aktivací za den a průměrnou aktivací za den.

    ![Historie auditu rolí adresářů](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    V dolní části stránky se zobrazí tabulka s informacemi o každé akci v dostupné historii auditu. Sloupce mají následující význam:

    | Sloupec | Popis |
    | --- | --- |
    | Time | Při výskytu akce. |
    | Žadatel | Uživatel, který požádal o aktivaci nebo změnu role Pokud je hodnota **systémem Azure**, podívejte se na historii auditu Azure, kde najdete další informace. |
    | Action | Akce prováděné žadatelem. Akce mohou zahrnovat přiřazení, zrušení přiřazení, aktivace, deaktivace nebo AddedOutsidePIM. |
    | Člen | Uživatel, který se aktivuje nebo přiřadí k roli. |
    | Role | Roli přiřazenou nebo aktivovanou uživatelem. |
    | Zdůvodnění | Text, který byl zadán do pole důvod během aktivace |
    | Konec platnosti | Jakmile vyprší platnost aktivované role. Vztahuje se pouze na přiřazení oprávněných rolí. |

1. Chcete-li seřadit historii auditu, klikněte na tlačítka **čas**, **Akce**a **role** .

## <a name="filter-audit-history"></a>Filtrovat historii auditu

1. V horní části stránky Historie auditu klikněte na tlačítko **Filtr** .

    Zobrazí se podokno **aktualizovat parametry grafu** .

1. V **časovém rozsahu**vyberte časový rozsah.

1. V části **role**přidejte značky zaškrtnutí pro role, které chcete zobrazit.

    ![Aktualizovat podokno parametrů grafu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Kliknutím na **Hotovo** zobrazíte filtrovanou historii auditu.

## <a name="next-steps"></a>Další postup

- [Zobrazení historie aktivit a auditu pro role prostředků Azure v PIM](azure-pim-resource-rbac.md)
