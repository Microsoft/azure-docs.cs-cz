---
title: Role v Azure AD Privileged Identity Management | Microsoft Docs
description: Zjistěte, jaké role se používají pro privilegované identity pomocí rozšíření Azure Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 03/04/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 48ff4b4957ac1f9f8f0d6737335811a3e2df0b68
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698301"
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Různé role pro správu v Azure Active Directory PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Můžete přiřadit uživatele ve vaší organizaci pro různé role pro správu ve službě Azure AD. Přiřazení rolí řídí, jaké úkoly, jako je například přidávání nebo odebírání uživatelů nebo změna nastavení služby, mohou uživatelé provádět na Azure AD, Office 365 a dalších služeb Microsoft Online Services a propojených aplikací.  

> [!IMPORTANT]
> Společnost Microsoft doporučuje, které spravujete pomocí Azure AD [centra pro správu Azure AD](https://aad.portal.azure.com) na portálu Azure.

Můžete aktualizovat globální správce, které jsou uživatelé **trvale** přiřadit do rolí ve službě Azure AD, například pomocí rutin prostředí PowerShell `Add-MsolRoleMember` a `Remove-MsolRoleMember`, nebo prostřednictvím portálu, jak je popsáno v [přiřazení rolí správce ve službě Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).

Azure AD Privileged Identity Management (PIM) spravuje zásady pro privilegovaný přístup pro uživatele ve službě Azure AD. PIM přiřadí uživatelům jednu nebo více rolí ve službě Azure AD, a můžete přiřadit někoho, aby se trvale, v roli, ani vhodné k roli. Když uživatel trvale přiřazená k roli nebo aktivuje přiřazení vhodné role, pak můžou spravovat služby Azure Active Directory, Office 365 a dalších aplikací s oprávněními přiřazené k jejich rolí.

Není žádný rozdíl ve přístupu k někdo s trvalé a přiřazení role vhodné. Jediným rozdílem je, že někteří uživatelé nepotřebují tento přístup vždy. Probíhají vhodné pro roli a můžete zapnout a vypnout vždy, když potřebují.

## <a name="roles-managed-in-pim"></a>Role, které jsou spravovány v PIM
Privileged Identity Management umožňuje přiřadit uživatele k běžné role správce, včetně:

* **Globální správce** (také označovaný jako správce společnosti) má přístup ke všem funkcím pro správu. Můžete mít více než jeden globální správce ve vaší organizaci. Osoba, která se zaregistruje při nákupu služeb Office 365 automaticky stane globální správce.
* **Správce privilegovaných rolí** spravuje Azure AD PIM a aktualizuje přiřazení rolí pro ostatní uživatele.  
* **Správce fakturace** může dělat nákupy, spravovat předplatná, spravovat lístky žádostí o podporu a sledovat stav služeb.
* **Heslo správce** může resetovat hesla, spravovat žádosti o služby a sledovat stav služeb. Heslo správce je omezen na resetování hesla pro uživatele.
* **Správce služeb** spravovat žádosti o služby a sledovat stav služeb.
  
  > [!NOTE]
  > Pokud používáte Office 365, pak před přiřazením roli Správce služby pro uživatele, nejprve přiřadíte uživatel oprávnění správce služby, jako je Exchange Online.
  > 
  > 
* **Správce správy uživatelů** může resetovat hesla, sledovat stav služeb a spravovat uživatelské účty, skupiny uživatelů a žádosti o služby. Správce správy uživatelů nelze odstranit globální správce, vytvořit další role správce nebo resetování hesla pro fakturace, globálních a Správce služby.
* **Správce serveru Exchange** má přístup správce k systému Exchange Online pomocí centra pro správu Exchange (e) a můžete provádět skoro všechny úlohy v systému Exchange Online.
* **Správce služby SharePoint (Preview)** má přístup správce ke službě SharePoint Online přes do centra pro správu služby SharePoint Online a můžete provádět skoro všechny úlohy ve službě SharePoint Online. Tato role je aktuálně ve verzi preview. Oprávněných uživatelů může zaznamenat zpoždění pomocí této role v rámci služby SharePoint po aktivaci v PIM.
* **Skype pro firmy správce** má přístup správce ke Skypu pro firmy prostřednictvím Skype pro firmy centra pro správu a můžete provádět skoro všechny úlohy v Skype for Business Online.

Přečtěte si tyto články další podrobnosti o [přiřazení rolí správce ve službě Azure AD](active-directory-assign-admin-roles-azure-portal.md) a [přiřazení rolí správců v Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Z PIM, můžete [přiřadit tyto role uživatele](active-directory-privileged-identity-management-how-to-add-role-to-user.md) tak, aby uživatel může [aktivovat roli, v případě potřeby](active-directory-privileged-identity-management-how-to-activate-role.md).

Pokud chcete poskytnout jiný uživatel přístup ke správě v PIM samotné, jsou popsány dále v rolí, které PIM vyžaduje, aby měl uživatel [jak poskytnout přístup k PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Role není spravovaná v PIM
Role v systému Exchange Online nebo SharePoint Online, s výjimkou případů uvedených výše, nenachází ve službě Azure AD a proto nejsou viditelné v PIM. Další informace o změně přiřazení jemně odstupňovaných rolí v těchto služeb Office 365 najdete v tématu [oprávnění v Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Skupiny prostředků a předplatná Azure také nenachází ve službě Azure AD. Chcete-li spravovat předplatná Azure, přečtěte si téma [postup přidání nebo změna role Správce služby Azure](../billing/billing-add-change-azure-subscription-administrator.md) a další informace o Azure RBAC najdete [řízení přístupu](../role-based-access-control/role-assignments-portal.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Uživatelské role a přihlášení
Pro některé služby společnosti Microsoft a aplikace nemusí být přiřazení uživatele k roli dostatečná povolení tohoto uživatele jako správce.

Přístup k portálu Azure vyžaduje, že uživatel být služby správce nebo spolusprávce na předplatné Azure, i v případě, že uživatel není potřeba spravovat předplatná Azure.  Například ke správě nastavení konfigurace pro Azure AD, uživatel musí být globálním správcem ve službě Azure AD a spolusprávce předplatného na základě předplatného služby Azure.  Zjistěte, jak přidat uživatele do předplatná Azure, najdete v tématu [postup přidání nebo změna role Správce služby Azure](../billing/billing-add-change-azure-subscription-administrator.md).

Přístup ke službám Microsoft Online Services může vyžadovat uživatel taky přiřadit licenci před jejich otevřete portál služby nebo provádění úloh správy.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Přiřazení licence pro uživatele ve službě Azure AD
1. Přihlaste se k [portál Azure](http://portal.azure.com) s účet globálního správce nebo spolusprávce účtem.
3. Výběr možnosti Azure AD a adresáře, který chcete pracovat s a že má licencí s ním spojená.
4. Vyberte **licence** na levé straně. Zobrazí se seznam dostupných licencí.
5. Vyberte licenční plán, který obsahuje licencí, které chcete distribuovat.
6. Vyberte **přiřazení uživatelů**.
7. Vyberte uživatele, který chcete přiřadit licenci k.
8. klikněte **přiřadit** tlačítko.  Uživatel teď může přihlásit k Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

