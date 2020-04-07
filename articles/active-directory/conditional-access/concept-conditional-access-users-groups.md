---
title: Uživatelé a skupiny v zásadách podmíněného přístupu – Azure Active Directory
description: Kdo jsou uživatelé a skupiny v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17312e44714c8bdb20e22ad9aeb950e46eb71e3e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755251"
---
# <a name="conditional-access-users-and-groups"></a>Podmíněný přístup: Uživatelé a skupiny

Zásady podmíněného přístupu musí zahrnovat přiřazení uživatele jako jeden ze signálů v rozhodovacím procesu. Uživatelé mohou být zahrnuti nebo vyloučeni ze zásad podmíněného přístupu. 

![Uživatel jako signál v rozhodnutích učiněných podmíněným přístupem](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Zahrnout uživatele

Tento seznam uživatelů obvykle zahrnuje všechny uživatele, na které organizace cílí v zásadách podmíněného přístupu. 

Následující možnosti jsou k dispozici zahrnout při vytváření zásad podmíněného přístupu.

- Žádný
   - Nebyl vybrán žádný uživatel.
- Všichni uživatelé
   - Všichni uživatelé, kteří existují v adresáři, včetně hostů B2B.
- Výběr uživatelů a skupin
   - Všichni hostující a externí uživatelé
      - Tento výběr zahrnuje všechny hosty B2B a `user type` externí uživatele, včetně všech uživatelů s atributem nastaveným na `guest`. Tento výběr platí také pro všechny externí uživatele přihlášené z jiné organizace, jako je poskytovatel cloudových řešení (CSP). 
   - Role adresáře
      - Umožňuje správcům vybrat konkrétní role adresáře Azure AD, které se používají k určení přiřazení. Organizace mohou například vytvořit přísnější zásady pro uživatele, kterým byla přiřazena role globálního správce.
   - Uživatelé a skupiny
      - Umožňuje cílení na konkrétní sady uživatelů. Organizace mohou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybrána aplikace HR. Skupinou může být libovolný typ skupiny ve službě Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce.

## <a name="exclude-users"></a>Vyloučit uživatele

Pokud organizace zahrnují a vylučují uživatele nebo skupinu, je uživatel nebo skupina vyloučena ze zásady, protože akce vyloučení přepíše zahrnutí do zásady. Výjimky se běžně používají pro nouzové přístupové nebo break-glass účty. Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, naleznete v následujících článcích: 

* [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Služby Azure Active Directory](../authentication/concept-resilient-controls.md)

Následující možnosti jsou k dispozici vyloučit při vytváření zásad podmíněného přístupu.

- Všichni hostující a externí uživatelé
   - Tento výběr zahrnuje všechny hosty B2B a `user type` externí uživatele, včetně všech uživatelů s atributem nastaveným na `guest`. Tento výběr platí také pro všechny externí uživatele přihlášené z jiné organizace, jako je poskytovatel cloudových řešení (CSP). 
- Role adresáře
   - Umožňuje správcům vybrat konkrétní role adresáře Azure AD, které se používají k určení přiřazení. Organizace mohou například vytvořit přísnější zásady pro uživatele, kterým byla přiřazena role globálního správce.
- Uživatelé a skupiny
   - Umožňuje cílení na konkrétní sady uživatelů. Organizace mohou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybrána aplikace HR. Skupinou může být libovolný typ skupiny ve službě Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce.

### <a name="preventing-administrator-lockout"></a>Zabránění uzamčení správce

Chcete-li zabránit správci v uzamčení z jejich adresáře při vytváření zásady pro **všechny uživatele** a **všechny aplikace**, zobrazí se následující upozornění.

> Nezamykejte se! Doporučujeme použít zásady pro malou sadu uživatelů nejprve ověřit, že se chová podle očekávání. Doporučujeme také vyloučit alespoň jednoho správce z těchto zásad. Tím zajistíte, že budete mít stále přístup a můžete aktualizovat zásady, pokud je požadována změna. Zkontrolujte postižené uživatele a aplikace.

Ve výchozím nastavení bude zásada poskytovat možnost vyloučit aktuálního uživatele ze zásady, ale toto nastavení může přepsáno správcem, jak je znázorněno na následujícím obrázku. 

![Pozor, nezamykejte se!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: Cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md)

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)
