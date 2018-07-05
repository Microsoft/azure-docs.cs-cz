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
ms.date: 09/12/2017
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: bdc8754253ce2567d957b4d6240fe52242aea2ea
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449758"
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
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Představení služby Azure Active Directory](active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../connect/active-directory-aadconnect.md)
