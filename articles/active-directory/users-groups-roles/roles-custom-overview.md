---
title: Náhled rolí správce Azure s přizpůsobitelnými oprávněními – Azure Active Directory | Microsoft Docs
description: Zobrazte si náhled vlastních rolí Azure AD pro delegování správy identit. Spravujte role Azure v Azure Portal, PowerShellu nebo Graph API.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 546abdae5d7c03bb0d4b49f9485fe06b521fdc1d
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722233"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Vlastní role správců v Azure Active Directory (Preview)

Tento článek popisuje, jak pochopit nové vlastní RBAC (řízení přístupu na základě rolí) a obory prostředků v Azure Active Directory (Azure AD). Vlastní role RBAC obcházejí základní oprávnění k předdefinovaným [rolím](directory-assign-admin-roles.md) , takže můžete vytvářet a organizovat vlastní role. Obory prostředků poskytují způsob, jak přiřadit vlastní roli pro správu některých prostředků (např. jedna aplikace) bez udělení přístupu všem prostředkům (všem aplikacím).

Udělení oprávnění pomocí vlastních rolí RBAC je proces se dvěma kroky. Nejprve vytvoříte vlastní definici role a přidáte do ní oprávnění ze seznamu přednastavení. Jedná se o stejná oprávnění, která se používají ve vestavěných rolích. Jakmile roli vytvoříte, přiřadíte ji někomu vytvořením přiřazení role. Tento dvoustupňový proces vám umožní vytvořit jednu roli a přiřadit ji v různých oborech mnohokrát. Vlastní role může být přiřazena v oboru adresáře nebo může být přiřazena v oboru objektu. Příkladem oboru objektu může být jedna aplikace. Tímto způsobem je možné přiřadit stejnou roli Sally přes všechny aplikace v adresáři a pak Naveen jenom přes aplikaci společnosti Contoso pro sestavy výdajů.

Tato první verze vlastních rolí RBAC zahrnuje možnost vytvořit roli pro přiřazení oprávnění pro správu registrací aplikací. V průběhu času se přidají další oprávnění k prostředkům organizace, jako jsou podnikové aplikace, uživatelé a zařízení.

Funkce ve verzi Preview:

- Aktualizace uživatelského rozhraní portálu pro vytváření a správu vlastních rolí a jejich přiřazení uživatelům v oboru v rámci organizace
- Modul PowerShellu ve verzi Preview s novými rutinami pro:
  - Vytváření a Správa vlastních rolí
  - Přiřaďte vlastní role k oboru registrace pro celé organizace nebo pro aplikaci.
  - Přiřazení předdefinovaných rolí oboru v rámci organizace (parita s rutinami GA)
  - Podpora Graph API Azure AD

Řízení přístupu na základě role Azure AD je funkce Public Preview služby Azure AD a je dostupná pro placený plán licencí Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Principy řízení přístupu na základě role v Azure AD

Řízení přístupu na základě role Azure AD umožňuje přiřadit role, které jsou přizpůsobené tak, aby povolovaly povolené akce jenom pro jeden typ prostředku Azure AD. Přístup založený na rolích Azure AD funguje v konceptech podobných řízení přístupu na základě role Azure ([Azure RBAC](../../role-based-access-control/overview.md) pro přístup k prostředkům Azure, ale řízení přístupu na základě rolí Azure AD je založené na Microsoft Graph a služba Azure RBAC je založená na Azure Resource Manager. Oba systémy však jejich funkce založí na přiřazení rolí.

### <a name="role-assignments"></a>Přiřazení rolí

Způsob řízení přístupu pomocí řízení přístupu na základě role Azure AD je vytvoření **přiřazení rolí**, které se používají k vymáhání oprávnění. Přiřazení role se skládá ze tří prvků:

- Objekt zabezpečení
- Definice role
- Obor prostředku

Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role. [Přiřazení rolí můžete vytvořit](roles-create-custom.md) pomocí Azure Portal, Azure AD PowerShellu a Graph API. Můžete samostatně [Zobrazit přiřazení pro vlastní roli](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu byla uživateli přiřazena role [správce aplikace](directory-assign-admin-roles.md#application-administrator) v oboru aplikace Salesforce. Chris nemá přístup ke správě žádné jiné aplikace, pokud nejsou součástí jiného přiřazení role.

![Přiřazení role je způsob, jakým se vynutila oprávnění a které mají tři části.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Objekt zabezpečení

Objekt zabezpečení představuje uživatele nebo instanční objekt, kterému má být přiřazen přístup k prostředkům služby Azure AD. *Uživatel* je osoba, která má profil uživatele v Azure Active Directory. *Instanční objekt* je identita zabezpečení, kterou aplikace nebo služby používají pro přístup ke konkrétním prostředkům služby Azure AD.

Objekt zabezpečení je podobný identitě uživatele v tom, že představuje uživatelské jméno a heslo nebo certifikát, ale u aplikace nebo služby místo uživatele.

### <a name="role"></a>Role

Definice role neboli role je kolekce oprávnění. Definice role obsahuje seznam operací, které se dají provádět na prostředcích Azure AD, jako je vytváření, čtení, aktualizace a odstranění. Ve službě Azure AD existují dva typy rolí:

- Předdefinované role vytvořené Microsoftem, které se nedají změnit. Integrovaná role globálního správce má všechna oprávnění pro všechny prostředky Azure AD.
- Vlastní role vytvořené a spravované vaší organizací.

### <a name="scope"></a>Scope

Obor je omezení povolených akcí na konkrétním prostředku Azure AD. Když přiřadíte roli, můžete přizpůsobit roli a omezit povolené akce správce definováním rozsahu akce. Pokud například vývojáři nepotřebují plně spravovat všechny aplikace, můžete použít vlastní role Azure AD, které jim umožní spravovat jenom registrace aplikací.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další postup

- Vytváření přiřazení vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
