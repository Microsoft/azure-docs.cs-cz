---
title: Zobrazení sestavy auditu pro role prostředků Azure v privilegované správě identit (PIM) – Azure AD | Dokumenty společnosti Microsoft
description: Zobrazení historie aktivit a auditu pro role prostředků Azure v privilegované správě identit Azure AD (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bf65ad595fb1ab70eb6613b6d54ac2a4f69141e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329576"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Zobrazení historie aktivit a auditu rolí prostředků Azure v privilegované správě identit

Pomocí azure active directory (Azure AD) privileged identity management (PIM), můžete zobrazit aktivity, aktivace a historie auditu pro role prostředků Azure ve vaší organizaci. To zahrnuje předplatná, skupiny prostředků a dokonce i virtuální počítače. Každý prostředek v rámci portálu Azure, který využívá funkce řízení přístupu na základě rolí Azure, může využít možnosti správy zabezpečení a životního cyklu ve službě Správa privilegovaných identit.

> [!NOTE]
> Pokud vaše organizace zadala funkce správy poskytovateli služeb, který používá [azure delegovanou správu prostředků](../../lighthouse/concepts/azure-delegated-resource-management.md), přiřazení rolí autorizovaná tímto poskytovatelem služeb se zde nezobrazí.

## <a name="view-activity-and-activations"></a>Zobrazit aktivitu a aktivace

Chcete-li zjistit, jaké akce konkrétní uživatel provedl v různých prostředcích, můžete zobrazit aktivitu prostředků Azure, která je přidružena k danému období aktivace.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte zdroj, pro který chcete zobrazit aktivitu a aktivace.

1. Vyberte **role** nebo **členy**.

1. Vyberte uživatele.

    Zobrazí se souhrn akcí uživatele v prostředcích Azure podle data. Zobrazuje také nedávné aktivace rolí za stejné časové období.

    ![Podrobnosti o uživateli se souhrnem aktivity prostředků a aktivacemi rolí](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Vyberte aktivaci konkrétní role, chcete-li zobrazit podrobnosti a odpovídající aktivitu prostředků Azure, ke kterým došlo, když byl uživatel aktivní.

    [![Vybraná aktivace role a podrobnosti o aktivitě](media/azure-pim-resource-rbac/export-membership.png "Vybraná aktivace role a podrobnosti o aktivitě")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Export přiřazení rolí s podřízenými funkcemi

Můžete mít požadavek na dodržování předpisů, kde je nutné poskytnout úplný seznam přiřazení rolí auditorům. Správa privilegovaných identit umožňuje dotazovat přiřazení rolí u určitého prostředku, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dříve bylo pro správce obtížné získat úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek. Pomocí správy privilegovaných identit můžete dotazovat na všechna aktivní a způsobilá přiřazení rolí v předplatném včetně přiřazení rolí pro všechny skupiny prostředků a prostředky.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, pro který chcete exportovat přiřazení rolí, například předplatné.

1. Vyberte možnost **Členové**.

1. Vyberte **Exportovat,** chcete-li otevřít podokno Exportčlenství.

    [![Podokno exportu členství pro export všech členů](media/azure-pim-resource-rbac/export-membership.png "Exportovat stránku členství pro export všech členů")](media/azure-pim-resource-rbac/export-membership.png)

1. Vyberte **Exportovat všechny členy,** chcete-li exportovat všechna přiřazení rolí v souboru CSV.

    ![Exportovaná přiřazení rolí v souboru CSV jako zobrazení v Excelu](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu zdrojů

Audit zdrojů poskytuje zobrazení všech aktivit role zdroje.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte zdroj, pro který chcete zobrazit historii auditu.

1. Vyberte **příkaz Audit zdrojů**.

1. Historie můžete filtrovat pomocí předdefinovaného data nebo vlastního rozsahu.

    [![Seznam auditů zdrojů s filtry](media/azure-pim-resource-rbac/rbac-resource-audit.png "Seznam auditů zdrojů s filtry")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Pro **typ auditu**vyberte **Aktivovat (Přiřazeno + Aktivováno)**.

    [![Seznam auditů zdrojů filtrovaný podle aktivace typu](media/azure-pim-resource-rbac/rbac-audit-activity.png "Seznam auditů zdrojů filtrovaný podle aktivace")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![auditu Seznam auditu zdrojů, který je filtrován podle typu Aktivace auditu](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. V **části Akce**klikněte na **(aktivita)** pro uživatele, abyste viděli podrobnosti o aktivitě tohoto uživatele ve prostředcích Azure.

    ![Podrobnosti o aktivitě uživatele pro konkrétní akci](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit vaši osobní aktivitu role.

1. Otevřete **správu privilegovaných identit Azure AD**.

1. Vyberte **prostředky Azure**.

1. Vyberte zdroj, pro který chcete zobrazit historii auditu.

1. Vyberte **možnost Můj audit**.

1. Historie můžete filtrovat pomocí předdefinovaného data nebo vlastního rozsahu.

    [![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png "Seznam auditu pro aktuálního uživatele")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Přístup k historii auditu vyžaduje roli globálního správce nebo správce privilegovaných rolí.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Získat důvod, schvalovatele a číslo lístku pro události schválení

1. Přihlaste se k [portálu Azure](https://aad.portal.azure.com) s oprávněními role správce privilegovaných rolí a otevřete Azure AD.
1. Vyberte **Protokoly auditování**.
1. Filtr **Služby** slouží k zobrazení pouze událostí auditování pro službu Správa privilegovaných identit. Na stránce **Protokoly auditu** můžete:

    - Důvod události auditu naleznete ve sloupci **Důvod stavu.**
    - Naleznete schvalovatel ve **sloupci Iniciováno (objekt actor)** pro událost "přidat člena do žádosti o roli schváleno".

    [![Filtrování protokolu auditu pro službu PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrování protokolu auditu pro službu PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Výběrem události protokolu auditu zobrazíte číslo lístku na kartě **Aktivita** v podokně **Podrobnosti.**
  
    [![Zkontrolujte číslo lístku pro událost auditu](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Zkontrolujte číslo lístku pro událost auditu")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Žadatel (osoba, která roli aktivovala) můžete zobrazit na kartě **Cíle** v podokně **Podrobnosti** pro událost auditu. Existují tři cílové typy pro role prostředků Azure:

    - Role (**Typ** = Role)
    - Žadatel (**Typ** = Ostatní)
    - Schvalovatel (**Typ** = Uživatel)

    [![Kontrola typu cíle](media/azure-pim-resource-rbac/audit-event-target-type.png "Kontrola typu cíle")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Událost protokolu bezprostředně nad událostí schválení je událost pro "Přidat člena do role dokončena", kde **Initiated by (actor)** je žadatel. Ve většině případů nebudete muset najít uchazeče v žádosti o schválení z hlediska auditování.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure ve správě privilegovaných identit](pim-resource-roles-assign-roles.md)
- [Schválení nebo zamítnutí požadavků na role prostředků Azure v privilegované správě identit](pim-resource-roles-approval-workflow.md)
- [Zobrazení historie auditu pro role Azure AD ve správě privilegovaných identit](pim-how-to-use-audit-log.md)
