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
ms.openlocfilehash: cf6c0c68d13cf179f0635085296fe9953a45a255
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707692"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Vlastní role správců v Azure Active Directory (Preview)

Tento článek popisuje, jak pochopit nové vlastní role v Azure Active Directory (Azure AD). Azure AD umožňuje přiřadit oprávnění pro správu omezená na přizpůsobený obor prostředků služby Azure AD. Vlastní role, jako jsou ty, jsou součástí verze Preview modelu řízení přístupu založeného na rolích Azure AD. [Předdefinované role](directory-assign-admin-roles.md) poskytují adekvátní členitost v mnoha situacích, ale pokud chcete získat přesnější oprávnění, můžete vytvořit vlastní role a snížit tak rozsah řízení role na úroveň jednoho prostředku Azure AD.

Verze Preview řízení přístupu na základě role Azure AD zveřejňuje dva obory: rozsah celé organizace (povolení povolených akcí pro všechny prostředky Azure AD v organizaci) a vlastní obor s oprávněními omezenými na registrace aplikací. . V této vlastní roli můžete upravovat oprávnění k registraci aplikace. V průběhu času se přidají další oprávnění k prostředkům organizace, jako jsou podnikové aplikace, uživatelé a zařízení.

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