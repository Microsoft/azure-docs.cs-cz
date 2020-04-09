---
title: Správa přístupu k aplikacím & prostředkům pomocí skupin – Azure AD
description: Přečtěte si, jak spravovat přístup ke cloudovým aplikacím, místním aplikacím a prostředkům vaší organizace pomocí skupin Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89c4fa536994ecc8cecb50c907d43df2110be7df
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982595"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Správa přístupu k aplikacím a prostředkům pomocí skupin služby Azure Active Directory
Azure Active Directory (Azure AD) umožňuje používat skupiny ke správě přístupu ke cloudovým aplikacím, místním aplikacím a prostředkům. Vaše prostředky mohou být součástí organizace Azure AD, jako jsou oprávnění ke správě objektů prostřednictvím rolí ve službě Azure AD nebo externích aplikací, jako jsou aplikace Software jako služba (SaaS), služby Azure, weby SharePointu a místní prostředky.

>[!NOTE]
> Na webu Azure Portal uvidíte některé skupiny, jejichž členství a podrobnosti o skupině se na portálu nedaří spravovat:
>
> - Skupiny synchronizované z místní služby Active Directory lze spravovat pouze v místním adresáři Active Directory.
> - Jiné typy skupin, jako jsou distribuční seznamy a skupiny zabezpečení s povolenou poštou, se spravují jenom v Centru pro správu Exchange nebo v Centru pro správu Microsoftu 365. Chcete-li spravovat tyto skupiny, musíte se přihlásit do Centra pro správu Exchange nebo do Centra pro správu Microsoftu 365.

## <a name="how-access-management-in-azure-ad-works"></a>Jak funguje správa přístupu ve službě Azure AD

Azure AD vám pomůže udělit přístup k prostředkům vaší organizace tím, že poskytuje přístupová práva pro jednoho uživatele nebo pro celou skupinu Azure AD. Použití skupin umožňuje vlastníkovi prostředku (nebo vlastníkovi adresáře Azure AD) přiřadit sadu přístupových oprávnění všem členům skupiny – místo poskytování práv jedno po druhém. Vlastník prostředku nebo adresáře může také udělit práva správce seznamu členů někomu jinému, například vedoucímu oddělení nebo správci technické podpory, a podle potřeby umožnit této osobě přidávat a odebírat členy. Další informace o správě vlastníků skupin naleznete v tématu [Správa vlastníků skupin](active-directory-accessmanagement-managing-group-owners.md)

![Diagram správy přístupu ve službě Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Způsoby přiřazení přístupových práv

Existují čtyři způsoby, jak uživatelům přiřadit přístupová práva k prostředkům:

- **Přímé přiřazení.** Vlastník prostředku přímo přiřadí uživatele k prostředku.

- **Přiřazení skupiny.** Vlastník prostředku přiřadí skupině Azure AD k prostředku, který automaticky poskytuje všem členům skupiny přístup k prostředku. Členství ve skupině spravuje vlastník skupiny i vlastník prostředku, který umožňuje vlastníkovi přidávat nebo odebírat členy ze skupiny. Další informace o přidání nebo odebrání členství ve skupině najdete v [tématu Postup: Přidání nebo odebrání skupiny z jiné skupiny pomocí portálu Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Přiřazení založené na pravidlech.** Vlastník prostředku vytvoří skupinu a použije pravidlo k definování, kteří uživatelé jsou přiřazeni k určitému prostředku. Pravidlo je založeno na atributech, které jsou přiřazeny jednotlivým uživatelům. Vlastník prostředku spravuje pravidlo a určuje, které atributy a hodnoty jsou nutné k povolení přístupu k prostředku. Další informace naleznete [v tématu Vytvoření dynamické skupiny a kontrola stavu](../users-groups-roles/groups-create-rule.md).

    Můžete také sledovat toto krátké video pro rychlé vysvětlení o vytváření a používání dynamických skupin:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Přiřazení externího orgánu.** Přístup pochází z externího zdroje, jako je místní adresář nebo aplikace SaaS. V takovém případě vlastník prostředku přiřadí skupinu poskytnout přístup k prostředku a pak externí zdroj spravuje členy skupiny.

   ![Diagram s přehledem správy přístupu](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Mohou se uživatelé připojit ke skupinám, aniž by byli přiřazeni?
Vlastník skupiny může uživatelům povolit, aby místo jejich přiřazení našli své vlastní skupiny. Vlastník může také nastavit skupinu tak, aby automaticky přijímala všechny uživatele, kteří se připojují nebo vyžadují schválení.

Poté, co uživatel požádá o připojení ke skupině, je žádost předána vlastníkovi skupiny. Pokud je to nutné, vlastník může žádost schválit a uživatel je upozorněn na členství ve skupině. Pokud však máte více vlastníků a jeden z nich nesouhlasí, uživatel je upozorněn, ale není přidán do skupiny. Další informace a pokyny, jak uživatelům povolit žádost o připojení ke skupinám, najdete v tématu [Nastavení Azure AD, aby uživatelé mohli požádat o připojení ke skupinám.](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Další kroky
Teď, když máte trochu úvod do správy přístupu pomocí skupin, začnete spravovat své prostředky a aplikace.

- [Vytvoření nové skupiny pomocí služby Azure Active Directory](active-directory-groups-create-azure-portal.md) nebo [vytvoření a správa nové skupiny pomocí rutin prostředí PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Přiřazení přístupu k integrované aplikaci SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Synchronizace místní skupiny s Azure pomocí Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
