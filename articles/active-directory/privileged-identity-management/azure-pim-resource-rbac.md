---
title: Zobrazit sestavu auditu pro role prostředků Azure v Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Podívejte se na historii aktivit a auditu pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743146"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Zobrazení historie aktivit a auditu pro role prostředků Azure v Privileged Identity Management

S Azure Active Directory (Azure AD) Privileged Identity Management (PIM) můžete zobrazit aktivity, aktivace a historii auditu pro role prostředků Azure v rámci vaší organizace. Patří sem předplatná, skupiny prostředků a i virtuální počítače. Všechny prostředky v rámci Azure Portal, které využívají funkci řízení přístupu založené na rolích Azure, můžou využívat možnosti správy zabezpečení a životního cyklu v Privileged Identity Management.

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

## <a name="view-activity-and-activations"></a>Zobrazit aktivity a aktivace

Pokud chcete zjistit, jaké akce určitý uživatel trval v různých prostředcích, můžete zobrazit aktivitu prostředků Azure, která je přidružená k danému období aktivace.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, pro který chcete zobrazit aktivitu a aktivace.

1. Vyberte **role** nebo **členy**.

1. Vyberte uživatele.

    Zobrazí se souhrn akcí uživatele v prostředcích Azure podle data. Zobrazuje také poslední aktivaci rolí v rámci tohoto časového období.

    ![Podrobnosti o uživateli s přehledem aktivit prostředků a aktivací rolí](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Pokud chcete zobrazit podrobnosti a odpovídající aktivity prostředků Azure, ke kterým došlo v době, kdy byl tento uživatel aktivní, vyberte konkrétní aktivaci role.

    [![Vybraná aktivace role a podrobnosti o aktivitě](media/azure-pim-resource-rbac/export-membership.png "Vybraná aktivace role a podrobnosti o aktivitě")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Export přiřazení rolí s podřízenými položkami

Můžete mít požadavek na dodržování předpisů, kde musíte poskytnout úplný seznam přiřazení rolí auditorům. Privileged Identity Management vám umožní dotazovat se na přiřazení rolí u konkrétního prostředku, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dřív bylo obtížné, aby správci získali úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek. Pomocí Privileged Identity Management se můžete dotazovat na všechna aktivní a oprávněná přiřazení rolí v rámci předplatného, včetně přiřazení rolí pro všechny skupiny prostředků a prostředky.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, pro který chcete exportovat přiřazení rolí, jako je například předplatné.

1. Vyberte možnost **Členové**.

1. Výběrem **exportovat** otevřete podokno exportovat členství.

    [![Exportovat podokno členství pro export všech členů](media/azure-pim-resource-rbac/export-membership.png "Exportovat stránku členství pro export všech členů")](media/azure-pim-resource-rbac/export-membership.png)

1. Vyberte **exportovat všechny členy** a exportujte všechna přiřazení rolí v souboru CSV.

    ![Export přiřazení rolí v souboru CSV jako zobrazení v Excelu](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu prostředků

Audit prostředků poskytuje zobrazení všech aktivit role pro určitý prostředek.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, pro který chcete zobrazit historii auditu.

1. Vyberte **audit prostředků**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    [![Seznam auditu prostředků s filtry](media/azure-pim-resource-rbac/rbac-resource-audit.png "Seznam auditu prostředků s filtry")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Jako **typ auditu**vyberte **aktivovat (přiřazeno + aktivované)**.

    [![Seznam auditu prostředků filtrovaný podle typu](media/azure-pim-resource-rbac/rbac-audit-activity.png "Seznam auditu prostředků filtrovaný podle aktivace")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ aktivovat audit Seznam auditu prostředků, který je filtrovaný podle typu aktivovat audit](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. V části **Akce**klikněte na **(aktivita)** pro uživatele a podívejte se na podrobnosti o aktivitách tohoto uživatele v prostředcích Azure.

    ![Podrobnosti o aktivitě uživatele pro určitou akci](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

Můj audit umožňuje zobrazit aktivitu osobní role.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **prostředky Azure**.

1. Vyberte prostředek, pro který chcete zobrazit historii auditu.

1. Vyberte **Moje audit**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    [![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png "Seznam auditu pro aktuálního uživatele")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Přístup k historii auditu vyžaduje roli správce globálního správce nebo privilegované role.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Získat důvod, schvalovatele a číslo lístku pro události schválení

1. Přihlaste se k [Azure Portal](https://aad.portal.azure.com) s oprávněními role správce privilegované role a otevřete službu Azure AD.
1. Vyberte **Protokoly auditování**.
1. Pomocí filtru **služeb** můžete zobrazit pouze události auditu pro službu Privileged Identity Management. Na stránce **protokoly auditu** můžete:

    - Podívejte se na důvod události auditu ve sloupci **Důvod stavu** .
    - U události "přidat člena do žádosti o roli" schválíme zobrazení schvalovatele ve sloupci **Volaný (objekt actor)** .

    [![Filtrování protokolu auditu pro službu PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrování protokolu auditu pro službu PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Vyberte událost protokolu auditu a na kartě **aktivita** v podokně **podrobností** se zobrazí číslo lístku.
  
    [![Zkontrolujte číslo lístku pro událost auditu](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Zkontrolujte číslo lístku pro událost auditu.")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Žadatele (osoba aktivující roli) si můžete prohlédnout na kartě **cíle** v podokně **podrobností** pro událost auditu. Existují tři cílové typy pro role prostředků Azure:

    - Role (**Type** = role)
    - Žadatel (**typ** = jiné)
    - Schvalovatel (**typ** = uživatel)

    [![Zkontroluje cílový typ.](media/azure-pim-resource-rbac/audit-event-target-type.png "Zkontroluje cílový typ.")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Obvykle událost protokolu hned nad událostí schválení je událost pro přidání člena do role dokončeno, kde **iniciuje (actor)** žadatel. Ve většině případů nebudete muset žadatele v rámci žádosti o schválení najít v perspektivě pro auditování.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí prostředků Azure v Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Schválení nebo zamítnutí žádostí o role prostředků Azure v Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Zobrazit historii auditu pro role Azure AD v Privileged Identity Management](pim-how-to-use-audit-log.md)
