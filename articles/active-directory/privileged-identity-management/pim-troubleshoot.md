---
title: Poradce při potížích se správou privilegovaných identit – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit systémové chyby s rolemi v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299665"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Poradce při potížích se správou privilegovaných identit

Máte potíže se správou privilegovaných identit (PIM) ve službě Azure Active Directory (Azure AD)? Následující informace vám mohou pomoci znovu zpracovat věci.

## <a name="access-to-azure-resources-denied"></a>Přístup k prostředkům Azure byl odepřen.

### <a name="problem"></a>Problém

Jako aktivní vlastník nebo správce přístupu uživatelů pro prostředek Azure můžete zobrazit prostředek v rámci správy privilegovaných identit, ale nemůžete provádět žádné akce, jako je vytvoření způsobilého přiřazení nebo zobrazení seznamu přiřazení rolí z prostředku. přehledové stránky. Každá z těchto akcí má za následek chybu autorizace.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, když role správce přístupu uživatele pro instanční objekt PIM byl omylem odebrán z předplatného. Aby služba Privileged Identity Management mohla přistupovat k prostředkům Azure, měl by mít vždy [přiřazena role správce přístupu uživatelů](../../role-based-access-control/built-in-roles.md#user-access-administrator) přes předplatné Azure.

### <a name="resolution"></a>Řešení

Přiřaďte roli Správce přístupu uživatelů hlavnímu názvu služby Správa privilegovaných identit (MS–PIM) na úrovni předplatného. Toto přiřazení by mělo umožnit službě Správa privilegovaných identit přístup k prostředkům Azure. Roli lze přiřadit na úrovni skupiny pro správu nebo na úrovni předplatného, v závislosti na vašich požadavcích. Další informace o instančních objektech naleznete [v tématu Přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application).

## <a name="next-steps"></a>Další kroky

- [Licenční požadavky na používání správy privilegovaných identit](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)
