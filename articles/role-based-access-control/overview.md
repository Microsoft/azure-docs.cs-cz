---
title: Co je řízení přístupu na základě role Azure (Azure RBAC)?
description: Získejte přehled řízení přístupu na základě role v Azure (Azure RBAC). K řízení přístupu k prostředkům Azure použijte přiřazení rolí.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d186fd2182603709921f876d0bc212762cb8586b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735857"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Co je řízení přístupu na základě role Azure (Azure RBAC)?

Správa přístupu ke cloudovým prostředkům je velmi důležitou funkcí pro jakoukoli organizaci, která používá cloud. Řízení přístupu na základě role v Azure (Azure RBAC) pomáhá spravovat, kdo má přístup k prostředkům Azure, co s těmito prostředky může dělat a k jakým oblastem mají přístup.

Azure RBAC je autorizačním systémem postaveným na [Azure Resource Manager](../azure-resource-manager/management/overview.md) , který poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure.

## <a name="what-can-i-do-with-azure-rbac"></a>Co můžu dělat s Azure RBAC?

Tady je několik příkladů toho, co můžete dělat s Azure RBAC:

- Jednomu uživateli můžete povolit správu virtuálních počítačů v předplatném a jinému uživateli správu virtuálních sítí.
- Skupině DBA můžete povolit správu databází SQL v předplatném.
- Uživateli můžete povolit správu všech prostředků ve skupině prostředků, například virtuálních počítačů, webů a podsítí.
- Aplikaci můžete povolit přístup ke všem prostředkům ve skupině prostředků.

## <a name="how-azure-rbac-works"></a>Jak funguje Azure RBAC

Způsob řízení přístupu k prostředkům pomocí Azure RBAC je vytvoření přiřazení rolí. Jedná se o klíčový koncept, který vám pomůže pochopit – jak se vynutila oprávnění. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor.

### <a name="security-principal"></a>Objekt zabezpečení

Objekt *zabezpečení* je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která žádá o přístup k prostředkům Azure.

![Objekt zabezpečení pro přiřazení role](./media/overview/rbac-security-principal.png)

- Uživatel – jednotlivec, který má profil ve službě Azure Active Directory. Můžete také přiřadit role uživatelům v jiných tenantech. Informace o uživatelích v jiných organizacích najdete v článku o [B2B ve službě Azure Active Directory](../active-directory/b2b/what-is-b2b.md).
- Skupina – skupina uživatelů vytvořená ve službě Azure Active Directory. Když přiřadíte roli skupině, budou mít danou roli všichni její uživatelé. 
- Instanční objekt – identita zabezpečení, kterou používají aplikace nebo služby pro přístup ke konkrétním prostředkům Azure. Můžete si ji představit jako *identitu uživatele* (uživatelské jméno a heslo nebo certifikát) pro aplikaci.
- Spravovaná identita – identita v Azure Active Directory, kterou automaticky spravuje Azure. [Spravované identity](../active-directory/managed-identities-azure-resources/overview.md) obvykle používáte při vývoji cloudových aplikací pro správu přihlašovacích údajů pro ověřování ve službách Azure.

### <a name="role-definition"></a>Definice role

*Definice role* je kolekce oprávnění. Obvykle se nazývá jenom *role*. Definice role poskytuje seznam operací, které je možné provádět, například čtení, zápis a odstranění. Role mohou být souhrnné, například vlastník, nebo konkrétní, například čtenář virtuálních počítačů.

![Definice role pro přiřazení role](./media/overview/rbac-role-definition.png)

Azure obsahuje několik [předdefinovaných rolí](built-in-roles.md), které můžete využít. V následujícím seznamu najdete čtyři základní předdefinované role. První tři se vztahují ke všem typům prostředků.

- [Vlastník](built-in-roles.md#owner) – má úplný přístup ke všem prostředkům, včetně práva delegovat přístup na ostatní.
- [Přispěvatel](built-in-roles.md#contributor) – může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup jiným uživatelům.
- [Čtenář](built-in-roles.md#reader) – může zobrazit existující prostředky Azure.
- [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) – může spravovat uživatelský přístup k prostředkům Azure.

Zbývající předdefinované role umožňují správu konkrétních prostředků Azure. Role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) například uživateli umožňuje vytvářet a spravovat virtuální počítače. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [vlastní role Azure](custom-roles.md).

Azure obsahuje operace s daty, které umožňují udělit přístup k datům v rámci objektu. Pokud má uživatel například přístup ke čtení dat u účtu úložiště, bude moci číst objekty blob nebo zprávy v rámci daného účtu úložiště. Další informace najdete v tématu [vysvětlení definic rolí Azure](role-definitions.md).

### <a name="scope"></a>Rozsah

*Obor* je sada prostředků, na které se vztahuje přístup. Když přiřadíte roli, můžete definováním oboru dále omezit akce, které jsou povoleny. To je užitečné v případě, kdy někomu chcete udělit roli [Přispěvatel webů](built-in-roles.md#website-contributor), ale pouze pro jednu skupinu prostředků.

V Azure můžete zadat obor na více úrovních: na úrovni [skupiny pro správu](../governance/management-groups/overview.md), předplatného, skupiny prostředků nebo prostředku. Obory jsou strukturovány ve vztahu nadřazený-podřízený obor.

![Obor přiřazení role](./media/overview/rbac-scope.png)

Když udělíte přístup na úrovni nadřízeného oboru, podřízené obory zdědí příslušná oprávnění. Příklad:

- Pokud uživateli přiřadíte roli [Vlastník](built-in-roles.md#owner) na úrovni oboru skupiny pro správu, může tento uživatel spravovat vše ve všech předplatných v příslušné skupině pro správu.
- Pokud přiřadíte roli [Čtenář](built-in-roles.md#reader) skupině v oboru předplatného, členové takové skupiny mohou zobrazit všechny skupiny prostředků a prostředky v daném předplatném.
- Pokud přiřadíte roli [Přispěvatel](built-in-roles.md#contributor) aplikaci v oboru skupiny prostředků, může aplikace spravovat prostředky všech typů v dané skupině prostředků, ale už ne žádné jiné skupiny prostředků v předplatném.

### <a name="role-assignments"></a>Přiřazení rolí

*Přiřazení role* je proces připojení definice role k uživateli, skupině, objektu služby nebo spravované identitě v konkrétním oboru pro účely udělení přístupu. Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role.

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu byla marketingové skupině přiřazena role [Přispěvatel](built-in-roles.md#contributor) pro skupinu prostředků prodeje farmaceutických výrobků. To znamená, že uživatelé marketingové skupiny mohou vytvářet a spravovat prostředky Azure ve skupině prostředků prodeje farmaceutických výrobků. Uživatelé marketingové skupiny nemají přístup k prostředkům mimo skupinu prostředků prodeje farmaceutických výrobků, pokud nejsou součástí přiřazení jiné role.

![Přiřazení role za účelem řízení přístupu](./media/overview/rbac-overview.png)

Přiřazení rolí můžete vytvořit pomocí portálu Azure Portal, Azure CLI, Azure PowerShellu, sad Azure SDK nebo rozhraní REST API. V každé skupině pro správu můžete mít až **2000** přiřazení rolí v každém předplatném a v **500** přiřazení rolí. K vytváření a odebírání přiřazení rolí musíte mít oprávnění `Microsoft.Authorization/roleAssignments/*`. Toto oprávnění se uděluje prostřednictvím role [Vlastník](built-in-roles.md#owner) nebo [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator).

## <a name="multiple-role-assignments"></a>Přiřazení více rolí

Co se stane, když máte více překrývajících se přiřazení rolí? Azure RBAC je doplňkovým modelem, takže vaše skutečná oprávnění jsou součtem přiřazení rolí. Vezměte v úvahu následující příklad, kdy je uživateli udělena role Přispěvatel v oboru předplatného a role čtenář ve skupině prostředků. Součet oprávnění přispěvatele a oprávnění čtenářů je efektivně rolí přispěvatele pro skupinu prostředků. Proto v takovém případě přiřazení role čtenáře nemá žádný vliv.

![Přiřazení více rolí](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Přiřazení zamítnutí

Dřív se jednalo o model jenom pro povolování bez odepření, ale teď Azure RBAC podporuje přiřazení odepřít omezenému způsobu. Podobně jako u přiřazení role *přiřazení zamítnutí* připojuje sadu akcí Odepřít pro uživatele, skupinu, instanční objekt nebo spravovanou identitu v konkrétním oboru pro účely odepření přístupu. Přiřazení role definuje sadu akcí, které jsou *povoleny*, zatímco přiřazení zamítnutí definuje sadu akcí, které nejsou *povoleny*. Jinými slovy, přiřazení zamítnutí blokuje uživatelům možnost provádět určité akce i v případě, že přiřazení role jim přístup uděluje. Přiřazení zamítnutí mají přednost před přiřazením rolí. Další informace najdete v tématu [Vysvětlení přiřazení odmítnutí Azure](deny-assignments.md).

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Jak Azure RBAC určuje, jestli má uživatel přístup k prostředku

Níže jsou uvedené kroky vysoké úrovně, které Azure RBAC používá k určení, jestli máte přístup k prostředku na rovině správy. Tyto principy je užitečné chápat, pokud se snažíte vyřešit potíže s přístupem.

1. Uživatel (nebo instanční objekt služby) získá token pro Azure Resource Manager.

    Tento token obsahuje členství uživatele ve skupinách (včetně tranzitivního členství ve skupinách).

1. Uživatel provede s připojeným tokenem volání rozhraní REST API do Azure Resource Manageru.

1. Azure Resource Manager načte všechna přiřazení rolí a přiřazení zamítnutí vztahující se k prostředku, na kterém se akce provádí.

1. Azure Resource Manageru zúží přiřazení rolí, které se vztahují na tohoto uživatele nebo jeho skupinu, a určí, jaké role má uživatel u daného prostředku.

1. Azure Resource Manager určí, zda je akce ve volání rozhraní API je zahrnuta v rolích, které uživatel pro tento prostředek má.

1. Pokud uživatel nemá roli s akcí v požadovaném oboru, přístup se neudělí. V opačném případě Azure Resource Manager ověří, zda platí přiřazení zamítnutí.

1. Pokud ano, přístup se zablokuje. Pokud ne, přístup je udělen.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: zobrazení přístupu uživatele k prostředkům Azure](check-access.md)
- [Přidání nebo odebrání přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md)
- [Vysvětlení různých rolí](rbac-and-directory-admin-roles.md)
- [Architektura pro přijetí do cloudu: Správa přístupu k prostředkům v Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
