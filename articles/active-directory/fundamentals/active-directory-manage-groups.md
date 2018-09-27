---
title: Další informace o správu přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory | Dokumentace Microsoftu
description: Další informace o správě přístupu k prostředkům pomocí skupin Azure Active Directory, místních aplikací a cloudových aplikací vaší organizace.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: piotrci
ms.openlocfilehash: 69be2cb8b57b34502d5a5688ac163d7451abe48e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223968"
---
# <a name="learn-about-access-management-using-azure-active-directory-groups"></a>Další informace o správě přístupu pomocí skupin Azure Active Directory
Azure Active Directory (Azure AD) umožňuje spravovat vaše cloudové aplikace, místním aplikacím a prostředkům pomocí skupin vaší organizace. Vaše prostředky můžou být součástí adresáře, jako je například oprávnění ke správě objektů pomocí rolí v adresáři, nebo externí vzhledem k adresáři, jako jsou pro Software jako služba (SaaS) aplikací Azure services, Sharepointové weby a místním prostředkům.

>[!NOTE]
>Abyste mohli používat Azure Active Directory, musíte mít účet Azure. Pokud účet Azure nemáte, můžete si [zaregistrovat bezplatný účet Azure](https://azure.microsoft.com/free/).

## <a name="how-does-access-management-in-azure-ad-work"></a>Jak získat přístup k práci s Azure AD?
Azure AD vám poskytnout přístup k prostředkům ve vaší organizaci tím, že poskytuje přístupová práva k jenom jednoho konkrétního uživatele nebo pro celý Azure AD seskupit. Použití skupin umožňuje prostředku vlastníka (nebo vlastník adresář Azure AD), přiřadit sadu oprávnění k přístupu na všechny členy skupiny, místo nutnosti poskytují práva jeden po druhém. Vlastník prostředku nebo adresář můžete také udělit práva pro správu pro seznam členů někomu jinému, jako je například vedoucí oddělení nebo správce technické podpory, aby tato osoba přidávat a odebírat, podle potřeby. Další informace o tom, jak Správa vlastníků skupin najdete v tématu [Správa vlastníků skupin](active-directory-accessmanagement-managing-group-owners.md)

![Diagram správy přístupu ve službě Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Způsoby, jak přiřadit přístupová práva
Existují čtyři způsoby, jak přiřadit přístupová práva uživatelům prostředků:

- **Přímého přiřazení.** Vlastník prostředku přiřadí uživatele přímo k prostředku.

- **Skupina přiřazení.** Vlastník prostředku přiřadí k prostředku, který automaticky poskytuje všechny členy skupiny umožňuje přístup k prostředku skupiny Azure AD. Členství ve skupině spravuje vlastník skupiny a vlastníka prostředku, takže buď vlastník, přidat nebo odebrat členy ze skupiny. Další informace o přidávání nebo odebírání členství ve skupinách najdete v tématu [postupy: Přidat nebo odebrat skupiny z jiné skupiny pomocí portálu Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Přiřazování na základě pravidel.** Vlastník prostředku vytvoří skupinu a pravidlo používá k definování, kteří uživatelé jsou přiřazení ke konkrétnímu prostředku. Toto pravidlo je na základě atributů, které jsou přiřazeny k jednotlivým uživatelům. Vlastník prostředku spravuje pravidla určující, které atributy a hodnoty jsou požadovány pro povolení přístupu prostředku. Další informace najdete v tématu [vytvoření dynamické skupiny a zkontrolovat stav](../users-groups-roles/groups-create-rule.md).

    Můžete také sledovat toto krátké video rychlé vysvětlení o vytváření a používání dynamických skupin:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Přiřazení externího úřadu.** Přístup pochází z externího zdroje, jako je například v místním adresáři nebo aplikaci SaaS. V takovém případě vlastník prostředku přiřadí skupinu a poskytne přístup k prostředku a potom spravuje externího zdroje členy skupiny.

   ![Diagram s přehledem správy přístupu](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Mohou uživatelé připojit skupiny bez přiřazení?
Vlastník skupiny můžete umožnit uživatelům najít skupin pro připojení, namísto jejich přiřazení. Vlastníka můžete také nastavit skupiny, aby automaticky přijímat všechny uživatele, kteří připojení nebo tak, aby vyžadovala schválení.

Jakmile uživatel požádá o připojení ke skupině, požadavek předá vlastníka skupiny. Pokud je to požadováno, vlastníka můžete schválit požadavek a uživatel informován o členství ve skupině. Pokud máte více vlastníkům a jeden z nich disapproves, uživatel obdrží oznámení, ale není přidán do skupiny. Další informace a pokyny o tom, jak vaše uživatelům umožněno žádat o připojení skupin najdete v tématu [nastavení Azure AD, takže uživatelé můžou požádat o připojení skupin](../users-groups-roles/groups-self-service-management.md)

## <a name="next-steps"></a>Další postup
Teď, když máte hodně Úvod ke správě přístupu pomocí skupin, je spustit ke správě vašich prostředků a aplikací.

- [Vytvořte novou skupinu pomocí Azure Active Directory](active-directory-groups-create-azure-portal.md) nebo [vytvořit a spravovat pomocí rutin Powershellu pro novou skupinu](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Použití skupin pro přiřazení přístupu k aplikaci SaaS integrované](../users-groups-roles/groups-saasapps.md)

- [Synchronizovat místní skupině do Azure pomocí služby Azure AD Connect](../hybrid/whatis-hybrid-identity.md)