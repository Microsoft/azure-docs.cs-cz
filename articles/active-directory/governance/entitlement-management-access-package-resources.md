---
title: Změna rolí prostředků pro balíček pro přístup ve správě nároků Azure AD – Azure Active Directory
description: Naučte se, jak změnit role prostředků pro existující balíček přístupu v Azure Active Directory správě nároků.
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
ms.openlocfilehash: 1b6e2ac9d80c1c3bf76b4a3d4c44f0654100670f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89567414"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Změna rolí prostředků pro balíček přístupu v Azure AD – Správa nároků

Jako správce balíčků přístupu můžete kdykoli změnit prostředky v balíčku pro přístup, aniž byste se museli starat o přístup uživatelů k novým prostředkům nebo odebrat jeho přístup z předchozích zdrojů. Tento článek popisuje, jak změnit role prostředků pro existující balíček přístupu.

Toto video poskytuje přehled o tom, jak změnit balíček přístupu.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Kontrolovat prostředky v katalogu

Pokud potřebujete přidat prostředky do balíčku pro přístup, měli byste ověřit, jestli jsou prostředky, které potřebujete, k dispozici v katalogu. Pokud jste správce balíčků pro přístup, nemůžete přidávat prostředky do katalogu, i když je vlastníte. Budete omezeni na používání prostředků dostupných v katalogu.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **katalog** a pak otevřete katalog.

1. V nabídce vlevo klikněte na **prostředky** . zobrazí se seznam prostředků v tomto katalogu.

    ![Seznam prostředků v katalogu](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Pokud jste správce balíčků pro přístup a potřebujete přidat prostředky do katalogu, můžete požádat vlastníka katalogu o jeho přidání.

## <a name="add-resource-roles"></a>Přidat role prostředků

Role prostředku je kolekce oprávnění přidružených k prostředku. Způsob, jakým zpřístupníte prostředky uživatelům k vyžádání, je přidání rolí prostředků do balíčku pro přístup. Můžete přidat role prostředků pro skupiny, týmy, aplikace a weby služby SharePoint.

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **role prostředků**.

1. Kliknutím na **Přidat role prostředků** otevřete stránku přidat role prostředků pro přístup do balíčku.

    ![Přístup k balíčku – přidání rolí prostředků](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. V závislosti na tom, zda chcete přidat skupinu, tým, aplikaci nebo web služby SharePoint, proveďte kroky v jedné z následujících částí role prostředku.

## <a name="add-a-group-or-team-resource-role"></a>Přidat skupinu nebo roli prostředku týmu

V případě, že se jim přiřadí balíček pro přístup, můžete mít správu nároků na přidání uživatelů do skupiny nebo týmu v Microsoft Teams. 

- Když je skupina nebo tým součástí balíčku přístupu a uživatel je přiřazen k tomuto přístupovému balíčku, uživatel se přidá do této skupiny nebo týmu, pokud ještě není k dispozici.
- Po vypršení platnosti přiřazení balíčku přístupu uživatele se ze skupiny nebo týmu odeberou, pokud aktuálně nemají přiřazení k jinému přístupovému balíčku, který obsahuje stejnou skupinu nebo tým.

Můžete vybrat libovolnou [skupinu zabezpečení Azure AD nebo skupinu Microsoft 365](../fundamentals/active-directory-groups-create-azure-portal.md). Správci můžou do katalogu přidat libovolnou skupinu. Vlastníci katalogu můžou přidat libovolnou skupinu do katalogu, pokud jsou vlastníkem skupiny. Při výběru skupiny mějte na paměti následující omezení Azure AD:

- Když se uživatel, včetně hosta, přidá jako člen do skupiny nebo týmu, uvidí všichni ostatní členové této skupiny nebo týmu.
- Azure AD nemůže změnit členství ve skupině, která byla synchronizovaná ze služby Windows Server Active Directory pomocí Azure AD Connect, nebo vytvořeného v systému Exchange Online jako distribuční skupinu.  
- Členství dynamických skupin nelze aktualizovat přidáním nebo odebráním člena, takže dynamická členství ve skupině nejsou vhodná pro použití se správou nároků.

Další informace najdete v tématu [porovnání skupin](/office365/admin/create-groups/compare-groups) a [skupin Microsoft 365 a Microsoft Teams](/microsoftteams/office-365-groups).

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **skupiny a týmy** otevřete podokno vybrat skupiny.

1. Vyberte skupiny a týmy, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr skupin](./media/entitlement-management-access-package-resources/group-select.png)

1. Klikněte na **Vybrat**.

    Jakmile vyberete skupinu nebo tým, sloupec **Sub Type** zobrazí jeden z následujících podtypů:

    | Dílčí typ | Description |
    | --- | --- |
    | Zabezpečení | Používá se pro udělení přístupu k prostředkům. |
    | Distribuce | Slouží k odesílání oznámení skupině lidí. |
    | Microsoft 365 | Microsoft 365 skupina, která není povolená pro týmy. Používá se pro spolupráci mezi uživateli i mimo vaši společnost. |
    | Tým | Microsoft 365 skupina, která je povolená pro týmy. Používá se pro spolupráci mezi uživateli i mimo vaši společnost. |

1. V seznamu **role** vyberte možnost **vlastník** nebo **člen**.

    Obvykle vyberete roli člena. Pokud vyberete roli vlastníka, která umožní uživatelům přidat nebo odebrat jiné členy nebo vlastníky.

    ![Přístup k balíčku – přidání role prostředku pro skupinu nebo tým](./media/entitlement-management-access-package-resources/group-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku přístupu se při přidání automaticky stanou členy této skupiny nebo týmu.

## <a name="add-an-application-resource-role"></a>Přidat roli prostředku aplikace

Azure AD může automaticky přiřazovat uživatelům přístup k podnikovým aplikacím Azure AD, včetně aplikací SaaS i aplikací vaší organizace, které jsou federované do Azure AD, když je uživateli přiřazený balíček přístupu. Pro aplikace, které se integrují se službou Azure AD prostřednictvím federovaného jednotného přihlašování, Azure AD bude vydávat federační tokeny pro uživatele přiřazené k aplikaci.

Aplikace mohou mít více rolí. Pokud má aplikace při přidávání aplikace do balíčku pro přístup více než jednu roli, budete muset pro tyto uživatele zadat příslušnou roli. Pokud vyvíjíte aplikace, můžete si přečíst další informace o tom, jak se tyto role přidávají do vašich aplikací v tématu [Postupy: konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace](../develop/active-directory-enterprise-app-role-management.md).

Jakmile je role aplikace součástí balíčku přístupu:

- Když je uživateli přiřazený balíček přístupu, přidá se do této role aplikace, pokud ještě není k dispozici.
- Po vypršení platnosti přiřazení balíčku přístupu uživatele se z aplikace odeberou jejich přístup, pokud nemají přiřazení k jinému balíčku přístupu, který by tuto roli aplikace zahrnoval.

Zde jsou některé předpoklady při výběru aplikace:

- Aplikace mohou mít také přiřazené skupiny i role.  Můžete se rozhodnout přidat skupinu místo aplikační role v balíčku pro přístup, ale aplikace nebude viditelná pro uživatele jako součást balíčku přístupu na portálu pro přístup.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **aplikace** otevřete podokno vybrat aplikace.

1. Vyberte aplikace, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr aplikací](./media/entitlement-management-access-package-resources/application-select.png)

1. Klikněte na **Vybrat**.

1. V seznamu **role** vyberte roli aplikace.

    ![Přístup k balíčku – přidání role prostředku pro aplikaci](./media/entitlement-management-access-package-resources/application-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou při přidání automaticky mít k této aplikaci přístup.

## <a name="add-a-sharepoint-site-resource-role"></a>Přidat roli prostředku webu služby SharePoint

Azure AD může automaticky přiřadit uživatele k webu SharePointu Online nebo kolekci webů SharePointu Online, když jim přiřadí balíček přístupu.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** kliknutím na **weby služby SharePoint** otevřete podokno vybrat weby SharePointu Online.

1. Vyberte weby SharePointu Online, které chcete zahrnout do balíčku pro přístup.

    ![Přístup k balíčku – přidání rolí prostředků – výběr webů SharePointu Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klikněte na **Vybrat**.

1. V seznamu **role** vyberte roli webu SharePoint Online.

    ![Přístup k balíčku – přidání role prostředku pro web SharePointu Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou při přidání automaticky mít přístup k tomuto webu SharePointu Online.

## <a name="remove-resource-roles"></a>Odebrání rolí prostředků

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. V nabídce vlevo klikněte na **role prostředků**.

1. V seznamu rolí prostředků Najděte roli prostředků, kterou chcete odebrat.

1. Klikněte na tlačítko se třemi tečkami (**...**) a pak klikněte na **Odebrat roli prostředku**.

    Všichni uživatelé s existujícími přiřazeními k balíčku pro přístup budou mít při odebrání do této role prostředků automaticky přístup odvolali.

## <a name="when-changes-are-applied"></a>Při použití změn

V rámci správy nároků služba Azure AD zpracuje hromadnou změnu přiřazení a prostředků v balíčcích přístupu několikrát denně. Takže pokud provedete přiřazení nebo změníte role prostředků vašeho balíčku pro přístup, může trvat až 24 hodin, než se tato změna provede v Azure AD, a navíc dobu potřebnou k rozšíření těchto změn na jiné online služby Microsoftu nebo připojené aplikace SaaS. Pokud vaše změna ovlivní jenom pár objektů, může tato změna trvat jenom pár minut, než se použije ve službě Azure AD, potom ostatní součásti Azure AD tuto změnu odhalí a aktualizují aplikace SaaS. Pokud vaše změna ovlivní tisíce objektů, bude změna trvat déle. Například pokud máte balíček pro přístup se dvěma aplikacemi a 100 přiřazení uživatele a rozhodnete se přidat roli webu služby SharePoint do balíčku pro přístup, může dojít ke zpoždění, dokud všichni uživatelé nebudou součástí této role webu služby SharePoint. Průběh můžete sledovat pomocí protokolu auditu Azure AD, protokolu zřizování Azure AD a protokolů auditu webu služby SharePoint.

Když odeberete člena týmu, odeberou se také z Microsoft 365 skupiny. Odebrání z týmového chatu může trvat o něco déle. Další informace najdete v tématu [členství ve skupině](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Další kroky

- [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Postupy: konfigurace deklarace identity role vydané v tokenu SAML pro podnikové aplikace](../develop/active-directory-enterprise-app-role-management.md)
- [Úvod do SharePointu Online](/sharepoint/introduction)