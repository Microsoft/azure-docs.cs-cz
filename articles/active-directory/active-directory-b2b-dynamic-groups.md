---
title: Dynamické skupiny a spolupráce Azure Active Directory s B2B | Microsoft Docs
description: Ukazuje, jak používat Azure AD dynamických skupin se spoluprací Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 751502c2be84e9454c507f09a47b609d003ce2c5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33927847"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamické skupiny a spolupráce Azure Active Directory s B2B

## <a name="what-are-dynamic-groups"></a>Co jsou dynamické skupiny?
Konfigurace dynamické členství ve skupině zabezpečení pro Azure Active Directory (Azure AD) je k dispozici v [portálu Azure](https://portal.azure.com). Správci mohou nastavit pravidla pro naplnění skupin, které jsou vytvořeny ve službě Azure AD založit na atributech uživatelů (například userType, oddělení nebo země). Členové dají automaticky přidat nebo odebrat ze skupiny zabezpečení na základě jejich atributů. Tyto skupiny můžete poskytnout přístup k aplikacím nebo prostředkům cloudu (webů služby SharePoint, dokumenty) a přiřadit licence na členy. Další informace o dynamických skupin v [vyhrazené skupiny ve službě Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Odpovídající [licencování Azure AD Premium P1 a P2](https://azure.microsoft.com/pricing/details/active-directory/) je nutná k vytváření a použití dynamických skupin. Další informace najdete v článku [vytvořit pravidla založená na atributu pro dynamické členství ve skupině v Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Co jsou integrované dynamické skupiny?
**Všichni uživatelé** dynamická skupina umožňuje správci klientů k vytvoření skupiny obsahující všechny uživatele v klientovi s jedním kliknutím. Ve výchozím nastavení **všichni uživatelé** skupina obsahuje všechny uživatele v adresáři, včetně členů a hostů.
V rámci nový portál pro správu Azure Active Directory, můžete povolit **všichni uživatelé** skupiny v nastavení skupiny zobrazení.

![Zobrazuje povolení skupině všichni uživatelé nastavenou na Ano](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Posílení zabezpečení všech dynamické skupiny uživatelů
Ve výchozím nastavení **všichni uživatelé** skupina obsahuje také uživatelům (Host) spolupráce B2B. Dále je možné zabezpečit vaše **všichni uživatelé** skupiny pomocí pravidla odebrat uživatele typu Host. Následující obrázek ukazuje **všichni uživatelé** skupiny upravit tak, aby vyloučit hostů.

![Pravidlo ukazuje, kde typ uživatele není rovno hosta](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Může také pro vás užitečné k vytvoření nové dynamické skupiny, která obsahuje pouze uživatele typu Host, tak, aby mohli použít zásady (například zásady Azure AD podmíněného přístupu) k nim.
Jak taková skupina může vypadat:

![Pravidlo ukazuje, kde typ uživatele rovná hosta](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Další postup

- [Vlastnosti uživatele spolupráce B2B](active-directory-b2b-user-properties.md)
- [Přidání uživatele spolupráce B2B k roli](active-directory-b2b-add-guest-to-role.md)
- [Podmíněný přístup pro uživatele spolupráce B2B](active-directory-b2b-mfa-instructions.md)

