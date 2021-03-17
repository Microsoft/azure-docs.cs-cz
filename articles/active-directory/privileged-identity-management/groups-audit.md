---
title: Zobrazit sestavu auditu pro přiřazení privilegovaných skupin přístupu v Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Zobrazení historie aktivit a auditu pro přiřazení privilegovaných skupin přístupu v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141665"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Historie auditu aktivity pro přiřazení privilegovaných skupin přístupu (Preview) v Privileged Identity Management

S Privileged Identity Management (PIM) můžete zobrazit aktivity, aktivace a historii auditu pro členy skupiny privilegovaný přístup Azure a vlastníky v rámci vaší organizace Azure Active Directory (Azure AD).

> [!NOTE]
> Pokud má vaše organizace samoobslužné funkce správy pro poskytovatele služeb, který používá [správu delegovaných prostředků Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), tady se nezobrazí přiřazení rolí autorizovaných tímto poskytovatelem služeb.

Pomocí těchto kroků zobrazíte historii auditu pro privilegovaný přístup skupiny.

## <a name="view-resource-audit-history"></a>Zobrazit historii auditu prostředků

**Audit prostředků** vám poskytne přehled o všech aktivitách přidružených k vašim privilegovaným skupinám přístupu.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **skupiny Privileged Access groups (Preview)**.

1. Vyberte skupinu privilegovaný přístup, pro kterou chcete zobrazit historii auditu.

1. V části **aktivita**vyberte **audit prostředků**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu prostředků s filtry](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Zobrazit můj audit

**Můj audit** umožňuje zobrazit aktivitu osobní role pro privilegovaný přístupovou skupinu.

1. Otevřete **Azure AD Privileged Identity Management**.

1. Vyberte **skupiny Privileged Access groups (Preview)**.

1. Vyberte skupinu privilegovaný přístup, pro kterou chcete zobrazit historii auditu.

1. V části **aktivita**vyberte **Moje audit**.

1. Filtruje historii s použitím předdefinovaného data nebo vlastního rozsahu.

    ![Seznam auditu pro aktuálního uživatele](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Další kroky

- [Přiřazení členství ve skupině a vlastnictví v Privileged Identity Management](groups-assign-member-owner.md)
- [Schválení nebo zamítnutí požadavků pro privilegované skupiny přístupu v PIM](groups-approval-workflow.md)
- [Zobrazení historie auditu pro role Azure AD v PIM](groups-audit.md)
