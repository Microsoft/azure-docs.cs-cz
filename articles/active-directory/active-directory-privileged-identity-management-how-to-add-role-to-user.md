---
title: Postup přidání nebo odebrání role uživatele | Dokumentace Microsoftu
description: Zjistěte, jak k přidání rolí na privilegované identity pomocí aplikace Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: 438a2fcd511fe62fa1dc7ba603c3770d5bcb56a4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446839"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele
S Azure Active Directory (AD), globálního správce (nebo správce společnosti) můžete aktualizovat běžícím uživatelé **trvale** přiřazená role ve službě Azure AD. To se provádí pomocí rutin Powershellu, například `Add-MsolRoleMember` a `Remove-MsolRoleMember`. Nebo může použít na webu Azure portal, jak je popsáno v [přiřazení rolí správce v Azure Active Directory](active-directory-assign-admin-roles.md).

Aplikace Azure AD Privileged Identity Management umožňuje správci privilegovaných rolí, aby trvalé přiřazení rolí stejně. Kromě toho správci privilegovaných rolí, můžete nastavit uživatele **oprávněné** pro role. Oprávněného správce můžete aktivovat role, když ho potřebují, a potom jejich oprávnění vyprší po jejich dokončení.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Správa rolí pomocí služby PIM na portálu Azure portal
Ve vaší organizaci můžete přiřadit uživatele pro různé role pro správu ve službě Azure AD, Office 365 a dalším službám společnosti Microsoft a aplikace.  Další informace o dostupných rolí najdete v [role v Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Chcete-li přidat nebo odebrat uživatele v roli pomocí Privileged Identity Management, otevřete řídicí panel PIM. Pak klikněte na **uživatelé ve správcovských rolích** , nebo vyberte určité role (jako je například globální správce) z tabulky role.

> [!NOTE]
> Pokud jste nepovolili PIM na portálu Azure portal ještě, přejděte na [Začínáme s Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) podrobnosti.

Pokud chcete poskytnout jiný přístup uživatelů k PIM samotné, role, které PIM vyžaduje, aby uživatel měl jsou popsány dále v [způsob poskytnutí přístupu k PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Přidání uživatele do role
1. V [webu Azure portal](https://portal.azure.com/), vyberte **Azure AD Privileged Identity Management** dlaždici na řídicím panelu.
2. Vyberte **spravovat privilegované role**.
3. V **Souhrn rolí** tabulku, vyberte roli, kterou chcete spravovat.
4. V okně roli vyberte **přidat**.
5. Klikněte na tlačítko **vybraným uživatelům** a vyhledejte uživatele **vybraným uživatelům** okno.  
6. Vyberte uživatele ze seznamu výsledků hledání a klikněte na tlačítko **provádí**.
7. Klikněte na tlačítko **OK** uložte svůj výběr. Uživatel, kterého jste vybrali se zobrazí v seznamu jako oprávněné pro roli.

> [!NOTE]
> Oprávněné pro roli ve výchozím nastavení jsou pouze noví uživatelé v roli. Pokud chcete trvalé roli, klikněte na uživatele v seznamu. Informace o uživateli se zobrazí v novém okně. Vyberte **zkontrolujte oprávnění** v nabídce uživatelské informace.  
> Pokud uživatel nemůže zaregistrovat pro Azure Multi-Factor Authentication (MFA), nebo se pomocí účtu Microsoft (obvykle @outlook.com), je třeba provést je trvalé v jejich role. Oprávnění správci se zobrazí výzva k registraci pro MFA během aktivace.

Teď, když uživatel není oprávněný pro roli, informujte je, že si ji můžou aktivovat podle pokynů v [postup aktivace nebo deaktivace role](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Odebrání uživatele z role
Odebrání přiřazení oprávněné role uživatele, ale ujistěte se, že je vždy alespoň jeden uživatel, který je globální správce je trvalý.

Postupujte podle těchto kroků můžete odebrat konkrétní uživatele z role:

1. Přejděte k roli v seznamu rolí tak, že vyberete roli na řídicím panelu Azure AD PIM nebo kliknutím na **uživatelé ve správcovských rolích** tlačítko.
2. Kliknutím na uživatele v seznamu uživatelů.
3. Klikněte na tlačítko **odebrat**. Zpráva zobrazí výzvu k potvrzení.
4. Klikněte na tlačítko **Ano** k odebrání role uživatele.

Pokud si nejste jisti, které uživatelé stále potřebují svá přiřazení rolí, pak můžete [zahájení kontroly přístupu pro roli](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

