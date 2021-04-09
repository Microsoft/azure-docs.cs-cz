---
title: Role správců pro klasický odběr, role Azure a role Azure AD
description: Popisuje různé role v rolích správců předplatného Azure Classic, rolích Azure a Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2021
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: c4ab8c9f4e1f9dae8c10e155729c639c089fb77a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727111"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Role správců pro klasický odběr, role Azure a role Azure AD

Pokud s Azure teprve začínáte, může být pro vás trochu obtížné vyznat se v různých rolích v Azure. Tento článek vám pomůže porozumět následujícím rolím a tomu, kdy je použít:
- Role klasického správce předplatného
- Role Azure
- Role Azure Active Directory (Azure AD)

## <a name="how-the-roles-are-related"></a>Jak spolu role souvisejí

Lepšímu porozumění rolí v Azure pomůže znalost trochy historie. Když byla poprvé služba Azure vydána, byl přístup k prostředkům spravován pomocí pouhých tří rolí správce: správce účtu, správce služeb a spolusprávce. Později se přidalo řízení přístupu na základě role (Azure RBAC) Azure. Azure RBAC je novější systém autorizace, který poskytuje podrobnou správu přístupu k prostředkům Azure. Azure RBAC zahrnuje mnoho předdefinovaných rolí, které je možné přiřadit v různých oborech, a umožňuje vám vytvářet vlastní role. Pro správu prostředků v Azure AD, jako jsou uživatelé, skupiny a domény, je k dispozici několik rolí Azure AD.

Následující diagram představuje podrobný přehled toho, jak souvisí role správců pro klasický odběr, role Azure a role Azure AD.

![Různé role v Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Role klasického správce předplatného

Správce účtu, správce služeb a spolusprávce jsou tři role klasického správce předplatného v Azure. Klasičtí správci předplatného mají úplný přístup k předplatnému Azure. Mohou spravovat prostředky pomocí portálu Azure Portal, rozhraní API Azure Resource Manageru a rozhraní API modelu nasazení Classic. Účet, který slouží k registraci v Azure, je automaticky nastaven jako účet správce účtu a správce služeb. Potom je možné přidat další spolusprávce. Správce služby a Co-Administrators mají ekvivalentní přístup k uživatelům, kterým byla přiřazena role vlastníka (role Azure) v oboru předplatného. Následující tabulka popisuje rozdíly mezi třemi rolemi klasického správce předplatného.

| Klasický správce předplatného | Omezení | Oprávnění | Poznámky |
| --- | --- | --- | --- |
| Správce účtu | 1 na účet Azure | <ul><li>Správa fakturace v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)</li><li>Správa všech předplatných v účtu</li><li>Vytváření nových předplatných</li><li>Rušení předplatných</li><li>Změna fakturace předplatného</li><li>Změna správce služeb</li></ul> | Koncepčně se jedná o vlastníka fakturace předplatného. |
| Správce služeb | 1 na předplatné Azure | <ul><li>Správa služeb na portálu [Azure Portal](https://portal.azure.com)</li><li>Zrušení předplatného</li><li>Přiřazení role spolusprávce uživatelům</li></ul> | Ve výchozím nastavení u nového předplatného je správce účtu současně i správcem služeb.<br>Správce služeb má stejný přístup jako uživatel, který má přidělenu roli vlastníka v oboru předplatného.<br>Správce služeb má úplný přístup k webu Azure Portal. |
| Spolusprávce | 200 na předplatné | <ul><li>Má stejná přístupová oprávnění jako správce služeb, ale nemůže měnit přidružení předplatných k adresářům Azure.</li><li>Může uživatelům přiřazovat role spolusprávce, ale nemůže měnit správce služeb.</li></ul> | Spolusprávce má stejný přístup jako uživatel, který má přidělenu roli vlastníka v oboru předplatného. |

V Azure Portal můžete spravovat Co-Administrators nebo zobrazit správce služby pomocí karty **Classic Administrators** .

![Správci předplatného Azure Classic v Azure Portal](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

V Azure Portal můžete zobrazit nebo změnit správce služby nebo zobrazit správce účtu v okně vlastností předplatného.

![Správce účtu a správce služeb na portálu Azure Portal](./media/rbac-and-directory-admin-roles/account-admin.png)

Další informace najdete v tématu [Správci předplatného Azure Classic](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Účet Azure a předplatná Azure

Účet Azure reprezentuje fakturační vztah. Účet Azure je identita uživatele, jedno nebo více předplatných Azure a přidružená skupina prostředků Azure. Osoba, která vytvoří účet, je správcem účtu pro všechna předplatná vytvořená v daném účtu. Tato osoba je také výchozím správcem služeb pro předplatné.

Předplatná Azure vám usnadňují organizaci přístupu k prostředkům Azure. Zároveň vám pomohou řídit způsob, jak je používání prostředků vykazováno, fakturováno a placeno. Každé předplatné může mít jiné nastavení fakturace a plateb. Můžete tedy mít jiná předplatná a jiné plány pro různé pobočky, oddělení, projekty a podobně. Každá služba patří do předplatného a pro programové operace se může vyžadovat ID předplatného.

Každé předplatné je přidruženo k adresáři služby Azure AD. Pokud chcete najít adresář, ke kterému je předplatné přidruženo, otevřete **odběry** v Azure Portal a pak vyberte předplatné, které se zobrazí v adresáři.

Účty a předplatná se spravují v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

## <a name="azure-roles"></a>Role Azure

Azure RBAC je systém autorizace založený na [Azure Resource Manageru](../azure-resource-manager/management/overview.md), který poskytuje podrobnou správu přístupu k prostředkům Azure, jako jsou výpočetní služby a úložiště. Azure RBAC zahrnuje více než 70 předdefinovaných rolí. Existují čtyři základní role Azure. První tři se vztahují ke všem typům prostředků:

| Role Azure | Oprávnění | Poznámky |
| --- | --- | --- |
| [Vlastník](built-in-roles.md#owner) | <ul><li>Úplný přístup ke všem prostředkům</li><li>Delegování přístupu na jiné uživatele</li></ul> | Správce služeb a spolusprávci mají přiřazenu roli vlastníka v oboru předplatného.<br>Platí pro všechny typy prostředků. |
| [Přispěvatel](built-in-roles.md#contributor) | <ul><li>Vytváření a správa všech typů prostředků Azure</li><li>Vytvořit nového tenanta v Azure Active Directory</li><li>Nemůže udělovat přístup ostatním</li></ul> | Platí pro všechny typy prostředků. |
| [Čtenář](built-in-roles.md#reader) | <ul><li>Zobrazení prostředků Azure</li></ul> | Platí pro všechny typy prostředků. |
| [Správce uživatelského přístupu](built-in-roles.md#user-access-administrator) | <ul><li>Správa uživatelských přístupů k prostředkům Azure</li></ul> |  |

Zbývající předdefinované role umožňují správu konkrétních prostředků Azure. Role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) například uživateli umožňuje vytvářet a spravovat virtuální počítače. Seznam všech předdefinovaných rolí najdete v tématu [předdefinované role Azure](built-in-roles.md).

Pouze Azure Portal a rozhraní API Azure Resource Manager podporují službu Azure RBAC. Uživatelé, skupiny a aplikace, kterým jsou přiřazené role Azure, nemůžou používat [rozhraní API modelu nasazení Classic Azure Classic](../azure-resource-manager/management/deployment-models.md).

V Azure Portal se přiřazení rolí pomocí Azure RBAC zobrazuje v okně **řízení přístupu (IAM)** . Toto okno se dá najít v celém portálu, jako jsou skupiny pro správu, předplatná, skupiny prostředků a různé prostředky.

![Okno Řízení přístupu (IAM) na portálu Azure Portal](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Po kliknutí na kartu **role** se zobrazí seznam předdefinovaných a vlastních rolí.

![Předdefinované role na portálu Azure Portal](./media/rbac-and-directory-admin-roles/roles-list.png)

Další informace najdete v tématu [přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md).

## <a name="azure-ad-roles"></a>Role Azure AD

Role Azure AD slouží ke správě prostředků Azure AD v adresáři, jako je vytváření nebo úpravy uživatelů, přiřazování rolí pro správu ostatním, Resetování uživatelských hesel, správě uživatelských licencí a správě domén. Následující tabulka popisuje několik nejdůležitějších rolí Azure AD.

| Role Azure AD | Oprávnění | Poznámky |
| --- | --- | --- |
| [Globální správce](../active-directory/roles/permissions-reference.md#global-administrator) | <ul><li>Správa přístupu ke všem administrativním funkcím v Azure Active Directory i službám federovaným do Azure Active Directory</li><li>Přiřazení rolí správce dalším uživatelům</li><li>Resetování hesel uživatelů a všech ostatních správců</li></ul> | Osoba, která se zaregistruje v tenantovi Azure Active Directory, se stává globálním správcem. |
| [Správce uživatele](../active-directory/roles/permissions-reference.md#user-administrator) | <ul><li>Vytváření a správa všech aspektů uživatelů a skupin</li><li>Správa lístků podpory</li><li>Monitorování stavu služby</li><li>Změna hesel pro uživatele, správce helpdesku a další správce uživatelů</li></ul> |  |
| [Správce fakturace](../active-directory/roles/permissions-reference.md#billing-administrator) | <ul><li>Nové nákupy</li><li>Správa předplatných</li><li>Správa lístků podpory</li><li>Monitorování stavu služby</li></ul> |  |

V Azure Portal uvidíte seznam rolí Azure AD v okně **role a správci** . Seznam všech rolí Azure AD najdete v tématu [oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md).

![Role Azure AD v Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Rozdíly v rolích Azure a Azure AD

Role Azure na nejvyšší úrovni řídí oprávnění ke správě prostředků Azure, zatímco role Azure AD řídí oprávnění ke správě Azure Active Directorych prostředků. Následující tabulka obsahuje přehled některých rozdílů.

| Role Azure | Role Azure AD |
| --- | --- |
| Správa přístupu k prostředkům Azure | Správa přístupu k prostředkům Azure Active Directory |
| Podpora vlastních rolí | Podpora vlastních rolí |
| Možnost zadání oboru na více úrovních (skupina pro správu, předplatné, skupina prostředků, prostředek) | [Rozsah](../active-directory/roles/custom-overview.md#scope) lze zadat na úrovni tenanta (v rámci celé organizace) nebo na jednotlivé objekty (například na konkrétní aplikaci). |
| Dostupnost informací o roli na portálu Azure Portal, v Azure CLI, Azure PowerShellu, šablonách Azure Resource Manageru, rozhraní REST API | K informacím o rolích se dá získat pøístup na portálu pro správu Azure, Microsoft 365 centru pro správu, Microsoft Graph, AzureAD PowerShellu. |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Překrývají se role Azure a role Azure AD?

Ve výchozím nastavení role Azure a role Azure AD nezahrnují Azure a Azure AD. Pokud ale globální správce zvyšuje svůj přístup, vyberte v Azure Portal přepínač **Správa přístupu pro prostředky Azure** . globální správce udělí roli [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) (role Azure) ve všech předplatných pro konkrétního tenanta. Role správce uživatelských přístupů uživateli umožňuje udělit dalším uživatelům přístup k prostředkům Azure. Tento přepínač může být užitečný pro opakované získání přístupu k předplatnému. Další informace najdete v tématu [zvýšení úrovně přístupu ke správě všech předplatných Azure a skupin pro správu](elevate-access-global-admin.md).

Několik rolí Azure AD zahrnuje Azure AD a Microsoft 365, jako jsou globální role správců a správců uživatelů. Pokud jste například členem role globálního správce, máte možnosti globálního správce v Azure AD a Microsoft 365, jako je například provádění změn v systému Microsoft Exchange a Microsoft SharePoint. Ve výchozím nastavení ale globální správce nemá přístup k prostředkům Azure.

![Role Azure RBAC oproti rolím Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Další kroky

- [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](overview.md)
- [Oprávnění role správce v Azure Active Directory](../active-directory/roles/permissions-reference.md)
- [Správci předplatného Azure Classic](classic-administrators.md)
