---
title: Další kroky pro správu přístupu pomocí skupin, Azure AD | Dokumentace Microsoftu
description: Jak pokročilé – pro správu skupin zabezpečení a jak používat tyto skupiny pro správu přístupu k prostředku.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: d84c3b556f27366c249382a37e0a8227726110cf
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714680"
---
# <a name="managing-owners-for-a-group"></a>Správa vlastníků pro skupinu
Jakmile vlastníka prostředku má přidělený přístup k prostředku na skupiny Azure AD, členství ve skupině spravuje ho vlastník skupiny. Vlastník prostředku deleguje efektivní oprávnění pro přiřazení uživatelů k prostředku na vlastníka skupiny.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny

1. V [centrum pro správu Azure AD](https://aad.portal.azure.com)vyberte **uživatelů a skupin**.
2. Vyberte **všechny skupiny**a pak otevřete skupinu, kterou chcete přidat vlastníky.
3. Vyberte **přidat vlastníky**.
4. Na **přidat vlastníky** vyberte uživatele, který chcete přidat jako vlastníka této skupiny a ujistěte se, že toto jméno přidat i do **vybrané** podokně.

## <a name="remove-an-owner-from-a-group"></a>Odebrání vlastníka ze skupiny

1. V [centrum pro správu Azure AD](https://aad.portal.azure.com)vyberte **uživatelů a skupin**.
2. Vyberte **všechny skupiny**a pak otevřete skupinu, ze kterého chcete odebrat vlastníky.
3. Vyberte **vlastníky** kartu.
4. Vyberte vlastníka, který chcete z této skupiny odebrat a pak vyberte **odebrat**.

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)
* [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md)
* [Správa aplikací v Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Představení služby Azure Active Directory](active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../connect/active-directory-aadconnect.md)
