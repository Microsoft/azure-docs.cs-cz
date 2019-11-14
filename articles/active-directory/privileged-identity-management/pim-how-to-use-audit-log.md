---
title: Zobrazit sestavu auditu pro role Azure AD v PIM – Azure AD | Microsoft Docs
description: Přečtěte si, jak zobrazit historii auditu pro role Azure AD v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c4a157d8d5bcd281ca9fee488e58c455034e898
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022066"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Zobrazení historie auditu pro role Azure AD v PIM

Historii auditu Privileged Identity Management (PIM) můžete použít k zobrazení všech přiřazení rolí a aktivací během posledních 30 dnů pro všechny privilegované role. Pokud chcete zobrazit úplnou historii auditu aktivity ve vaší organizaci Azure Active Directory (Azure AD), včetně aktivity správce, koncového uživatele a synchronizační aktivity, můžete použít [sestavy Azure Active Directory zabezpečení a aktivita](../reports-monitoring/overview-reports.md).

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
    | Čas | Při výskytu akce. |
    | Žadatele | Uživatel, který požádal o aktivaci nebo změnu role Pokud je hodnota **systémem Azure**, podívejte se na historii auditu Azure, kde najdete další informace. |
    | Akce | Akce prováděné žadatelem. Akce mohou zahrnovat přiřazení, zrušení přiřazení, aktivace, deaktivace nebo AddedOutsidePIM. |
    | Člen | Uživatel, který se aktivuje nebo přiřadí k roli. |
    | Role | Roli přiřazenou nebo aktivovanou uživatelem. |
    | Uvažování | Text, který byl zadán do pole důvod během aktivace |
    | Vypršení platnosti | Jakmile vyprší platnost aktivované role. Vztahuje se pouze na přiřazení oprávněných rolí. |

1. Chcete-li seřadit historii auditu, klikněte na tlačítka **čas**, **Akce**a **role** .

## <a name="filter-audit-history"></a>Filtrovat historii auditu

1. V horní části stránky Historie auditu klikněte na tlačítko **Filtr** .

    Zobrazí se podokno **aktualizovat parametry grafu** .

1. V **časovém rozsahu**vyberte časový rozsah.

1. V části **role**zaškrtněte políčka pro určení rolí, které chcete zobrazit.

    ![Aktualizovat podokno parametrů grafu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Kliknutím na **Hotovo** zobrazíte filtrovanou historii auditu.

## <a name="next-steps"></a>Další kroky

- [Zobrazení historie aktivit a auditu pro role prostředků Azure v Privileged Identity Management](azure-pim-resource-rbac.md)
