---
title: Role adresáře Azure AD můžete spravovat v PIM | Dokumentace Microsoftu
description: Popisuje role adresáře Azure AD, které můžete spravovat v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: fc45cde1a5f0f287274302541ac0115569e2239d
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666332"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>Role adresáře Azure AD, která spravujete v PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Přiřadit uživatele ve vaší organizaci pro různé role pro správu ve službě Azure AD. Tato přiřazení rolí řídit, jaké úkoly, jako je například přidávání nebo odebírání uživatelů nebo změna nastavení služby, uživatelé budou moct provádět v Azure AD, Office 365 a další Microsoft Online Services a propojené aplikace.  

Globální správce můžete aktualizovat, které jsou uživatelům **trvale** přiřazená role ve službě Azure AD na portálu, jak je popsáno v [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) nebo pomocí [ Příkazy prostředí PowerShell](/powershell/module/azuread#directory_roles).

Azure AD Privileged Identity Management (PIM) spravuje zásady pro privilegovaný přístup pro uživatele ve službě Azure AD. PIM uživatelům přiřadí jednu nebo více rolí ve službě Azure AD a můžete přiřadit někdo bude trvale v roli, nebo na roli oprávnění. Když uživatel trvale přiřazená k roli nebo aktivuje přiřazení oprávněné role, pak se oprávnění přiřazená k jejich rolí můžou spravovat služby Azure Active Directory, Office 365 a dalších aplikací.

Není žádný rozdíl v přístupu k někdo s trvalou oproti přiřazení oprávněné role. Jediným rozdílem je, že někteří uživatelé nepotřebují tento přístup neustále. Probíhají na roli oprávnění a můžete zapnout a vypnout vždy, když potřebují.

## <a name="roles-managed-in-pim"></a>Role spravovaného v PIM
Privileged Identity Management umožňuje přiřadit uživatele k běžné rolí správce, včetně:

* **Globální správce** (taky označovaný jako správce společnosti) má přístup ke všem funkcím pro správu. Můžete mít více než jednoho globálního správce ve vaší organizaci. Osoba, která se zaregistruje k nákupu předplatného Office 365 automaticky stane globálním správcem.
* **Správce privilegovaných rolí** spravuje Azure AD PIM a aktualizuje přiřazení rolí pro ostatní uživatele.  
* **Správce fakturace** může dělat nákupy, spravovat předplatná, spravovat lístky podpory a sledovat stav služeb.
* **Správce hesel** může resetovat hesla, spravovat žádosti o služby a sledovat stav služeb. Správci hesel můžou resetovat hesla pro uživatele jenom.
* **Správce služeb** spravovat žádosti o služby a sledovat stav služeb.
  
  > [!NOTE]
  > Pokud používáte Office 365, pak před přiřazením role Správce služby pro uživatele, nejdříve uživatele přiřaďte oprávnění pro správu služby, jako je Exchange Online.
  > 
  > 
* **Správce správy uživatelů** může resetovat hesla, sledovat stav služeb a spravuje uživatelské účty, skupiny uživatelů a žádostí o služby. Správce správy uživatelů nelze odstranit globálního správce, vytvářet další role správců ani resetovat hesla správců fakturace, globálních a správců služeb.
* **Správce Exchange** má přístup k Exchangi Online pro správu prostřednictvím centra pro správu Exchange (EAC) a můžete provést téměř jakoukoli úlohu v Exchangi Online.
* **Správce služby SharePoint (Preview)** má přístupová oprávnění k Sharepointu Online prostřednictvím centra pro správu Sharepointu Online a můžete provádět skoro všechny úlohy v Sharepointu Online. Tato role je aktuálně ve verzi preview. Oprávnění uživatelé setkat zpoždění až po dokončení aktivace v PIM pomocí této role v rámci služby SharePoint.
* **Správce Skypu pro firmy** má přístup správce ke Skypu pro firmy pomocí Skypu pro firmy centra pro správu a můžete provádět skoro všechny úlohy ve Skypu for Business Online.

Přečtěte si tyto články pro další podrobnosti o [přiřazení rolí správce ve službě Azure AD](../users-groups-roles/directory-assign-admin-roles.md) a [přiřazení rolí správce v Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


PIM, můžete [těchto rolí přiřadit uživateli](pim-how-to-add-role-to-user.md) tak, aby uživatel může [aktivace role v případě potřeby](pim-how-to-activate-role.md).

Pokud chcete jiné uživateli dát přístup ke správě v PIM samotné, role, které PIM vyžaduje, aby uživatel měl jsou popsány dále v [způsob poskytnutí přístupu k PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Role, která nejsou spravovaná v PIM
Role v systému Exchange Online nebo SharePoint Online, kromě těch, které uvedených výše, nejsou reprezentovány ve službě Azure AD a proto nejsou viditelné v PIM. Další informace o změně přiřazení rolí podrobných v těchto služeb Office 365 najdete v tématu [oprávnění ve službách Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Skupiny prostředků a předplatná Azure nejsou také reprezentovány ve službě Azure AD. Správa předplatných Azure, najdete v článku [postup přidání nebo změna rolí správce Azure](../../billing/billing-add-change-azure-subscription-administrator.md) a další informace o funkci Azure RBAC najdete v tématu [řízení přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Role uživatele a přihlašování
Některé služby a aplikace přiřazení uživatele k roli nemusí být dostatečné pro povolení tohoto uživatele jako správce.

Přístup k webu Azure portal vyžaduje že uživatel být správce služeb nebo spolusprávcem předplatného Azure, i v případě, že uživatel není potřeba Správa předplatných Azure.  Například pokud chcete spravovat nastavení konfigurace pro službu Azure AD, uživatel musí být globální správce ve službě Azure AD a spolusprávce předplatného na předplatné Azure.  Zjistěte, jak přidat uživatele do předplatných Azure, najdete v článku [postup přidání nebo změna rolí správce Azure](../../billing/billing-add-change-azure-subscription-administrator.md).

Přístup k Microsoft Online Services může vyžadovat uživateli také přiřadí licence mohli otevřít portál služby nebo provádět úlohy správy.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Přiřazení licence pro uživatele ve službě Azure AD

1. Přihlaste se k [webu Azure portal](http://portal.azure.com) pomocí účtu globálního správce nebo Spolusprávce účtu.

1. Vyberte adresář Azure AD, kterou chcete pracovat, který má licence s ním spojená.

1. V levém navigačním panelu klikněte na tlačítko **Azure Active Directory**.

1. Klikněte na tlačítko **licence**. Zobrazí se seznam dostupných licencí.

    ![Licence Azure Active Directory](./media/pim-roles/licenses-overview.png)

1. Klikněte na vaše **produktu**.

1. Klikněte na licenční plán, který obsahuje licence, které chcete distribuovat.

    ![Licence produktů](./media/pim-roles/licenses-products.png)

1. Klikněte na tlačítko **přiřadit** a otevřete tak podokno přiřadit licence.

    ![Licencovaní uživatelé](./media/pim-roles/licenses-licensed-users.png)

1. Vyberte uživatele nebo skupiny, kterou chcete přiřadit licenci k.

    ![Přiřadit licenci](./media/pim-roles/licenses-assign-license.png)

1. Klikněte na tlačítko **možnosti přiřazení** konfigurace možností přiřazení.

    ![Možnosti přiřazení](./media/pim-roles/licenses-assignment-options.png)

1. Klikněte na tlačítko **přiřadit** přiřadit licence. Uživatel nyní má licence.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup

- [Začněte používat PIM](pim-getting-started.md)
- [Přiřazení role adresáře Azure AD v PIM](pim-how-to-add-role-to-user.md)

