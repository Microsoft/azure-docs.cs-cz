---
title: Řešení potíží s Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se řešit chyby systému pomocí rolí v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: b02b8f3b94d191c7439aadf5a56ec0e2a1c38e28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782494"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Řešení potíží s Privileged Identity Management

Máte potíže s Privileged Identity Management (PIM) v Azure Active Directory (Azure AD)? Následující informace vám můžou pomohou znovu pracovat.

## <a name="access-to-azure-resources-denied"></a>Přístup k prostředkům Azure byl odepřen.

### <a name="problem"></a>Problém

Jako aktivní vlastník nebo správce přístupu uživatelů pro prostředek Azure můžete prostředek zobrazit v rámci Privileged Identity Management ale nemůžete provádět žádné akce, jako je třeba vytvoření oprávněného přiřazení nebo zobrazení seznamu přiřazení rolí na stránce Přehled prostředků. Každá z těchto akcí má za následek chybu autorizace.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud byla role správce přístupu uživatele pro instanční objekt PIM omylem odebrána z předplatného. Aby služba Privileged Identity Management mohla získat přístup k prostředkům Azure, musí být vždy přiřazena [role správce přístupu k uživateli](../../role-based-access-control/built-in-roles.md#user-access-administrator) v rámci předplatného služby Azure.

### <a name="resolution"></a>Řešení

Přiřaďte roli správce přístupu uživatele k hlavnímu názvu služby Privileged Identity Management (MS-PIM) na úrovni předplatného. Toto přiřazení by mělo službě Privileged Identity Management umožňovat přístup k prostředkům Azure. Role se dá přiřadit na úrovni skupiny pro správu nebo na úrovni předplatného v závislosti na vašich požadavcích. Další informace o instančních objektech najdete v tématu [přiřazení aplikace k roli](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application).

## <a name="next-steps"></a>Další kroky

- [Licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení technologie Privileged Identity Management](pim-deployment-plan.md)