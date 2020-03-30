---
title: Zobrazení sestavy protokolu auditu pro role Azure AD v PIM Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak zobrazit historii protokolu auditu pro role Azure AD v azure ad privilegované správy identit (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 01/07/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b8aef68e0f61e6ca995fc2bb362d59aba73ead2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329496"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Zobrazení historie auditu pro role Azure AD ve správě privilegovaných identit

Pomocí historie auditu správy privilegovaných identit (PIM) můžete zobrazit všechna přiřazení a aktivace rolí za posledních 30 dní pro všechny privilegované role. Pokud chcete zobrazit úplnou historii auditování aktivity v organizaci Azure Active Directory (Azure AD), včetně aktivity správce, koncového uživatele a synchronizace, můžete použít [sestavy zabezpečení a aktivit služby Azure Active Directory](../reports-monitoring/overview-reports.md).

## <a name="determine-your-version-of-pim"></a>Určení verze PIM

Počínaje listopadem 2019 se část rolí Azure AD privileged identity Management aktualizuje na novou verzi, která odpovídá prostředí pro role prostředků Azure. Tím se vytvoří další funkce, stejně jako [změny existující rozhraní API](azure-ad-roles-features.md#api-changes). Při zavádění nové verze, které postupy, které budete postupovat v tomto článku, závisí na verzi správy privilegovaných identit, kterou aktuálně máte. Podle pokynů v této části určete, kterou verzi správy privilegovaných identit máte. Poté, co znáte verzi správy privilegovaných identit, můžete vybrat postupy v tomto článku, které odpovídají této verzi.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je v roli [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)
1. Otevřete **správu privilegovaných identit Azure AD**. Pokud máte v horní části stránky přehledu nápis, postupujte podle pokynů na kartě **Nová verze** tohoto článku. V opačném případě postupujte podle pokynů na kartě **Předchozí verze.**

    [![Azure AD role nová verze](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Výběr karty pro vaši verzi")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

# <a name="new-version"></a>[Nová verze](#tab/new)

Podle těchto kroků zobrazte historii auditu pro role Azure AD.

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu zdrojů

Audit prostředků poskytuje zobrazení všech aktivit spojených s vašimi rolemi Azure AD.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte **příkaz Audit zdrojů**.

1. Historie můžete filtrovat pomocí předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditů zdrojů s filtry](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit vaši osobní aktivitu role.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte zdroj, pro který chcete zobrazit historii auditu.

1. Vyberte **možnost Můj audit**.

1. Historie můžete filtrovat pomocí předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png)

# <a name="previous-version"></a>[Předchozí verze](#tab/previous)

## <a name="view-audit-history"></a>Zobrazení historie auditů

Podle těchto kroků zobrazte historii auditu pro role Azure AD.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) s uživatelem, který je členem role [správce privilegované role.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **role Azure AD**.

1. Vyberte **historii auditu rolí adresářů**.

    V závislosti na historii auditu se zobrazí sloupcový graf spolu s celkovým počtem aktivací, maximálními aktivacemi za den a průměrnými aktivacemi za den.

    [![Azure AD role nová verze](media/pim-how-to-use-audit-log/directory-roles-audit-history.png "Zobrazit historii auditu rolí adresářů")](media/pim-how-to-use-audit-log/directory-roles-audit-history.png)

    V dolní části stránky se zobrazí tabulka s informacemi o každé akci v historii auditu, která je k dispozici. Sloupce mají následující významy:

    | Sloupec | Popis |
    | --- | --- |
    | Time | Kdy došlo k akci. |
    | Requestor | Uživatel, který požádal o aktivaci nebo změnu role. Pokud je hodnota **Azure System**, zkontrolujte historii auditu Azure pro další informace. |
    | Akce | Opatření přijatá žadatelem. Akce mohou zahrnovat přiřadit, Zrušit přiřazení, Aktivovat, Deaktivovat nebo AddedOutsidePIM. |
    | Člen | Uživatel, který aktivuje nebo je přiřazen k roli. |
    | Role | Role přiřazená nebo aktivovaná uživatelem. |
    | Uvažování | Text, který byl zadán do pole důvod během aktivace. |
    | Konec platnosti | Když vyprší platnost aktivované role. Platí pouze pro způsobilá přiřazení rolí. |

1. Chcete-li seřadit historii auditu, klepněte na tlačítka **Čas**, **Akce**a **Role.**

## <a name="filter-audit-history"></a>Historie auditu filtru

1. V horní části stránky historie auditu klikněte na tlačítko **Filtrovat.**

    Zobrazí se podokno **Aktualizovat parametry grafu.**

1. V **časovém rozsahu**vyberte časový rozsah.

1. V **části Role**zaškrtněte políčka, která označují role, které chcete zobrazit.

    ![Podokno Aktualizovat parametry grafu](media/pim-how-to-use-audit-log/update-chart-parameters.png)

1. Chcete-li zobrazit filtrovanou historii auditu, vyberte **možnost Hotovo.**

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Získat důvod, schvalovatele a číslo lístku pro události schválení

1. Přihlaste se k [portálu Azure](https://aad.portal.azure.com) s oprávněními role správce privilegovaných rolí a otevřete Azure AD.
1. Vyberte **Protokoly auditování**.
1. Filtr **Služby** slouží k zobrazení pouze událostí auditování pro službu Správa privilegovaných identit. Na stránce **Protokoly auditu** můžete:

    - Důvod události auditu naleznete ve sloupci **Důvod stavu.**
    - Naleznete schvalovatel ve **sloupci Iniciováno (objekt actor)** pro událost "přidat člena do žádosti o roli schváleno".

    [![Azure AD role nová verze](media/pim-how-to-use-audit-log/filter-audit-logs.png "Filtrování protokolu auditu pro službu PIM")](media/pim-how-to-use-audit-log/filter-audit-logs.png)

1. Výběrem události protokolu auditu zobrazíte číslo lístku na kartě **Aktivita** v podokně **Podrobnosti.**
  
    [![Azure AD role nová verze](media/pim-how-to-use-audit-log/audit-event-ticket-number.png "Zkontrolujte číslo lístku pro událost auditu")](media/pim-how-to-use-audit-log/audit-event-ticket-number.png)

1. Žadatel (osoba, která roli aktivovala) můžete zobrazit na kartě **Cíle** v podokně **Podrobnosti** pro událost auditu. Existují dva typy cílů pro role Azure AD:

    - Role (**Typ** = Role)
    - Žadatel (**Typ** = Uživatel)

Událost protokolu auditu bezprostředně nad událostí schválení je obvykle událost pro "Přidat člena do role dokončena", kde **Iniciována (actor)** je žadatel. Ve většině případů nebudete muset najít uchazeče v žádosti o schválení z hlediska auditování.

---

## <a name="next-steps"></a>Další kroky

- [Zobrazení historie aktivit a auditu rolí prostředků Azure v privilegované správě identit](azure-pim-resource-rbac.md)
