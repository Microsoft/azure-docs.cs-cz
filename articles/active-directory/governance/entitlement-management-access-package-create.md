---
title: Vytvoření nového přístupového balíčku v rámci správy nároků – Azure AD
description: Naučte se, jak vytvořit nový balíček pro přístup k prostředkům, které chcete sdílet, v Azure Active Directory správu nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3df08272b352ee789c9879b1118105c435cffbd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011081"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Vytvoření nového balíčku pro přístup ve správě nároků Azure AD

Balíček pro přístup vám umožní jednorázovou instalaci prostředků a zásad, které automaticky spravují přístup po dobu života balíčku přístupu. Tento článek popisuje, jak vytvořit nový balíček pro přístup.

## <a name="overview"></a>Přehled

Všechny balíčky přístupu musí být vloženy do kontejneru s názvem Catalog. Katalog definuje prostředky, které můžete přidat do balíčku pro přístup. Pokud nezadáte katalog, balíček pro přístup se vloží do katalogu pro obecné. V současné době nemůžete přesunout existující balíček přístupu do jiného katalogu.

Pokud jste správce balíčků pro přístup, nemůžete přidat prostředky, které vlastníte do katalogu. Budete omezeni na používání prostředků dostupných v katalogu. Pokud potřebujete přidat prostředky do katalogu, můžete požádat vlastníka katalogu.

Všechny balíčky pro přístup musí mít aspoň jednu zásadu. Zásady určují, kdo může žádat o přístup k balíčku a také nastavení schválení a životního cyklu. Při vytváření nového přístupového balíčku můžete vytvořit počáteční zásady pro uživatele v adresáři, pro uživatele, kteří nejsou ve vašem adresáři, jenom pro přímé přiřazení správců, nebo můžete zvolit vytvoření zásady později.

![Vytvoření přístupového balíčku](./media/entitlement-management-access-package-create/access-package-create.png)

Tady je postup vysoké úrovně pro vytvoření nového přístupového balíčku.

1. V části zásady správného řízení identity spusťte proces vytváření nového přístupového balíčku.

1. Vyberte katalog, ve kterém chcete vytvořit balíček pro přístup.

1. Přidejte prostředky z katalogu do balíčku pro přístup.

1. Přiřaďte role prostředků pro každý prostředek.

1. Zadejte uživatele, kteří můžou požádat o přístup.

1. Zadejte nastavení schválení.

1. Zadejte nastavení životního cyklu.

## <a name="start-new-access-package"></a>Spustit nový balíček pro přístup

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Klikněte na **Azure Active Directory** a pak na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na možnost **přístup k balíčkům**.

1. Klikněte na **nový balíček pro přístup**.
   
    ![Správa nároků v Azure Portal](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Základy

Na kartě **základy** udělíte přístupovému balíčku název a určíte, ke kterému katalogu se má balíček pro přístup vytvořit.

1. Zadejte zobrazovaný název a Popis balíčku pro přístup. Uživatelům se zobrazí tyto informace, když odešlou žádost o přístup k balíčku.

1. V rozevíracím seznamu **katalog** vyberte katalog, ve kterém chcete vytvořit balíček pro přístup. Můžete mít například vlastníka katalogu, který spravuje všechny prostředky marketingu, které mohou být požadovány. V takovém případě můžete vybrat marketingový katalog.

    Zobrazí se pouze katalogy, které máte oprávnění k vytváření balíčků přístupu v nástroji. Pokud chcete vytvořit balíček přístupu v existujícím katalogu, musíte být globálním správcem nebo správcem uživatelů, nebo musíte být vlastníkem katalogu nebo správce balíčků přístupu v tomto katalogu.

    ![Přístup k základům balíčku](./media/entitlement-management-access-package-create/basics.png)

    Pokud jste globální správce, správce uživatele nebo Tvůrce katalogu a chcete vytvořit balíček přístupu v novém katalogu, který není v seznamu, klikněte na **vytvořit nový katalog**. Zadejte název a popis katalogu a pak klikněte na **vytvořit**.

    Balíček přístupu, který vytváříte, a všechny prostředky, které jsou v něm obsažené, se přidají do nového katalogu. Později můžete také přidat další vlastníky katalogu.

1. Klikněte na **Next** (Další).

## <a name="resource-roles"></a>Role prostředků

Na kartě **role prostředků** vyberte prostředky, které se mají zahrnout do balíčku pro přístup. Uživatelé, kteří požadují a obdrží balíček pro přístup, obdrží všechny role prostředků v balíčku pro přístup.

1. Klikněte na typ prostředku, který chcete přidat (**skupiny a týmy**, **aplikace** nebo **weby SharePointu**).

1. V zobrazeném podokně vyberte jeden nebo více prostředků ze seznamu.

    ![Přístup k prostředkům balíčku – role](./media/entitlement-management-access-package-create/resource-roles.png)

    Pokud vytváříte balíček přístupu v katalogu obecné nebo v novém katalogu, budete moci vybrat libovolný prostředek z adresáře, který vlastníte. Musíte mít aspoň globálního správce, Správce uživatelů nebo Tvůrce katalogu.

    Pokud vytváříte balíček přístupu v existujícím katalogu, můžete vybrat libovolný prostředek, který je již v katalogu, aniž by mu byl vlastník.

    Pokud jste globální správce, správce uživatele nebo vlastníka katalogu, máte další možnost výběru nevlastních prostředků, které ještě nejsou v katalogu. Pokud vyberete prostředky, které nejsou aktuálně ve vybraném katalogu, tyto prostředky budou také přidány do katalogu pro jiné Správce katalogu, aby mohli vytvářet balíčky přístupu pomocí nástroje. Chcete-li zobrazit všechny prostředky, které lze přidat do katalogu, zaškrtněte políčko **Zobrazit vše** v horní části podokna výběru. Pokud chcete vybrat pouze prostředky, které jsou aktuálně ve vybraném katalogu, ponechejte zaškrtávací políčko **všechny** nezaškrtnuté (výchozí stav).

1. Jakmile vyberete prostředky, v seznamu **role** vyberte roli, kterou chcete přiřadit uživatelům pro daný prostředek.

    ![Přístup k balíčku – výběr role prostředků](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Klikněte na **Next** (Další).

>[!NOTE]
>Do katalogu a přístupového balíčku můžete přidat dynamické skupiny. Při správě prostředku dynamické skupiny v přístupovém balíčku ale budete moct vybrat jenom roli vlastníka.

## <a name="requests"></a>Žádosti

Na kartě **žádosti** vytvoříte první zásadu, která určí, kdo může žádat o přístup k balíčku a také nastavení schválení. Později můžete vytvořit další zásady vyžádání, které umožní více skupinám uživatelů žádat o přístup k balíčku s vlastním nastavením schválení.

![Přístup na kartu požadavky na balíček](./media/entitlement-management-access-package-create/requests.png)

V závislosti na tom, kdo má být schopný požádat o přístup k tomuto balíčku, proveďte kroky v jednom z následujících částí.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Kontrola a vytvoření

Na kartě **Revize + vytvořit** můžete zkontrolovat nastavení a zkontrolovat chyby ověřování.

1. Kontrola nastavení balíčku přístupu

    ![Přístup k balíčku – povolení nastavení zásad](./media/entitlement-management-access-package-create/review-create.png)

1. Kliknutím na **vytvořit** vytvořte balíček pro přístup.

    Nový balíček pro přístup se zobrazí v seznamu balíčků přístupu.

## <a name="creating-an-access-package-programmatically"></a>Programové vytvoření balíčku pro přístup

Balíček pro přístup můžete také vytvořit pomocí Microsoft Graph.  Uživatel v příslušné roli s aplikací, která má delegované `EntitlementManagement.ReadWrite.All` oprávnění, může volat rozhraní API na

1. [Seznamte se s accessPackageResources v katalogu](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta) a [vytvořte accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta) pro všechny prostředky, které ještě nejsou v katalogu.
1. [Vypíše accessPackageResourceRoles](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) každého accessPackageResourceu v accessPackageCatalog. Tento seznam rolí se pak použije k výběru role při následném vytvoření accessPackageResourceRoleScope.
1. [Vytvořte accessPackage](/graph/tutorial-access-package-api?view=graph-rest-beta).
1. [Vytvořte accessPackageAssignmentPolicy](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta).
1. [Vytvořte accessPackageResourceRoleScope](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta) pro každou roli prostředku potřebnou v balíčku pro přístup.

## <a name="next-steps"></a>Další kroky

- [Sdílet odkaz pro vyžádání balíčku pro přístup](entitlement-management-access-package-settings.md)
- [Změna rolí prostředků pro balíček pro přístup](entitlement-management-access-package-resources.md)
- [Přímé přiřazení uživatele k balíčku pro přístup](entitlement-management-access-package-assignments.md)
- [Vytvoření kontroly přístupu pro balíček přístupu](entitlement-management-access-reviews-create.md)
