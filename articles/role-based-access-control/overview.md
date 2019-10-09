---
title: Co je řízení přístupu na základě role (RBAC) pro prostředky Azure? | Microsoft Docs
description: Získejte přehled řízení přístupu na základě role (RBAC) pro prostředky Azure. K řízení přístupu k prostředkům Azure použijte přiřazení rolí.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 59708be1de53ab231ccb6dc89bd56c795734fccc
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176822"
---
# <a name="what-is-role-based-access-control-rbac-for-azure-resources"></a>Co je řízení přístupu na základě role (RBAC) pro prostředky Azure?

Správa přístupu pro cloudové prostředky je důležitou funkcí pro všechny organizace, které Cloud využívají. Řízení přístupu na základě role (RBAC) pomáhá spravovat, kdo má přístup k prostředkům Azure, co s těmito prostředky může dělat a k jakým oblastem mají přístup.

RBAC je autorizační systém založený na [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) , který poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure.

## <a name="what-can-i-do-with-rbac"></a>Co můžu dělat s RBAC?

Tady je několik příkladů toho, co můžete dělat s RBAC:

- Umožňuje jednomu uživateli spravovat virtuální počítače v rámci předplatného a dalšího uživatele pro správu virtuálních sítí.
- Umožňuje skupině DBA spravovat databáze SQL v předplatném.
- Umožní uživateli spravovat všechny prostředky ve skupině prostředků, jako jsou virtuální počítače, weby a podsítě.
- Povolení aplikace pro přístup ke všem prostředkům ve skupině prostředků

## <a name="best-practice-for-using-rbac"></a>Osvědčené postupy použití RBAC

Pomocí RBAC můžete oddělit povinnosti v rámci svého týmu a udělit uživatelům pouze množství přístupu, které potřebují k provedení svých úloh. Místo udělení všech neomezených oprávnění v rámci předplatného Azure nebo prostředků můžete v určitém oboru použít jenom určité akce.

Při plánování strategie řízení přístupu je osvědčeným postupem udělit uživatelům nejnižší oprávnění k tomu, aby dosáhli své práce. Následující diagram znázorňuje navrhovaný vzor pro použití RBAC.

![RBAC a nejnižší oprávnění](./media/overview/rbac-least-privilege.png)

## <a name="how-rbac-works"></a>Jak funkce RBAC funguje

Způsob řízení přístupu k prostředkům pomocí RBAC je vytvoření přiřazení rolí. Jedná se o klíčový koncept, který vám pomůže pochopit – jak se vynutila oprávnění. Přiřazení role se skládá ze tří prvků: objekt zabezpečení, definice role a obor.

### <a name="security-principal"></a>Objekt zabezpečení

Objekt *zabezpečení* je objekt, který představuje uživatele, skupinu, instanční objekt nebo spravovanou identitu, která žádá o přístup k prostředkům Azure.

![Objekt zabezpečení pro přiřazení role](./media/overview/rbac-security-principal.png)

- Uživatel – jednotlivec, který má profil v Azure Active Directory. Role můžete přiřadit také uživatelům v jiných klientech. Informace o uživatelích v jiných organizacích najdete v tématu [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md).
- Skupina – sada uživatelů vytvořená v Azure Active Directory. Když skupině přiřadíte roli, budou mít všichni uživatelé v této skupině tuto roli. 
- Instanční objekt – identita zabezpečení, kterou aplikace nebo služby používá pro přístup ke konkrétním prostředkům Azure. Můžete si ho představit jako *identitu uživatele* (uživatelské jméno a heslo nebo certifikát) pro aplikaci.
- Spravovaná identita – identita v Azure Active Directory, kterou automaticky spravuje Azure. [Spravované identity](../active-directory/managed-identities-azure-resources/overview.md) obvykle používáte při vývoji cloudových aplikací pro správu přihlašovacích údajů pro ověřování ve službách Azure.

### <a name="role-definition"></a>Definice role

*Definice role* je kolekce oprávnění. Někdy se pouze nazývá *role*. Definice role obsahuje seznam operací, které je možné provést, například čtení, zápis a odstranění. Role můžou být vysoké úrovně, jako je vlastník nebo konkrétní, jako je třeba čtečka virtuálních počítačů.

![Definice role pro přiřazení role](./media/overview/rbac-role-definition.png)

Azure obsahuje několik [předdefinovaných rolí](built-in-roles.md) , které můžete použít. Následující seznam obsahuje čtyři základní předdefinované role. První tři platí pro všechny typy prostředků.

- [Owner (vlastník](built-in-roles.md#owner) ) – má úplný přístup ke všem prostředkům, včetně práva delegovat přístup ostatním uživatelům.
- [Přispěvatel](built-in-roles.md#contributor) – může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup jiným uživatelům.
- [Čtenář](built-in-roles.md#reader) – může zobrazit existující prostředky Azure.
- [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) – umožňuje spravovat přístup uživatelů k prostředkům Azure.

Zbývající z předdefinovaných rolí umožňují správu konkrétních prostředků Azure. Například role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) umožňuje uživateli vytvářet a spravovat virtuální počítače. Pokud předdefinované role nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [role pro prostředky Azure](custom-roles.md).

Azure obsahuje operace s daty, které umožňují udělit přístup k datům v rámci objektu. Pokud má uživatel například oprávnění číst data k účtu úložiště, pak může číst objekty blob nebo zprávy v rámci daného účtu úložiště. Další informace najdete v tématu [vysvětlení definic rolí pro prostředky Azure](role-definitions.md).

### <a name="scope"></a>Rozsah

*Obor* je sada prostředků, na které se vztahuje přístup. Když přiřadíte roli, můžete dále omezit povolené akce definováním oboru. To je užitečné, pokud chcete, aby někomu byl [Přispěvatel webu](built-in-roles.md#website-contributor), ale jenom pro jednu skupinu prostředků.

V Azure můžete zadat obor na více úrovních: [skupina pro správu](../governance/management-groups/overview.md), předplatné, skupina prostředků nebo prostředek. Obory jsou strukturované v relaci typu nadřazený-podřízený.

![Rozsah přiřazení role](./media/overview/rbac-scope.png)

Když udělíte přístup v nadřazeném oboru, budou tato oprávnění děděna do podřízených oborů. Příklad:

- Pokud přiřadíte roli [vlastníka](built-in-roles.md#owner) k uživateli v oboru skupiny pro správu, může tento uživatel spravovat vše ve všech předplatných ve skupině pro správu.
- Pokud přiřadíte roli [Čtenář](built-in-roles.md#reader) ke skupině v oboru předplatného, členové této skupiny mohou zobrazit všechny skupiny prostředků a prostředky v rámci předplatného.
- Pokud přiřadíte roli [Přispěvatel](built-in-roles.md#contributor) k aplikaci v oboru skupiny prostředků, může spravovat prostředky všech typů v této skupině prostředků, ale ne jiné skupiny prostředků v předplatném.

### <a name="role-assignments"></a>Přiřazení rolí

*Přiřazení role* je proces připojení definice role k uživateli, skupině, objektu služby nebo spravované identitě v konkrétním oboru pro účely udělení přístupu. Přístup se udělí vytvořením přiřazení role a přístup se odvolá odebráním přiřazení role.

V následujícím diagramu vidíte příklad přiřazení role. V tomto příkladu byla skupině marketingu přiřazena role [Přispěvatel](built-in-roles.md#contributor) pro skupinu prostředků Pharma-Sales. To znamená, že uživatelé ve skupině Marketing můžou vytvořit nebo spravovat libovolný prostředek Azure ve skupině prostředků Pharma-Sales. Uživatelé marketingu nemají přístup k prostředkům mimo skupinu prostředků Pharma-Sales, pokud nejsou součástí jiného přiřazení role.

![Přiřazení role pro řízení přístupu](./media/overview/rbac-overview.png)

Přiřazení rolí můžete vytvořit pomocí Azure Portal, Azure CLI, Azure PowerShell, sady Azure SDK nebo rozhraní REST API. V každé skupině pro správu můžete mít až **2000** přiřazení rolí v každém předplatném a v **500** přiřazení rolí. Chcete-li vytvořit a odebrat přiřazení rolí, je nutné mít oprávnění `Microsoft.Authorization/roleAssignments/*`. Toto oprávnění je uděleno prostřednictvím rolí [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) .

## <a name="multiple-role-assignments"></a>Přiřazení více rolí

Co se stane, když máte více překrývajících se přiřazení rolí? RBAC je aditivní model, takže vaše efektivní oprávnění jsou přidáním přiřazení rolí. Vezměte v úvahu následující příklad, kdy je uživateli udělena role Přispěvatel v oboru předplatného a role čtenář ve skupině prostředků. Přidání oprávnění přispěvatele a oprávnění čtenářů je efektivně rolí přispěvatele pro skupinu prostředků. Proto v takovém případě přiřazení role čtenáře nemá žádný vliv.

![Přiřazení více rolí](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>Odmítnout přiřazení

V minulosti byl pouze model s povoleným povolením bez odepření, ale nyní RBAC podporuje přiřazení odepřít omezenému způsobu. Podobně jako u přiřazení role *přiřazení zamítnutí* připojuje sadu akcí Odepřít pro uživatele, skupinu, instanční objekt nebo spravovanou identitu v konkrétním oboru pro účely odepření přístupu. Přiřazení role definuje sadu akcí, které jsou *povoleny*, zatímco přiřazení zamítnutí definuje sadu akcí, které nejsou *povoleny*. Jinými slovy, zamítnout přiřazení zablokují uživatelům, aby prováděli zadané akce, a to i v případě, že mu přiřazení role udělí přístup. Přiřazení odmítnutí mají přednost před přiřazením rolí. Další informace najdete v tématu [Vysvětlení přiřazení zamítnutí pro prostředky Azure](deny-assignments.md).

## <a name="how-rbac-determines-if-a-user-has-access-to-a-resource"></a>Jak RBAC určuje, jestli má uživatel přístup k prostředku

Níže jsou uvedené kroky vysoké úrovně, které RBAC používá k určení, jestli máte přístup k prostředku na rovině správy. To je užitečné pro pochopení, jestli se snažíte řešit problém s přístupem.

1. Uživatel (nebo instanční objekt) získá token pro Azure Resource Manager.

    Token zahrnuje členství uživatele ve skupinách (včetně členství v přenositelných skupinách).

1. Uživatel provede REST API volání Azure Resource Manager pomocí připojeného tokenu.

1. Azure Resource Manager načte všechna přiřazení rolí a zakáže přiřazení, která platí pro prostředek, na kterém se akce provádí.

1. Azure Resource Manager zužuje přiřazení rolí, která se vztahují k tomuto uživateli nebo skupině a určují, jaké role má uživatel pro tento prostředek.

1. Azure Resource Manager určuje, jestli je akce v volání rozhraní API zahrnutá v rolích, které má uživatel pro tento prostředek.

1. Pokud uživatel nemá roli s akcí v požadovaném oboru, přístup se neudělí. V opačném případě Azure Resource Manager ověří, zda se vztahuje přiřazení zamítnutí.

1. Pokud se použije přiřazení odepřít, přístup je zablokován. V opačném případě se udělí přístup.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: zobrazení přístupu uživatele k prostředkům Azure pomocí Azure Portal](check-access.md)
- [Správa přístupu k prostředkům Azure pomocí RBAC a Azure Portal](role-assignments-portal.md)
- [Pochopení různých rolí v Azure](rbac-and-directory-admin-roles.md)
- [Přijetí podnikového cloudu: Správa přístupu k prostředkům v Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
