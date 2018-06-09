---
title: Jak přidat nebo odebrat roli uživatele | Microsoft Docs
description: Zjistěte, jak k přidání rolí na privilegované identity pomocí aplikace Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 01/03/2018
ms.author: rolyon
ms.openlocfilehash: 856fdc69bd5ce582ca772c01f8af615fbc455887
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233216"
---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele
S Azure Active Directory (AD), globální správce (nebo správce společnosti) můžete aktualizovat které uživatelé jsou **trvale** přiřadit do rolí ve službě Azure AD. To se provádí pomocí rutin prostředí PowerShell jako `Add-MsolRoleMember` a `Remove-MsolRoleMember`. Nebo použitím portálu Azure, jak je popsáno v [přiřazení rolí správce v Azure Active Directory](active-directory-assign-admin-roles.md).

Aplikace Azure AD Privileged Identity Management umožňuje správcům privilegovaných rolí zkontrolujte přiřazení rolí trvalé, také. Kromě toho privilegované role Správci uživatelé **vhodné** pro role správce. Oprávněné správce může aktivovat roli, když je potřebují, a potom jejich oprávnění vyprší po jejich dokončení akce.

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>Správa rolí s PIM na portálu Azure
Ve vaší organizaci můžete přiřadit uživatele pro různé role pro správu ve službě Azure AD, Office 365 a dalším službám společnosti Microsoft a aplikace.  Další informace o dostupných rolí najdete na [role v Azure AD PIM](active-directory-privileged-identity-management-roles.md).

Chcete-li přidat nebo odebrat uživatele v roli pomocí Privileged Identity Management, zprovoznit PIM řídicího panelu. Buď klikněte **uživatelé v rolích správce** tlačítko nebo vyberte určité role (jako je například globální správce) z tabulky role.

> [!NOTE]
> Pokud jste nepovolili PIM na portálu Azure ještě, přejděte na [Začínáme s Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) podrobnosti.

Pokud chcete poskytnout jiný uživatel přístup k PIM samotné, jsou popsány dále v rolí, které PIM vyžaduje, aby měl uživatel [jak poskytnout přístup k PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## <a name="add-a-user-to-a-role"></a>Přidat uživatele k roli
1. V [portál Azure](https://portal.azure.com/), vyberte **Azure AD Privileged Identity Management** dlaždici na řídicím panelu.
2. Vyberte **spravovat privilegované role**.
3. V **Souhrn rolí** tabulky, vyberte roli, kterou chcete spravovat.
4. V okně role vyberte **přidat**.
5. Klikněte na tlačítko **vyberte uživatele,** a vyhledejte uživatele na **vyberte uživatele,** okno.  
6. Vyberte uživatele ze seznamu výsledků hledání a klikněte na tlačítko **provádí**.
7. Klikněte na tlačítko **OK** uložte svůj výběr. Uživatel, který jste zvolili se zobrazí v seznamu jako vhodné pro roli.

> [!NOTE]
> Vhodné pro roli ve výchozím nastavení jsou jenom noví uživatelé v roli. Pokud chcete, aby byly trvalé roli, klikněte na uživatele v seznamu. Informace o uživateli se zobrazí v novém okně. Vyberte **zkontrolujte oprávnění** v nabídce uživatelské informace.  
> Pokud uživatele nelze zaregistrovat pro Azure Multi-Factor Authentication (MFA), nebo se pomocí účtu Microsoft (obvykle @outlook.com), budete muset převést na trvalé v jejich rolí. Oprávněné správce se zobrazí výzva k registraci pro MFA během aktivace.

Teď, když je vhodné pro roli uživatele, dát jim vědět, že se může aktivovat podle pokynů v [jak aktivovat nebo deaktivovat roli](active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="remove-a-user-from-a-role"></a>Odebrat uživatele z role
Můžete odebrat uživatele z role vhodné přiřazení, ale ujistěte se, že je vždy alespoň jeden uživatel, který je trvalé globálního správce.

Postupujte podle těchto kroků odebrat konkrétního uživatele z role:

1. Přejděte do role v seznamu rolí, vyberte roli v řídicím panelu Azure AD PIM nebo kliknutím na **uživatelé v rolích správce** tlačítko.
2. Klikněte na uživatele v seznamu uživatelů.
3. Klikněte na tlačítko **odebrat**. Zpráva zobrazí výzvu k potvrzení.
4. Klikněte na tlačítko **Ano** k odebrání role uživatele.

Pokud si nejste jistí, kteří uživatelé stále nutné jejich přiřazení role, pak můžete [spustit kontrola přístupu pro roli](active-directory-privileged-identity-management-how-to-start-security-review.md).

## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

