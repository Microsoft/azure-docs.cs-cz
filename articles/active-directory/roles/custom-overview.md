---
title: Přehled Azure Active Directory řízení přístupu na základě role (RBAC)
description: Naučte se pochopit části přiřazení rolí a omezeného rozsahu v Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fad2c683890776908afbfbf15ee91d46d564783
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466758"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Přehled řízení přístupu na základě role v Azure Active Directory

Tento článek popisuje, jak porozumět řízení přístupu na základě role Azure Active Directory (Azure AD). Role Azure AD umožňují vašim správcům udělit podrobnější oprávnění, implementovat se zásadou nejnižší úrovně oprávnění. Integrované a vlastní role Azure AD fungují v konceptech podobných těm, které najdete v [systému řízení přístupu na základě role pro prostředky Azure](../../role-based-access-control/overview.md) (role Azure). [Rozdíl mezi těmito dvěma systémy řízení přístupu na základě rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md) :

- Role Azure AD řídí přístup k prostředkům Azure AD, jako jsou uživatelé, skupiny a aplikace, pomocí Graph API
- Role Azure řídí přístup k prostředkům Azure, jako jsou virtuální počítače nebo úložiště, pomocí správy prostředků Azure.

Oba systémy obsahují podobně použité definice rolí a přiřazení rolí. Oprávnění role Azure AD se ale nedají použít ve vlastních rolích Azure ani naopak.

## <a name="understand-azure-ad-role-based-access-control"></a>Principy řízení přístupu na základě role v Azure AD
Azure AD podporuje 2 typy definic rolí – 
* [Předdefinované role](./permissions-reference.md)
* [Vlastní role](./custom-create.md)

Předdefinované role jsou mimo box s pevnou sadou oprávnění. Tyto definice rolí nelze upravovat. Existuje mnoho [předdefinovaných rolí](./permissions-reference.md) , které Azure AD podporuje, a seznam roste. Azure AD podporuje také [vlastní role](./custom-create.md), aby bylo možné zaokrouhlit okraje a splnit vaše sofistikované požadavky. Udělení oprávnění pomocí vlastních rolí Azure AD je dvoustupňový proces, který zahrnuje vytvoření vlastní definice role a její přiřazení pomocí přiřazení role. Vlastní definice role je kolekce oprávnění, která přidáte ze seznamu přednastavených. Tato oprávnění jsou stejná oprávnění, která se používají ve vestavěných rolích.  

Po vytvoření vlastní definice role (nebo pomocí předdefinované role) ji můžete přiřadit k uživateli vytvořením přiřazení role. Přiřazení role uděluje uživateli oprávnění v definici role v zadaném oboru. Tento dvoustupňový proces vám umožní vytvořit definici jedné role a přiřadit ji v různých oborech mnohokrát. Obor definuje sadu prostředků Azure AD, ke kterým má člen role přístup. Nejběžnější je obor v rozsahu pro organizaci (v rámci organizace). Vlastní role může být přiřazena v oboru celé organizace, což znamená, že člen role má oprávnění role pro všechny prostředky v organizaci. Vlastní roli lze také přiřadit v oboru objektu. Příkladem oboru objektu může být jedna aplikace. Stejnou roli je možné přiřadit jednomu uživateli přes všechny aplikace v organizaci a pak jinému uživateli s rozsahem jenom aplikace se sestavami výdajů společnosti Contoso.  

Integrované a vlastní role Azure AD fungují v konceptech podobně jako [řízení přístupu na základě role v Azure (RBAC)](../develop/access-tokens.md#payload-claims). [Rozdíl mezi těmito dvěma systémy řízení přístupu na základě rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md) spočívá v tom, že Azure RBAC řídí přístup k prostředkům Azure, jako jsou virtuální počítače nebo úložiště, pomocí správy prostředků Azure a vlastní role Azure AD řídí přístup k prostředkům Azure ad pomocí Graph API. Oba systémy využívají koncept definic rolí a přiřazení rolí. Oprávnění služby Azure AD RBAC nelze zahrnout do rolí Azure ani naopak.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Jak Azure AD určuje, jestli má uživatel přístup k prostředku

Níže jsou uvedené kroky vysoké úrovně, které Azure AD používá k určení, jestli máte přístup k prostředku správy. Tyto informace slouží k řešení problémů s přístupem.

1. Uživatel (nebo instanční objekt) získá token pro Microsoft Graph koncový bod služby Azure AD Graph.
1. Uživatel vytvoří volání rozhraní API Azure Active Directory (Azure AD) přes Microsoft Graph nebo Azure AD Graph pomocí vydaného tokenu.
1. V závislosti na okolnostech využívá Azure AD jednu z následujících akcí:
   - Vyhodnotí členství uživatelů v rolích na základě [deklarace identity WIDS](../../active-directory-b2c/access-tokens.md) v přístupovém tokenu uživatele.
   - Načte všechna přiřazení rolí, která platí pro uživatele, a to buď přímo, nebo prostřednictvím členství ve skupině, na prostředek, na kterém se akce provádí.
1. Azure AD určuje, jestli je akce v volání rozhraní API zahrnutá v rolích, které má uživatel pro tento prostředek.
1. Pokud uživatel nemá roli s akcí v požadovaném oboru, přístup se neudělí. Pokud ne, přístup je udělen.

## <a name="role-assignment"></a>Přiřazení role

Přiřazení role je prostředek služby Azure AD, který připojuje *definici role* k *uživateli* v konkrétním *oboru* za účelem udělení přístupu k prostředkům služby Azure AD. Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role. V jádru se přiřazení role skládá ze tří prvků:

- Uživatel služby Azure AD
- Definice role
- Obor prostředku

[Přiřazení rolí můžete vytvořit](custom-create.md) pomocí Azure Portal, Azure AD PowerShellu nebo Graph API. Můžete také [Zobrazit seznam přiřazení rolí](view-assignments.md).

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu byla uživateli Novák přiřazena vlastní role správce registrace aplikace v oboru registrace aplikace Contoso widget Builder. Přiřazení uděluje pracovníkovi oprávnění role správce registrace aplikace jenom pro tuto konkrétní registraci aplikace.

![Přiřazení role je způsob, jakým se vynutila oprávnění a které mají tři části.](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Objekt zabezpečení

Objekt zabezpečení představuje uživatele, kterému se má přiřadit přístup k prostředkům služby Azure AD. Uživatel je osoba, která má profil uživatele v Azure Active Directory.

### <a name="role"></a>Role

Definice role neboli role je kolekce oprávnění. Definice role obsahuje seznam operací, které se dají provádět na prostředcích Azure AD, jako je vytváření, čtení, aktualizace a odstranění. Ve službě Azure AD existují dva typy rolí:

- Předdefinované role vytvořené Microsoftem, které se nedají změnit.
- Vlastní role vytvořené a spravované vaší organizací.

### <a name="scope"></a>Obor

Obor je omezení povolených akcí pro určitý prostředek služby Azure AD v rámci přiřazení role. Když přiřadíte roli, můžete zadat obor, který omezí přístup správce ke konkrétnímu prostředku. Pokud například chcete vývojářům udělit vlastní roli, ale jenom ke správě konkrétní registrace aplikace, můžete do přiřazení role zahrnout konkrétní registraci aplikace jako obor.

## <a name="required-license-plan"></a>Požadovaný licenční plán

Používání předdefinovaných rolí ve službě Azure AD je bezplatné, zatímco vlastní role vyžadují licenci Azure AD Premium P1. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Další kroky

- [Seznámení s rolemi Azure AD](concept-understand-roles.md)
- Vytváření přiřazení vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](custom-create.md)
- [Zobrazení seznamu přiřazení rolí](view-assignments.md)