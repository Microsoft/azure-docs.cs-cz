---
title: Dynamické skupiny a spolupráce Azure Active Directory s B2B | Dokumentace Microsoftu
description: Ukazuje, jak používat dynamické skupiny Azure AD se spoluprací Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 287c5a75d1c15a9ebc7a23d263ce7ff5516bcfab
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981860"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamické skupiny a spolupráce Azure Active Directory s B2B

## <a name="what-are-dynamic-groups"></a>Co jsou dynamické skupiny?
Dynamická konfigurace členství ve skupině zabezpečení Azure Active Directory (Azure AD) je k dispozici v [na webu Azure portal](https://portal.azure.com). Správci můžou nastavit pravidla pro naplnění skupin, které jsou vytvořeny ve službě Azure AD podle uživatelských atributů (jako je například userType, oddělení nebo země). Členy můžete přidat do automaticky nebo odebral ze skupiny zabezpečení na základě jejich atributů. Tyto skupiny můžete poskytovat přístup k aplikacím nebo cloudovým prostředkům (Sharepointové weby, dokumenty) a k přiřazení licencí ke členům. Další informace o dynamické skupiny v [vyhrazené skupiny v Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Odpovídající [licence Azure AD Premium P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/) je nutná k vytváření a používání dynamických skupin. Další informace najdete v článku [vytváření pravidel založených na atributech pro členství v dynamické skupině v Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Co jsou předdefinované dynamické skupiny?
**Všichni uživatelé** dynamické skupiny umožňuje správci tenanta k vytvoření skupiny obsahující všechny uživatele v tenantovi jediným kliknutím. Ve výchozím nastavení **všichni uživatelé** skupina obsahuje všechny uživatele v adresáři, včetně členy a hosty.
V rámci nový portál pro správu Azure Active Directory, můžete povolit **všichni uživatelé** skupiny v části Nastavení skupiny.

![Ukazuje povolit skupinu všichni uživatelé nastavenou na Ano](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Posílení zabezpečení všech dynamická skupina uživatelů
Ve výchozím nastavení **všichni uživatelé** skupina obsahuje vaše uživatele spolupráce B2B (Host) i. Dále je možné zabezpečit vaše **všichni uživatelé** skupiny pomocí pravidla odebrat uživatele typu Host. Je vidět na následujícím obrázku **všichni uživatelé** skupiny upravit tak, aby vyloučit hosty.

![Ukazuje pravidla, kde typ uživatele není rovno hosta](media/use-dynamic-groups/exclude-guest-users.png)

Vám může být také užitečné vytvořit novou dynamickou skupinu, která obsahuje pouze uživatele typu Host, tak, aby zásady (třeba zásady podmíněného přístupu Azure AD) můžete použít k nim.
Tyto skupiny můžou vypadat:

![Ukazuje pravidla, typ uživatele se rovná hodnotě typu Host](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Další postup

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)
- [Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md)

