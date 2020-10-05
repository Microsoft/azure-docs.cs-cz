---
title: Vlastní role správců v Azure Active Directory | Microsoft Docs
description: Naučte se pochopit vlastní role Azure AD v Azure Active Directory (Azure AD) s rolemi řízení přístupu na základě rolí a obory prostředků.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 09/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aac8713affd56d011e5e1f5e9326de501fb3ce67
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90975554"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Vlastní role správců v Azure Active Directory (Preview)

Tento článek popisuje, jak pochopit vlastní role Azure AD v Azure Active Directory (Azure AD) s rolemi řízení přístupu na základě rolí a obory prostředků. Vlastní role Azure AD surfují základní oprávnění [předdefinovaných rolí](directory-assign-admin-roles.md), abyste mohli vytvářet a organizovat vlastní role. Tento přístup vám umožní udělit přístup podrobnějším způsobem než předdefinované role, kdykoli je budete potřebovat. Tato první verze vlastních rolí Azure AD zahrnuje možnost vytvořit roli pro přiřazení oprávnění pro správu registrací aplikací. V průběhu času se přidají další oprávnění k prostředkům organizace, jako jsou podnikové aplikace, uživatelé a zařízení.  

Navíc vlastní role Azure AD podporují přiřazení na základě jednotlivých prostředků, kromě tradičních přiřazení v rámci organizace. Tento přístup vám dává možnost udělit přístup ke správě některých prostředků (například registrace jedné aplikace) bez poskytnutí přístupu ke všem prostředkům (registrace všech aplikací).

Řízení přístupu na základě role Azure AD je funkce Public Preview služby Azure AD a je dostupná pro placený plán licencí Azure AD. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="understand-azure-ad-role-based-access-control"></a>Principy řízení přístupu na základě role v Azure AD

Udělení oprávnění pomocí vlastních rolí Azure AD je dvoustupňový proces, který zahrnuje vytvoření vlastní definice role a její přiřazení pomocí přiřazení role. Vlastní definice role je kolekce oprávnění, která přidáte ze seznamu přednastavených. Tato oprávnění jsou stejná oprávnění, která se používají ve vestavěných rolích.  

Po vytvoření definice role ji můžete přiřadit uživateli vytvořením přiřazení role. Přiřazení role uděluje uživateli oprávnění v definici role v zadaném oboru. Tento dvoustupňový proces vám umožní vytvořit definici jedné role a přiřadit ji v různých oborech mnohokrát. Obor definuje sadu prostředků Azure AD, ke kterým má člen role přístup. Nejběžnější je obor v rozsahu pro organizaci (v rámci organizace). Vlastní role může být přiřazena v oboru celé organizace, což znamená, že člen role má oprávnění role pro všechny prostředky v organizaci. Vlastní roli lze také přiřadit v oboru objektu. Příkladem oboru objektu může být jedna aplikace. Stejnou roli je možné přiřadit jednomu uživateli přes všechny aplikace v organizaci a pak jinému uživateli s rozsahem jenom aplikace se sestavami výdajů společnosti Contoso.  

Integrované a vlastní role Azure AD fungují v konceptech podobně jako [řízení přístupu na základě role v Azure (RBAC)](../../role-based-access-control/overview.md). [Rozdíl mezi těmito dvěma systémy řízení přístupu na základě rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md) spočívá v tom, že Azure RBAC řídí přístup k prostředkům Azure, jako jsou virtuální počítače nebo úložiště, pomocí správy prostředků Azure a vlastní role Azure AD řídí přístup k prostředkům Azure ad pomocí Graph API. Oba systémy využívají koncept definic rolí a přiřazení rolí.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Jak Azure AD určuje, jestli má uživatel přístup k prostředku

Níže jsou uvedené kroky vysoké úrovně, které Azure AD používá k určení, jestli máte přístup k prostředku správy. Tyto informace slouží k řešení problémů s přístupem.

1. Uživatel (nebo instanční objekt) získá token pro Microsoft Graph koncový bod služby Azure AD Graph.

1. Uživatel vytvoří volání rozhraní API Azure Active Directory (Azure AD) přes Microsoft Graph nebo Azure AD Graph pomocí vydaného tokenu.

1. V závislosti na okolnostech využívá Azure AD jednu z následujících akcí:

    - Vyhodnotí členství uživatelů v rolích na základě [deklarace identity WIDS](../develop/access-tokens.md) v přístupovém tokenu uživatele.
    - Načte všechna přiřazení rolí, která platí pro uživatele, a to buď přímo, nebo prostřednictvím členství ve skupině, na prostředek, na kterém se akce provádí.

1. Azure AD určuje, jestli je akce v volání rozhraní API zahrnutá v rolích, které má uživatel pro tento prostředek.
1. Pokud uživatel nemá roli s akcí v požadovaném oboru, přístup se neudělí. Pokud ne, přístup je udělen.

### <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role je objekt, který připojuje definici role k uživateli v konkrétním oboru za účelem udělení přístupu k prostředkům Azure AD. Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role. V jádru se přiřazení role skládá ze tří prvků:

- Uživatel (osoba, která má profil uživatele v Azure Active Directory)
- Definice role
- Obor prostředku

[Přiřazení rolí můžete vytvořit](roles-create-custom.md) pomocí Azure Portal, Azure AD PowerShellu nebo Graph API. Můžete také [Zobrazit přiřazení vlastní role](roles-view-assignments.md#view-the-assignments-of-a-role).

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu byla uživateli Novák přiřazena vlastní role správce registrace aplikace v oboru registrace aplikace Contoso widget Builder. Přiřazení uděluje pracovníkovi oprávnění role správce registrace aplikace jenom pro tuto konkrétní registraci aplikace.

![Přiřazení role je způsob, jakým se vynutila oprávnění a které mají tři části.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Objekt zabezpečení

Objekt zabezpečení představuje uživatele, kterému se má přiřadit přístup k prostředkům služby Azure AD. *Uživatel* je osoba, která má profil uživatele v Azure Active Directory.

### <a name="role"></a>Role

Definice role neboli role je kolekce oprávnění. Definice role obsahuje seznam operací, které se dají provádět na prostředcích Azure AD, jako je vytváření, čtení, aktualizace a odstranění. Ve službě Azure AD existují dva typy rolí:

- Předdefinované role vytvořené Microsoftem, které se nedají změnit.
- Vlastní role vytvořené a spravované vaší organizací.

### <a name="scope"></a>Obor

Obor je omezení povolených akcí pro určitý prostředek služby Azure AD v rámci přiřazení role. Když přiřadíte roli, můžete zadat obor, který omezí přístup správce ke konkrétnímu prostředku. Pokud například chcete vývojářům udělit vlastní roli, ale jenom ke správě konkrétní registrace aplikace, můžete do přiřazení role zahrnout konkrétní registraci aplikace jako obor.

  > [!Note]
  > Vlastní role se dají přiřadit v oboru adresáře a v oboru prostředků. Ještě se nedají přiřadit v oboru administrativní jednotky.
  > Předdefinované role lze přiřadit v oboru adresáře a v některých případech i v oboru administrativní jednotky. Ještě se nedají přiřadit k oboru prostředků Azure AD.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Vytváření přiřazení vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md#view-assignments-of-single-application-scope)