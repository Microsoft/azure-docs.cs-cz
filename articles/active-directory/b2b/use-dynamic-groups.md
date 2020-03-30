---
title: Dynamické skupiny a spolupráce B2B – Azure Active Directory | Dokumenty společnosti Microsoft
description: Ukazuje, jak používat dynamické skupiny Azure AD se spoluprací azure active directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226895"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamické skupiny a spolupráce služby Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Co jsou dynamické skupiny?
Dynamická konfigurace členství ve skupinách zabezpečení pro Azure Active Directory (Azure AD) je [dostupná na webu Azure Portal](https://portal.azure.com). Správci mohou nastavit pravidla pro naplnění skupin, které jsou vytvořené ve službě Azure AD na základě atributů uživatele (například userType, oddělení nebo země nebo oblasti. Členy lze automaticky přidat nebo odebrat ze skupiny zabezpečení na základě jejich atributů. Tyto skupiny mohou poskytovat přístup k aplikacím nebo cloudovým prostředkům (sharepointové weby, dokumenty) a přiřazovat licence členům. Přečtěte si další informace o dynamických skupinách ve [vyhrazených skupinách ve službě Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

K vytvoření a použití dynamických skupin je potřeba příslušná [licence Azure AD Premium P1 nebo P2.](https://azure.microsoft.com/pricing/details/active-directory/) Další informace najdete v článku [Vytvoření pravidel založených na atributech pro dynamické členství ve skupinách ve službě Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Vytvoření dynamické skupiny "všichni uživatelé"
Můžete vytvořit skupinu obsahující všechny uživatele v rámci klienta pomocí pravidla členství. Když jsou uživatelé přidáni nebo odebráni z tenanta v budoucnu, členství skupiny se automaticky upraví.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu, kterému je přiřazena role globálního správce nebo správce uživatele v tenantovi.
1. Vyberte **Azure Active Directory**.
2. V části **Manage**vyberte **Skupiny**a potom vyberte **Nová skupina**.
1. Na stránce **Nová skupina** vyberte v části **Typ skupiny** **položku Zabezpečení**. Zadejte **název skupiny** a **popis skupiny** pro novou skupinu. 
2. V části **Typ členství**vyberte **Dynamický uživatel**a pak vyberte Přidat **dynamický dotaz**. 
4. Nad textovým polem **Syntaxe pravidla** vyberte **Upravit**. Na stránce **Upravit syntaxi pravidla** zadejte do textového pole následující výraz:

   ```
   user.objectId -ne null
   ```
1. Vyberte **OK**. Pravidlo se zobrazí v poli Syntaxe pravidla:

   ![Syntaxe pravidla pro všechny uživatele dynamické skupiny](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Vyberte **Uložit**. Nová dynamická skupina bude nyní zahrnovat uživatele typu Host B2B i členské uživatele.


1. Chcete-li vytvořit skupinu, vyberte vytvořit na stránce **Nová skupina.** **Create**

## <a name="creating-a-group-of-members-only"></a>Vytvoření pouze skupiny členů

Pokud chcete, aby vaše skupina vyloučila uživatele typu Host a zahrnula pouze členy vašeho tenanta, vytvořte dynamickou skupinu, jak je popsáno výše, ale do pole **Syntaxe pravidla** zadejte následující výraz:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Následující obrázek znázorňuje syntaxi pravidla pro dynamickou skupinu upravenou tak, aby zahrnovala pouze členy a vyloučila hosty.

![Zobrazuje pravidlo, kde se typ uživatele rovná členu.](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Vytvoření pouze skupiny hostů

Můžete také najít užitečné vytvořit novou dynamickou skupinu, která obsahuje pouze uživatele typu Host, takže můžete použít zásady (například zásady podmíněného přístupu Azure AD) na ně. Vytvořte dynamickou skupinu, jak je popsáno výše, ale do pole **Syntaxe pravidla** zadejte následující výraz:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Následující obrázek znázorňuje syntaxi pravidla pro dynamickou skupinu upravenou tak, aby zahrnovala pouze hosty a vyloučila členské uživatele.

![Zobrazuje pravidlo, kde se typ uživatele rovná hostu.](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Další kroky

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)
- [Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md)

