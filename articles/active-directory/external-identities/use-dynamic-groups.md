---
title: Dynamické skupiny a spolupráce B2B – Azure Active Directory | Microsoft Docs
description: Ukazuje, jak používat dynamické skupiny Azure AD s Azure Active Directory spolupráci B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441551"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Spolupráce s dynamickými skupinami a Azure Active Directory B2B

## <a name="what-are-dynamic-groups"></a>Co jsou dynamické skupiny?
Dynamická konfigurace členství ve skupině zabezpečení pro Azure Active Directory (Azure AD) je dostupná v [Azure Portal](https://portal.azure.com). Správci můžou nastavit pravidla k naplnění skupin vytvořených ve službě Azure AD na základě atributů uživatele (například userType, oddělení nebo země/oblast). Členy mohou být automaticky přidány nebo odebrány ze skupiny zabezpečení na základě jejich atributů. Tyto skupiny můžou poskytovat přístup k aplikacím nebo cloudovým prostředkům (webům SharePointu, dokumentům) a přiřazování licencí členům. Přečtěte si další informace o dynamických skupinách v [vyhrazených skupinách v Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

K vytváření a používání dynamických skupin se vyžaduje příslušné [Azure AD Premium licencování P1 nebo P2](https://azure.microsoft.com/pricing/details/active-directory/) . Další informace najdete v článku [vytváření pravidel založených na atributech pro členství v dynamické skupině v Azure Active Directory](../enterprise-users/groups-dynamic-membership.md).

## <a name="creating-an-all-users-dynamic-group"></a>Vytváří se dynamická skupina všichni uživatelé.
Můžete vytvořit skupinu obsahující všechny uživatele v rámci tenanta pomocí pravidla členství. Když se uživatelé v budoucnu přidávají nebo odebírají z tenanta, členství ve skupině se automaticky upraví.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, kterému se v tenantovi přiřadí role Globální správce nebo Správce uživatelů.
1. Vyberte **Azure Active Directory**.
2. V části **Spravovat** vyberte **skupiny** a pak vyberte **Nová skupina**.
1. Na stránce **Nová skupina** v části **typ skupiny** vyberte **zabezpečení**. Zadejte **název skupiny** a **Popis skupiny** pro novou skupinu. 
2. V části **typ členství** vyberte možnost **dynamický uživatel** a pak vyberte **Přidat dynamický dotaz**. 
4. Nad textovým polem **syntaxe pravidla** vyberte **Upravit**. Na stránce **Upravit syntaxi pravidla** zadejte do textového pole následující výraz:

   ```
   user.objectId -ne null
   ```
1. Vyberte **OK**. Pravidlo se zobrazí v poli syntaxe pravidla:

   ![Syntaxe pravidla pro všechny uživatele s dynamickou skupinou](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Vyberte **Uložit**. Nová dynamická skupina teď bude zahrnovat uživatele typu Host B2B i členské uživatele.


1. Vyberte **vytvořit** na stránce **Nová skupina** a vytvořte skupinu.

## <a name="creating-a-group-of-members-only"></a>Vytváření pouze skupiny členů

Pokud chcete, aby skupina vyloučila uživatele typu Host a zahrnovala jenom členy vašeho tenanta, vytvořte dynamickou skupinu, jak je popsáno výše, ale v poli **syntaxe pravidla** zadejte následující výraz:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Následující obrázek ukazuje syntaxi pravidla pro dynamickou skupinu upravenou tak, aby zahrnovala pouze členy a vyloučila hosty.

![Zobrazuje pravidlo, kde typ uživatele se rovná člen](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Vytváření pouze skupiny hostů

Může být také užitečné vytvořit novou dynamickou skupinu, která obsahuje jenom uživatele typu Host, abyste na ně mohli uplatnit zásady (například zásady podmíněného přístupu Azure AD). Vytvořte dynamickou skupinu, jak je popsáno výše, ale v poli **syntaxe pravidla** zadejte následující výraz:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Následující obrázek ukazuje syntaxi pravidla pro dynamickou skupinu upravenou tak, aby zahrnovala pouze hosty a vyloučila uživatele členů.

![Zobrazuje pravidlo, kde typ uživatele se rovná Host.](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Další kroky

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)
- [Přidání uživatele spolupráce B2B do role](add-guest-to-role.md)
- [Podmíněný přístup pro uživatele spolupráce B2B](conditional-access.md)