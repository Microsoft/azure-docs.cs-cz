---
title: Pochopit různé role v Azure | Dokumentace Microsoftu
description: Vysvětluje různé role v Azure – Classic role správců předplatného, role řízení přístupu Azure na základě rolí a rolí správce Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 6285614b42b469feeb63b880976e79828838e675
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37346916"
---
# <a name="understand-the-different-roles-in-azure"></a>Pochopit různé role v Azure

Pokud jste ještě do Azure, může být docela oříšek zjistit všechny různé role v Azure. Tento článek pomůže popisují následující role a při každé byste použili:
- Role správce v klasickém modelu předplatného
- Role řízení přístupu na základě rolí Azure
- Role Správce služby Azure Active Directory (Azure AD)

## <a name="how-the-roles-are-related"></a>Jak se týkají role

Pro lepší pochopení role v Azure, je důležité znát některé z historie. Po Azure byl zpočátku vydání, přístup k prostředkům se spravované pomocí pouhých tří rolí správce: účet správce, Správce služeb a spolupracujících správců. Přidala se později, řízení přístupu na základě role (RBAC) pro prostředky Azure. Azure RBAC je založená na novější systém autorizace [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) , který poskytuje propracovanou správu přístupu k prostředkům Azure. RBAC obsahuje mnoho předdefinovaných rolí, můžou být přiřazené na různých oborech a umožňuje vytvářet vlastní role. Ke správě prostředků ve službě Azure AD, jako jsou uživatelé, skupiny nebo domény, existuje několik rolí správce Azure AD.

V následujícím diagramu je souhrnný přehled, jak jsou související s rolí správce v klasickém modelu předplatného, role Azure RBAC a rolích správce Azure AD.

![Různé role v Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrators"></a>Správci v klasickém modelu předplatného

Účet správce, Správce služeb a spolupracujících správců jsou tři role správce klasickém modelu předplatného v Azure. Klasickém modelu předplatného Správci mají plný přístup k předplatnému Azure. Můžou spravovat prostředků pomocí webu Azure portal, rozhraní API Azure Resource Manager a modelu nasazení classic rozhraní API. Účet, který se používá k registraci do Azure se automaticky nastaví jako účet správce a Správce služby. Potom je možné přidat další Spolusprávce. Správce služeb a spolupracujících správců mají odpovídající přístup uživatelů, kteří mají přiřazenou roli vlastníka (role Azure RBAC) v oboru předplatného. Následující tabulka popisuje rozdíly mezi tyto tři role pro správu klasickém modelu předplatného.

| Klasický správce předplatného | Omezení | Oprávnění | Poznámky |
| --- | --- | --- | --- |
| Správce účtu | 1 za účet Azure | <ul><li>Přístup [centra účtů Azure](https://account.azure.com/Subscriptions)</li><li>Spravovat všechny odběry v rámci účtu</li><li>Vytvořit nové předplatné</li><li>Zrušit předplatná</li><li>Změny fakturace za předplatné</li><li>Změna správce služby</li></ul> | Koncepčně fakturační vlastníkem předplatného.|
| Správce služeb | 1 za předplatné Azure | <ul><li>Spravovat služby v [webu Azure portal](https://portal.azure.com)</li><li>Přiřadit uživatele k roli Spolusprávce</li></ul> | Ve výchozím nastavení nového předplatného je správcem účtu pro příslušné také Správce služeb.<br>Správce služeb má ekvivalentní přístup uživatele, který má přiřazenou roli vlastník v oboru předplatného. |
| Spolusprávce | 200 na předplatné | <ul><li>Stejný přístup k oprávnění jako správce služeb, ale nemůže změnit přidružení předplatných k adresářům Azure</li><li>Přiřadit uživatele k roli Spolusprávce, ale nemůže změnit správce služeb</li></ul> | Spolupracující správce má ekvivalentní přístup uživatele, který má přiřazenou roli vlastník v oboru předplatného. |

Na webu Azure Portal uvidíte, kdo je přiřazen k účtu správce a Správce služby zobrazením vlastností vašeho předplatného.

![Účet správce a Správce služby na webu Azure Portal](./media/rbac-and-directory-admin-roles/account-admin-service-admin.png)

Informace o tom, jak přidat nebo změnit správci předplatného najdete v tématu [přidat nebo změnit správce předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) v dokumentaci k fakturaci Azure.

### <a name="azure-account-and-azure-subscriptions"></a>Účet Azure a předplatných Azure

Účet Azure představuje fakturační vztah. Účet Azure je identita uživatele, jeden nebo více předplatných Azure a přidruženou sadu prostředků Azure. Osoba, která vytvoří účet je správcem účtu pro všechna předplatná vytvořená v příslušném účtu. Tato osoba je také výchozí správce služeb pro předplatné.

Předplatná Azure vám pomohou organizovat přístup k prostředkům Azure. Zároveň vám pomohou řídit způsob, jak je používání prostředků vykazováno, fakturováno a placeno. Každé předplatné může mít jiné nastavení fakturace a plateb, takže můžete mít jiná předplatná a jiné plány pro office, oddělení, projektů a tak dále. Každá služba patří do předplatného a ID předplatného se může vyžadovat programových operací.

Účty a předplatná se spravují v [centra účtů Azure](https://account.azure.com/Subscriptions).
## <a name="azure-rbac-roles"></a>Role Azure RBAC

Azure RBAC je systém, který poskytuje propracovanou správu přístupu k prostředkům Azure, jako jsou výpočetní výkon a úložiště. Azure RBAC obsahuje více než 60 předdefinované role. Existují čtyři základní role RBAC. První tři platí pro všechny typy prostředků:

| Role Azure RBAC | Oprávnění | Poznámky |
| --- | --- | --- |
| [Vlastník](built-in-roles.md#owner) | <ul><li>Úplný přístup ke všem prostředkům</li><li>Delegovat přístup ostatním uživatelům</li></ul> | Správce služeb a spolupracujících správců mají přiřazenou roli vlastník v oboru předplatného<br>Platí pro všechny typy prostředků. |
| [Přispěvatel](built-in-roles.md#contributor) | <ul><li>Vytvářet a spravovat všechny typy prostředků Azure</li><li>Nelze udělit přístup ostatním uživatelům</li></ul> | Platí pro všechny typy prostředků. |
| [Čtenář](built-in-roles.md#reader) | <ul><li>Zobrazení prostředků služby Azure</li></ul> | Platí pro všechny typy prostředků. |
| [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) | <ul><li>Správa přístupu uživatelů k prostředkům Azure</li></ul> |  |

Zbývající část předdefinované role povolit správu konkrétních prostředků Azure. Například [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) role umožňuje uživatelům vytvářet a spravovat virtuální počítače. Seznam předdefinovaných rolí najdete v tématu [předdefinované role](built-in-roles.md).

RBAC podporují pouze na webu Azure portal a rozhraní API Azure Resource Manageru. Uživatelé, skupiny a aplikace, které jsou přiřazené role RBAC nelze použít [modelu nasazení Azure classic API](../azure-resource-manager/resource-manager-deployment-model.md).

Na webu Azure Portal, přiřazení rolí pomocí RBAC joinkind **řízení přístupu (IAM)** okno. V tomto okně můžete najít v rámci portálu, jako je například předplatná, skupiny prostředků a různé prostředky.

![Okna ovládacího prvku (IAM) přístupu na webu Azure Portal](./media/rbac-and-directory-admin-roles/access-control.png)

Po kliknutí **role** možnost, zobrazí se seznam předdefinovaných a vlastních rolí.

![Předdefinované role na webu Azure Portal](./media/rbac-and-directory-admin-roles/built-in-roles.png)

## <a name="azure-ad-administrator-roles"></a>Role Správce služby Azure AD

Správce služby Azure AD, které role se používají ke správě prostředků Azure AD v adresáři, jako vytvořit nebo upravit uživatele, přiřazovat role správců jiným uživatelům, resetovat hesla uživatelů, spravovat uživatelské licence a spravovat domény. Následující tabulka popisuje některé důležité role správce Azure AD.

| Role správce Azure AD | Oprávnění | Poznámky |
| --- | --- | --- |
| [Globální správce](../active-directory/active-directory-assign-admin-roles-azure-portal.md#company-administrator) | <ul><li>Správa přístupu ke všem funkcím pro správu v Azure Active Directory, jakož i služby, které federaci s Azure Active Directory</li><li>Přiřazení role správce ostatním uživatelům</li><li>Resetovat heslo kteréhokoliv uživatele a všech ostatních správců</li></ul> | Osoba, která se zaregistruje k tenantovi Azure Active Directory se stane globálním správcem. |
| [Správce uživatelů](../active-directory/active-directory-assign-admin-roles-azure-portal.md#user-account-administrator) | <ul><li>Vytvářet a spravovat všechny aspekty uživatelů a skupin</li><li>Spravovat lístky podpory</li><li>Monitorování stavu služby</li><li>Změna hesla pro uživatele, správců technické podpory a jiným správcům uživatele</li></ul> |  |
| [Správce technické podpory](../active-directory/active-directory-assign-admin-roles-azure-portal.md#helpdesk-administrator) | <ul><li>Změna hesla pro uživatele a jiným správcům technické podpory</li><li>Správa žádostí o služby</li><li>Monitorování stavu služby</li></ul> |  |
| [Správce fakturace](../active-directory/active-directory-assign-admin-roles-azure-portal.md#billing-administrator) | <ul><li>Nákupy</li><li>Správa předplatných</li><li>Spravovat lístky podpory</li><li>Monitorování stavu služby</li></ul> |  |

Seznam všech rolí správce Azure AD najdete v tématu [přiřazení rolí správce v Azure Active Directory](/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

Na webu Azure Portal, můžete přiřadit role správce Azure AD v **Azure Active Directory** okno.

![Role správce Azure AD na webu Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-rbac-roles-and-azure-ad-administrator-roles"></a>Rozdíly mezi role Azure RBAC a rolích správce Azure AD

Role Azure RBAC na vysoké úrovni, řídit oprávnění ke správě prostředků Azure, zatímco role správce Azure AD, řídit oprávnění ke správě prostředků Azure Active Directory. Následující tabulka porovnává některé rozdíly.

| Role Azure RBAC | Role Správce služby Azure AD |
| --- | --- |
| Správa přístupu k prostředkům Azure | Správa přístupu k prostředkům Azure Active Directory |
| Podporuje vlastní role | Nelze vytvořit vlastní role |
| Obor se dá nastavit na více úrovních (skupiny pro správu, předplatné, skupinu prostředků, prostředků) | Obor je na úrovni tenanta |
| Informace o rolích mohou získat přístup k webu Azure Portal, rozhraní příkazového řádku Azure, Azure Powershellu, šablon Azure Resource Manageru, rozhraní REST API | Informace o rolích je přístupná Azure portal, Office 365 admin portálu pro správu v Microsoft Graphu, Azure AD Powershellu |

### <a name="do-azure-rbac-roles-and--azure-ad-administrator-roles-overlap"></a>Role Azure RBAC a rolích správce Azure AD překrývat?

Ve výchozím nastavení, role Azure RBAC a rolích správce Azure AD nejsou umístěné Azure i v Azure AD. Ale pokud jejich přístup výběrem zvýší oprávnění globálního správce **globální správce může spravovat předplatná Azure a skupiny pro správu** přepnout na webu Azure Portal, globálního správce udělí [ Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) rolí (RBAC role) na všechna předplatná pro konkrétního tenanta. Role správce přístupu uživatelů umožňuje uživateli udělit jiným uživatelům přístup k prostředkům Azure. Tento přepínač může být užitečné, abyste znovu získali přístup k předplatnému. Další informace najdete v tématu [zvýšení úrovně přístupu jako správce Azure AD](elevate-access-global-admin.md).

Několik rozpětí role správce Azure AD Azure AD a Microsoft Office 365, jako jsou role Globální správce a Správce uživatelů. Pokud jste členem role Globální správce, získáte možnosti globálního správce ve službě Azure AD a Office 365, jako je například vytváření změní na Microsoft Exchange a Microsoft SharePoint. Ve výchozím nastavení, ale globální správce nemá přístup k prostředkům Azure.

![Azure RBAC a rolích správce Azure AD](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Další postup

- [Co je řízení přístupu na základě rolí (RBAC)?](overview.md)
- [Přiřazení rolí správce v Azure Active Directory](/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Přidat nebo změnit správce předplatného Azure](/azure/billing/billing-add-change-azure-subscription-administrator)
