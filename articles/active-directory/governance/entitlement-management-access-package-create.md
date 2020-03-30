---
title: Vytvoření nového balíčku přístupu ve správě nároků – Azure AD
description: Zjistěte, jak vytvořit nový přístupový balíček prostředků, které chcete sdílet ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262006"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Vytvoření nového balíčku přístupu ve správě nároků Azure AD

Přístupový balíček umožňuje provést jednorázové nastavení prostředků a zásad, které automaticky spravuje přístup po dobu životnosti balíčku přístupu. Tento článek popisuje, jak vytvořit nový balíček přístupu.

## <a name="overview"></a>Přehled

Všechny přístupové balíčky musí být vloženy do kontejneru nazývaného katalog. Katalog definuje, jaké prostředky můžete přidat do balíčku přístupu. Pokud nezadáte katalog, váš přístupový balíček bude vložen do obecného katalogu. V současné době nelze přesunout existující balíček přístupu do jiného katalogu.

Pokud jste správce balíčků přístupu, nelze přidat prostředky, které vlastníte do katalogu. Jste omezeni na použití prostředků dostupných v katalogu. Pokud potřebujete přidat prostředky do katalogu, můžete požádat vlastníka katalogu.

Všechny přístupové balíčky musí mít alespoň jednu zásadu. Zásady určují, kdo může požádat o přístupový balíček a také nastavení schválení a životního cyklu. Při vytváření nového balíčku přístupu můžete vytvořit počáteční zásady pro uživatele ve vašem adresáři, pro uživatele, kteří nejsou ve vašem adresáři, pouze pro přiřazení přímých správců, nebo můžete zvolit, zda chcete zásadu vytvořit později.

![Vytvoření přístupového balíčku](./media/entitlement-management-access-package-create/access-package-create.png)

Zde jsou kroky vysoké úrovně k vytvoření nového přístupového balíčku.

1. V identity governance, spusťte proces k vytvoření nového balíčku přístupu.

1. Vyberte katalog, ve kterém chcete vytvořit přístupový balíček.

1. Přidejte prostředky z katalogu do balíčku přístupu.

1. Přiřazení rolí prostředků pro každý prostředek

1. Zadejte uživatele, kteří mohou požádat o přístup.

1. Zadejte všechna nastavení schválení.

1. Určete nastavení životního cyklu.

## <a name="start-new-access-package"></a>Spustit nový přístupový balíček

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Klikněte na **Azure Active Directory** a potom klikněte na **Zásadsprávné řízení identit .**

1. V levé nabídce klepněte na **položku Přístup k balíčkům**.

1. Klepněte na **položku Nový přístupový balíček**.
   
    ![Správa nároků na webu Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Základy

Na kartě **Základy** přiřazujete přístupový balíček názvu a určíte, ve kterém katalogu se má balíček pro přístup vytvořit.

1. Zadejte zobrazovaný název a popis přístupového balíčku. Uživatelé uvidí tyto informace při odeslání žádosti o přístup balíček.

1. V rozevíracím seznamu **Katalog** vyberte katalog, ve kterém chcete vytvořit přístupový balíček. Můžete mít například vlastníka katalogu, který spravuje všechny marketingové zdroje, které lze požadovat. V takovém případě můžete vybrat marketingový katalog.

    Zobrazí se pouze katalogy, ve kterých máte oprávnění k vytváření přístupových balíčků. Chcete-li vytvořit balíček přístupu v existujícím katalogu, musíte být globální správce nebo správce uživatele, nebo musíte být vlastníkem katalogu nebo správce mačkám v tomto katalogu.

    ![Přístupový balíček - základy](./media/entitlement-management-access-package-create/basics.png)

    Pokud jste globální správce, správce uživatele nebo tvůrce katalogu a chcete vytvořit balíček přístupu v novém katalogu, který není uveden, klepněte na tlačítko **Vytvořit nový katalog**. Zadejte název a popis katalogu a klepněte na tlačítko **Vytvořit**.

    Přístupový balíček, který vytváříte, a všechny prostředky, které jsou v něm zahrnuty, budou přidány do nového katalogu. Později můžete také přidat další vlastníky katalogu.

1. Klikněte na **Další**.

## <a name="resource-roles"></a>Role prostředků

Na kartě **Role prostředků** vyberete prostředky, které chcete zahrnout do balíčku přístupu. Uživatelé, kteří požadují a obdrží přístupový balíček, obdrží všechny role prostředků v balíčku přístupu.

1. Klikněte na typ prostředku, který chcete přidat (**Skupiny a týmy**, **Aplikace**nebo **Weby služby SharePoint**).

1. V panelu Vybrat, které se zobrazí, vyberte ze seznamu jeden nebo více zdrojů.

    ![Balíček accessu – role prostředků](./media/entitlement-management-access-package-create/resource-roles.png)

    Pokud vytváříte přístupový balíček v obecném katalogu nebo v novém katalogu, budete moci vybrat libovolný prostředek z adresáře, který vlastníte. Musíte být alespoň globálním správcem, správcem uživatele nebo tvůrcem katalogu.

    Pokud vytváříte balíček přístupu v existujícím katalogu, můžete vybrat libovolný prostředek, který je již v katalogu, aniž by byl vlastní.

    Pokud jste globální správce, správce uživatele nebo vlastník katalogu, máte další možnost výběru prostředků, které vlastníte, které ještě nejsou v katalogu. Pokud vyberete prostředky, které nejsou aktuálně ve vybraném katalogu, budou tyto prostředky přidány také do katalogu pro ostatní správce katalogu, aby vytvořili přístupové balíčky. Pokud chcete vybrat pouze zdroje, které jsou aktuálně ve vybraném katalogu, zaškrtněte políčko **Pouze zobrazit** v horní části podokna Výběr.

1. Po výběru zdrojů vyberte v seznamu **Role** roli, kterou mají být uživatelé přiřazeni k prostředku.

    ![Balíček accessu – výběr role prostředku](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klikněte na **Další**.

## <a name="requests"></a>Žádosti

Na kartě **Požadavky** vytvoříte první zásadu, která určí, kdo může požádat o přístupový balíček a také nastavení schválení. Později můžete vytvořit další zásady požadavků, které umožní dalším skupinám uživatelů požádat o přístupový balíček s vlastním nastavením schválení.

![Přístupový balíček – karta Požadavky](./media/entitlement-management-access-package-create/requests.png)

V závislosti na tom, kdo chcete mít možnost požádat o tento přístupový balíček, proveďte kroky v jedné z následujících částí.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Recenze + vytvořit

Na kartě **Revize + vytvořit** můžete zkontrolovat nastavení a zkontrolovat případné chyby ověření.

1. Kontrola nastavení přístupového balíčku

    ![Přístupový balíček – zásady – povolit nastavení zásad](./media/entitlement-management-access-package-create/review-create.png)

1. Chcete-li vytvořit přístupový balíček, klepněte na **tlačítko Vytvořit.**

    Nový přístupový balíček se zobrazí v seznamu přístupových balíčků.

## <a name="creating-an-access-package-programmatically"></a>Programové vytvoření přístupového balíčku

Můžete také vytvořit balíček přístupu pomocí aplikace Microsoft Graph.  Uživatel v příslušné roli s aplikací, která `EntitlementManagement.ReadWrite.All` má delegované oprávnění, může volat rozhraní API

1. [Seznam accessPackageResources v katalogu](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) a [vytvořte accessPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) pro všechny prostředky, které ještě nejsou v katalogu.
1. [Seznam accessPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) každého accessPackageResource v accessPackageCatalog. Tento seznam rolí pak bude použit k výběru role při následném vytvoření accessPackageResourceRoleScope.
1. [Vytvořte accessPackage](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta).
1. [Vytvořte accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Vytvořte accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) pro každou roli prostředku potřebnou v balíčku přístupu.

## <a name="next-steps"></a>Další kroky

- [Odkaz na sdílení pro vyžádání přístupového balíčku](entitlement-management-access-package-settings.md)
- [Změna rolí prostředků pro přístupový balíček](entitlement-management-access-package-resources.md)
- [Přímé přiřazení uživatele k přístupovému balíčku](entitlement-management-access-package-assignments.md)
