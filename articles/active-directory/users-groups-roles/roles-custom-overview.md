---
title: Vlastní role správce ve službě Azure Active Directory | Dokumenty společnosti Microsoft
description: Náhled vlastních rolí Azure AD pro delegování správy identit. Spravujte role Azure na webu Azure Portal, PowerShellu nebo rozhraní GRAPH API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085106"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Vlastní role správce ve službě Azure Active Directory (preview)

Tento článek popisuje, jak porozumět vlastním rolím Azure AD ve službě Azure Active Directory (Azure AD) s řízením přístupu na základě rolí a obory prostředků. Vlastní role Azure AD povrch základní oprávnění [předdefinované role](directory-assign-admin-roles.md), takže můžete vytvořit a uspořádat vlastní role. Tento přístup umožňuje udělit přístup podrobnějším způsobem než předdefinované role, kdykoli jsou potřeba. Tato první verze vlastních rolí Azure AD zahrnuje možnost vytvořit roli pro přiřazení oprávnění pro správu registrací aplikací. V průběhu času budou přidána další oprávnění pro organizační prostředky, jako jsou podnikové aplikace, uživatelé a zařízení.  

Kromě toho vlastní role Azure AD podporují přiřazení na základě prostředků, kromě tradiční přiřazení celé organizace. Tento přístup umožňuje udělit přístup ke správě některých prostředků (například registrace jedné aplikace), aniž byste měli přístup ke všem prostředkům (všechny registrace aplikací).

Řízení přístupu na základě rolí Azure AD je veřejná funkce náhledu Azure AD a je dostupná s placeným licenčním plánem Azure AD. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Principy řízení přístupu na základě rolí Azure AD

Udělení oprávnění pomocí vlastních rolí Azure AD je dvoustupňový proces, který zahrnuje vytvoření vlastní definice role a její přiřazení pomocí přiřazení role. Vlastní definice role je kolekce oprávnění, která přidáte ze seznamu přednastavených. Tato oprávnění jsou stejná oprávnění používaná v předdefinovaných rolích.  

Po vytvoření definice role ji můžete přiřadit uživateli vytvořením přiřazení role. Přiřazení role uděluje uživateli oprávnění v definici role v zadaném oboru. Tento dvoustupňový proces umožňuje vytvořit jednu definici role a přiřadit ji mnohokrát v různých oborech. Obor definuje sadu prostředků Azure AD, ke kterýmá člen role má přístup. Nejběžnější obor je celoorganizační (org-wide) obor. Vlastní roli lze přiřadit v celém organizačním rozsahu, což znamená, že člen role má oprávnění role nad všemi prostředky v organizaci. Vlastní roli lze také přiřadit v oboru objektu. Příkladem oboru objektu by byla jedna aplikace. Stejnou roli lze přiřadit jednomu uživateli ve všech aplikacích v organizaci a pak jinému uživateli s rozsahem pouze aplikace Přehledy výdajů společnosti Contoso.  

Integrované a vlastní role Azure AD fungují na konceptech podobných [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). [Rozdíl mezi těmito dvěma systémy řízení přístupu na základě rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md) spočívá v tom, že Azure RBAC řídí přístup k prostředkům Azure, jako jsou virtuální počítače nebo úložiště pomocí Azure Resource Management, a vlastní role Azure AD řídí přístup k prostředkům Azure AD pomocí rozhraní Graph API. Oba systémy využívají koncept definic rolí a přiřazení rolí.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Jak Azure AD určuje, pokud má uživatel přístup k prostředku

Následují kroky vysoké úrovně, které Azure AD používá k určení, pokud máte přístup k prostředku pro správu. Tyto informace slouží k řešení problémů s přístupem.

1. Uživatel (nebo instanční objekt) získá token do koncového bodu Microsoft Graph nebo Azure AD Graph.

1. Uživatel provede volání rozhraní API do služby Azure Active Directory (Azure AD) prostřednictvím Microsoft Graphu nebo Azure AD Graphu pomocí vydaného tokenu.

1. V závislosti na okolnostech Azure AD provede jednu z následujících akcí:

    - Vyhodnotí členství v rolích uživatele na základě [deklarace wids](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) v přístupovém tokenu uživatele.
    - Načte všechna přiřazení rolí, která platí pro uživatele, a to buď přímo, nebo prostřednictvím členství ve skupině, k prostředku, na kterém je akce provedena.

1. Azure AD určuje, pokud akce ve volání rozhraní API je zahrnuta v rolích, které má uživatel pro tento prostředek.
1. Pokud uživatel nemá roli s akcí v požadovaném oboru, přístup není udělen. Pokud ne, přístup je udělen.

### <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role je objekt, který připojuje definici role k uživateli v určitém oboru udělit přístup k prostředkům Azure AD. Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role. V jeho jádru se přiřazení role skládá ze tří prvků:

- Uživatel (osoba, která má uživatelský profil ve službě Azure Active Directory)
- Definice role
- Obor prostředků

Přiřazení [rolí](roles-create-custom.md) můžete vytvořit pomocí portálu Azure Portal, Azure AD PowerShell nebo Graph API. Můžete také [zobrazit přiřazení pro vlastní roli](roles-view-assignments.md#view-the-assignments-of-a-role).

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu chris Green byl přiřazen správce registrace aplikace vlastní roli v oboru Contoso Widget Builder registrace aplikace. Přiřazení uděluje Chris oprávnění role správce registrace aplikace pouze pro tuto konkrétní registraci aplikace.

![Přiřazení role je způsob vynucení oprávnění a má tři části](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Objekt zabezpečení

Objekt zabezpečení představuje uživatele, který má být přiřazen přístup k prostředkům Azure AD. *Uživatel* je osoba, která má uživatelský profil ve službě Azure Active Directory.

### <a name="role"></a>Role

Definice role nebo role je kolekce oprávnění. Definice role uvádí operace, které lze provádět na prostředky Azure AD, jako je například vytvořit, číst, aktualizovat a odstranit. Ve službě Azure AD existují dva typy rolí:

- Předdefinované role vytvořené společností Microsoft, které nelze změnit.
- Vlastní role vytvořené a spravované vaší organizací.

### <a name="scope"></a>Rozsah

Obor je omezení povolených akcí na konkrétní prostředek Azure AD jako součást přiřazení role. Při přiřazení role můžete určit obor, který omezuje přístup správce k určitému prostředku. Například pokud chcete udělit vývojáři vlastní roli, ale pouze pro správu konkrétní registrace aplikace, můžete zahrnout konkrétní registraci aplikace jako obor v přiřazení role.

  > [!Note]
  > Vlastní role lze přiřadit v oboru adresáře a v rozsahu oboru prostředků. Dosud nemohou být přiděleny v rozsahu správní jednotky.
  > Předdefinované role lze přiřadit v oboru adresáře a v některých případech v oboru správní jednotky. Ještě je nelze přiřadit v oboru prostředků Azure AD.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- Vytváření přiřazení vlastních rolí pomocí [portálu Azure Portal, Azure AD PowerShell a rozhraní GRAPH API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md#view-assignments-of-single-application-scope)
