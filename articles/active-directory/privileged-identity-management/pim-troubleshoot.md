---
title: Řešení potíží s Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Naučte se řešit chyby systému pomocí rolí v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7fc3508f7f672e277577f92218ff1860b676cb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559473"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Řešení potíží s Privileged Identity Management

Máte potíže s Privileged Identity Management (PIM) v Azure Active Directory (Azure AD)? Následující informace vám můžou pomohou znovu pracovat.

## <a name="access-to-azure-resources-denied"></a>Přístup k prostředkům Azure byl odepřen.

### <a name="problem"></a>Problém

Při pokusu o vytvoření uživatele s nárokem na roli správce Azure AD se zobrazí chyba autorizace a v rámci Privileged Identity Management nebudete moci získat přístup k prostředkům Azure. Nemůžete získat přístup k prostředkům Azure pod Privileged Identity Management ani v případě, že jste globálním správcem a vlastníkem předplatného.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud byla role správce přístupu uživatele pro instanční objekt PIM omylem odebrána z předplatného. Aby služba Privileged Identity Management mohla získat přístup k prostředkům Azure, musí být vždy přiřazena [role správce přístupu k uživateli](../../role-based-access-control/built-in-roles.md#user-access-administrator) v rámci předplatného služby Azure.

### <a name="resolution"></a>Rozlišení

Přiřaďte roli správce přístupu uživatele k hlavnímu názvu služby Privileged Identity Management (MS-PIM) na úrovni předplatného. Toto přiřazení by mělo službě Privileged Identity Management umožňovat přístup k prostředkům Azure. Role se dá přiřadit na úrovni skupiny pro správu nebo na úrovni předplatného v závislosti na vašich požadavcích. Další informace o instančních objektech najdete v tématu [přiřazení aplikace k roli](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Další kroky

- [Licenční požadavky pro použití Privileged Identity Management](subscription-requirements.md)
- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Nasazení Privileged Identity Management](pim-deployment-plan.md)
