---
title: Správa přístupu k prostředkům & aplikací pomocí skupin – Azure AD
description: Přečtěte si, jak spravovat přístup k cloudovým aplikacím, místním aplikacím a prostředkům vaší organizace pomocí Azure Active Directory skupin.
services: active-directory
author: ajburnle
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
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369110"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Správa přístupu k aplikacím a prostředkům pomocí skupin služby Azure Active Directory
Azure Active Directory (Azure AD) umožňuje používat skupiny ke správě přístupu ke cloudovým aplikacím, místním aplikacím a vašim prostředkům. Vaše prostředky můžou být součástí organizace Azure AD, například oprávnění ke správě objektů prostřednictvím rolí v Azure AD nebo externích organizacím, jako jsou například aplikace pro aplikace SaaS (software jako služba), služby Azure, weby SharePoint a místní prostředky.

>[!NOTE]
> V Azure Portal uvidíte některé skupiny, jejichž podrobnosti o členství a skupinách nemůžete spravovat na portálu:
>
> - Skupiny synchronizované z místní služby Active Directory je možné spravovat jenom v místní službě Active Directory.
> - Jiné typy skupin, například distribuční seznamy a skupiny zabezpečení s povolenými e-maily, se spravují pouze v centru pro správu Exchange nebo v centru pro správu Microsoft 365. Abyste mohli spravovat tyto skupiny, musíte se přihlásit do centra pro správu Exchange nebo Microsoft 365 centra pro správu.

## <a name="how-access-management-in-azure-ad-works"></a>Jak funguje Správa přístupu v Azure AD

Azure AD pomáhá poskytovat přístup k prostředkům vaší organizace poskytnutím přístupových práv jednomu uživateli nebo celé skupině Azure AD. Použití skupin umožňuje vlastníkovi prostředku (nebo vlastníkovi adresáře Azure AD) přiřadit sadu přístupových oprávnění všem členům skupiny – místo poskytování práv jedno po druhém. Vlastník prostředku nebo adresáře může také udělit práva pro správu seznamu členů někomu jinému, jako je například manažer oddělení nebo správce helpdesku, aby tato osoba mohla podle potřeby přidávat a odebírat členy. Další informace o tom, jak spravovat vlastníky skupiny, najdete v tématu [Správa vlastníků skupin](active-directory-accessmanagement-managing-group-owners.md) .

![Diagram správy přístupu ve službě Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Způsoby přiřazení přístupových práv

Existují čtyři způsoby přiřazení přístupových práv k prostředkům uživatelům:

- **Přímé přiřazení.** Vlastník prostředku přiřadí uživatele přímo k prostředku.

- **Přiřazení skupiny** Vlastník prostředku přiřadí ke zdroji skupinu Azure AD, která automaticky všem členům skupiny poskytne přístup k prostředku. Členství ve skupině je spravováno vlastníkem skupiny i vlastníkem prostředku a umožňuje tak vlastníkům přidat nebo odebrat členy ze skupiny. Další informace o přidání nebo odebrání členství ve skupině najdete v tématu [Postup: Přidání nebo odebrání skupiny z jiné skupiny pomocí portálu Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Přiřazení na základě pravidel** Vlastník prostředku vytvoří skupinu a použije pravidlo k určení uživatelů, kteří jsou přiřazeni k určitému prostředku. Pravidlo je založeno na atributech, které jsou přiřazeny jednotlivým uživatelům. Vlastník prostředku spravuje pravidlo, které určuje, které atributy a hodnoty jsou požadovány pro povolení přístupu k prostředku. Další informace najdete v tématu [Vytvoření dynamické skupiny a kontroly stavu](../enterprise-users/groups-create-rule.md).

    Toto krátké video můžete také využít k rychlému vysvětlení vytváření a používání dynamických skupin:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Přiřazení externích autorit.** Přístup pochází z externího zdroje, jako je například místní adresář nebo aplikace SaaS. V této situaci vlastník prostředku přiřadí skupinu, aby poskytla přístup k prostředku, a pak členové skupiny spravuje externí zdroj.

   ![Diagram s přehledem správy přístupu](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Můžou uživatelé připojit skupiny bez přiřazení?
Vlastník skupiny může uživatelům umožnit, aby k připojení našli vlastní skupiny, místo abyste je přiřadili. Vlastník může také nastavit skupinu tak, aby automaticky přijímala všechny uživatele, kteří se připojují nebo vyžadují schválení.

Jakmile uživatel požádá o připojení ke skupině, požadavek se přepošle vlastníkovi skupiny. Pokud je to nutné, může vlastník žádost schválit a uživatel bude upozorněn na členství ve skupině. Pokud ale máte více vlastníků a jeden z nich je vyřadí, uživateli se zobrazí oznámení, ale do skupiny se nepřidá. Další informace a pokyny, jak umožnit vašim uživatelům požádat o připojení ke skupinám, najdete v tématu [Nastavení Azure AD, aby si uživatelé mohli požádat o připojení ke skupinám](../enterprise-users/groups-self-service-management.md) .

## <a name="next-steps"></a>Další kroky
Teď, když máte k dispozici část úvodu ke správě přístupu pomocí skupin, začnete spravovat své prostředky a aplikace.

- [Vytvoření nové skupiny pomocí Azure Active Directory](active-directory-groups-create-azure-portal.md) nebo [Vytvoření a Správa nové skupiny pomocí rutin prostředí PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Přiřazení přístupu k integrované aplikaci SaaS pomocí skupin](../enterprise-users/groups-saasapps.md)

- [Synchronizace místní skupiny s Azure pomocí Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
