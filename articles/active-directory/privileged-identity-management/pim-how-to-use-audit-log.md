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
ms.date: 11/13/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d46036efa04b4e0225cad6e8a70cd31ad3c10bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024173"
---
# <a name="view-audit-history-for-azure-ad-roles-in-pim"></a>Zobrazení historie auditu pro role Azure AD v PIM

Historii auditu Privileged Identity Management (PIM) můžete použít k zobrazení všech přiřazení rolí a aktivací během posledních 30 dnů pro všechny privilegované role. Pokud chcete zobrazit úplnou historii auditu aktivity ve vaší organizaci Azure Active Directory (Azure AD), včetně aktivity správce, koncového uživatele a synchronizační aktivity, můžete použít [sestavy Azure Active Directory zabezpečení a aktivita](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Určení vaší verze PIM

Od listopadu 2019 se v části Privileged Identity Management role Azure AD aktualizuje na novou verzi, která se shoduje s prostředími pro role prostředků Azure. Tím se vytvoří další funkce a také [změny stávajícího rozhraní API](azure-ad-roles-features.md#api-changes). I když je nová verze zahrnuta, postupy, které provedete v tomto článku, závisí na verzi Privileged Identity Management, kterou máte v současnosti k dispozici. Podle pokynů v této části určete, kterou verzi Privileged Identity Management máte. Po zjištění vaší verze Privileged Identity Management můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí uživatele, který je v roli [správce privilegované role](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Otevřete **Azure AD Privileged Identity Management**. Pokud máte banner v horní části stránky s přehledem, postupujte podle pokynů na kartě **Nová verze** v tomto článku. Jinak postupujte podle pokynů na kartě **předchozí verze** .

    ![Nové verze rolí Azure AD](./media/pim-how-to-add-role-to-user/pim-new-version.png)

# <a name="new-versiontabnew"></a>[Nová verze](#tab/new)

Pomocí těchto kroků zobrazíte historii auditu pro role Azure AD.

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu prostředků

Audit prostředků poskytuje zobrazení všech aktivit přidružených k rolím Azure AD.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte **audit prostředků**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu prostředků s filtry](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit aktivitu osobní role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte prostředek, pro který chcete zobrazit historii auditu.

1. Vyberte **Moje audit**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-versiontabprevious"></a>[Předchozí verze](#tab/previous)

## <a name="view-audit-history"></a>Zobrazení historie auditů

Pomocí těchto kroků zobrazíte historii auditu pro role Azure AD.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegovaných rolí](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **role Azure AD**.

1. Vyberte možnost **Historie auditu pro role adresáře**.

    V závislosti na historii auditu se zobrazí sloupcový graf spolu s celkovými aktivacemi, maximální počet aktivací za den a průměrnou aktivací za den.

    ![Historie auditu rolí adresářů](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    V dolní části stránky se zobrazí tabulka s informacemi o každé akci v dostupné historii auditu. Sloupce mají následující význam:

    | Column | Popis |
    | --- | --- |
    | Time | Při výskytu akce. |
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

1. Vyberte **Hotovo** , chcete-li zobrazit filtrovanou historii auditu.

---

## <a name="next-steps"></a>Další kroky

- [Zobrazení historie aktivit a auditu pro role prostředků Azure v Privileged Identity Management](azure-pim-resource-rbac.md)
