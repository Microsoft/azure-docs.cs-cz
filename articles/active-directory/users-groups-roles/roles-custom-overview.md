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
ms.openlocfilehash: eabf29b10814d19e89c21f27ec66fce5355c9bfb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880689"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Vlastní role správců v Azure Active Directory (Preview)

Tento článek popisuje, jak pochopit nové vlastní role řízení přístupu na základě rolí (RBAC) a obory prostředků v Azure Active Directory (Azure AD). Vlastní role RBAC obcházejí základní oprávnění k předdefinovaným [rolím](directory-assign-admin-roles.md) , takže můžete vytvářet a organizovat vlastní role. Tento přístup vám v případě potřeby umožňuje udělit přístup podrobnějším způsobem než předdefinované role. Tato první verze vlastních rolí RBAC zahrnuje možnost vytvořit roli pro přiřazení oprávnění pro správu registrací aplikací. V průběhu času se přidají další oprávnění k prostředkům organizace, jako jsou podnikové aplikace, uživatelé a zařízení.  

Kromě toho vlastní role RBAC podporuje přiřazení na základě jednotlivých prostředků a navíc k efektivnějším přiřazením v rámci organizace. Tento přístup vám dává možnost udělit přístup ke správě některých prostředků (například registrace jedné aplikace) bez poskytnutí přístupu ke všem prostředkům (registrace všech aplikací).

Řízení přístupu na základě role Azure AD je funkce Public Preview služby Azure AD a je dostupná pro placený plán licencí Azure AD. Další informace o verzích Preview najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Principy řízení přístupu na základě role v Azure AD

Udělení oprávnění pomocí vlastních rolí RBAC je proces se dvěma kroky, který zahrnuje vytvoření vlastní definice role a jejich přiřazení pomocí přiřazení role. Vlastní definice role je kolekce oprávnění, která přidáte ze seznamu přednastavených. Tato oprávnění jsou stejná oprávnění, která se používají ve vestavěných rolích.  

Po vytvoření definice role ji můžete přiřadit někomu, že vytvoříte přiřazení role. Přiřazení role uděluje někomu oprávnění v definici role v konkrétním oboru. Tento dvoustupňový proces vám umožní vytvořit jednu definici role a přiřadit ji v různých oborech mnohokrát. Obor definuje sadu prostředků, ke kterým má člen role přístup. Nejběžnější je obor v rozsahu pro organizaci (v rámci organizace). Vlastní role může být přiřazena v oboru celé organizace, což znamená, že člen role má oprávnění role pro všechny prostředky v organizaci. Vlastní roli lze také přiřadit v oboru objektu. Příkladem oboru objektu může být jedna aplikace. Tímto způsobem je možné přiřadit stejnou roli Sally pro všechny aplikace v organizaci a pak Naveen jenom přes aplikaci společnosti Contoso pro sestavy výdajů.  

Azure AD RBAC funguje na konceptech podobných [řízení přístupu na základě role v Azure](../../role-based-access-control/overview.md). Rozdílem v tom, že Azure RBAC řídí přístup k prostředkům Azure, jako jsou virtuální počítače a weby, a řízení přístupu ke službám Azure AD RBAC řídí přístup k Azure AD. Oba systémy využívají koncept definic rolí a přiřazení rolí.

### <a name="role-assignments"></a>Přiřazení rolí

Přiřazení role je proces připojení definice role k uživateli v konkrétním oboru za účelem udělení přístupu. Přístup se uděluje vytvořením přiřazení role a odvolává se odebráním přiřazení role. Přiřazení role se skládá ze tří prvků:
- Uživatel
- Definice role
- Obor prostředku

[Přiřazení rolí můžete vytvořit](roles-create-custom.md) pomocí Azure Portal, Azure AD PowerShellu nebo Graph API. Můžete také [Zobrazit přiřazení vlastní role](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Následující diagram znázorňuje příklad přiřazení role. V tomto příkladu byla uživateli Novák přiřazena vlastní role správce registrace aplikace v oboru registrace aplikace Contoso widget Builder. Toto přiřazení uděluje pracovníkovi oprávnění role správce registrace aplikace jenom v této konkrétní registraci aplikace.

![Přiřazení role je způsob, jakým se vynutila oprávnění a které mají tři části.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Objekt zabezpečení

Objekt zabezpečení představuje uživatele, kterému se má přiřadit přístup k prostředkům služby Azure AD. *Uživatel* je osoba, která má profil uživatele v Azure Active Directory.

### <a name="role"></a>Role

Definice role neboli role je kolekce oprávnění. Definice role obsahuje seznam operací, které se dají provádět na prostředcích Azure AD, jako je vytváření, čtení, aktualizace a odstranění. Ve službě Azure AD existují dva typy rolí:

- Předdefinované role vytvořené Microsoftem, které se nedají změnit. Integrovaná role globálního správce má všechna oprávnění pro všechny prostředky Azure AD.
- Vlastní role vytvořené a spravované vaší organizací.

### <a name="scope"></a>Scope

Obor je omezení povolených akcí pro určitý prostředek služby Azure AD v rámci přiřazení role. Když přiřadíte roli, můžete zadat obor, který omezí přístup správce ke konkrétnímu prostředku. Pokud například chcete vývojářům udělit vlastní roli, ale jenom ke správě konkrétní registrace aplikace, můžete do přiřazení role zahrnout konkrétní registraci aplikace jako obor.

  > [!Note]
  > Vlastní role se dají přiřadit v oboru adresáře a v oboru prostředků. Ještě se nedají přiřadit v oboru administrativní jednotky.
  > Předdefinované role se dají přiřadit v oboru adresáře a v některých případech i v oboru jednotek pro správu. Nelze je ještě přiřadit v oboru objektů.

## <a name="required-license-plan"></a>Požadovaný licenční plán

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další postup

- Vytváření přiřazení vlastních rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](roles-create-custom.md)
- [Zobrazení přiřazení pro vlastní roli](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
