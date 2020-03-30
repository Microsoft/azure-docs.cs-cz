---
title: Změna rolí prostředků pro balíček přístupu ve správě nároků Azure AD – Azure Active Directory
description: Zjistěte, jak změnit role prostředků pro existující balíček přístupu ve správě nároků služby Azure Active Directory.
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
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261889"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Změna rolí prostředků pro balíček přístupu ve správě oprávnění Azure AD

Jako správce přístupových balíčků můžete kdykoli změnit prostředky v balíčku přístupu, aniž byste se museli starat o zřizování přístupu uživatele k novým prostředkům nebo odebrání jejich přístupu z předchozích prostředků. Tento článek popisuje, jak změnit role prostředků pro existující balíček přístupu.

Toto video poskytuje přehled o tom, jak změnit přístupový balíček.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Kontrola zdrojů v katalogu

Pokud potřebujete přidat prostředky do balíčku přístupu, měli byste zkontrolovat, zda jsou prostředky, které potřebujete, k dispozici v katalogu. Pokud jste správce balíčků přístupu, nelze přidat prostředky do katalogu, i v případě, že je vlastníte. Jste omezeni na použití prostředků dostupných v katalogu.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **Katalog** a otevřete katalog.

1. V levé nabídce klikněte na **zdroje** a zobceme seznam zdrojů v tomto katalogu.

    ![Seznam zdrojů v katalogu](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Pokud jste správce balíčků přístupu a potřebujete přidat prostředky do katalogu, můžete požádat vlastníka katalogu, aby je přidal.

## <a name="add-resource-roles"></a>Přidání rolí prostředků

Role prostředku je kolekce oprávnění přidružených k prostředku. Způsob, jakým zpřístupníte prostředky uživatelům k vyžádání, je přidáním rolí prostředků do balíčku přístupu. Role prostředků můžete přidat pro skupiny, týmy, aplikace a sharepointové weby.

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. V levé nabídce klikněte na **Role prostředků**.

1. Kliknutím na **Přidat role prostředků** otevřete stránku Přidat role prostředků pro přístup k balíčku.

    ![Balíček accessu – přidání rolí prostředků](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. V závislosti na tom, zda chcete přidat skupinu, tým, aplikaci nebo sharepointový web, proveďte kroky v jedné z následujících částí role prostředků.

## <a name="add-a-group-or-team-resource-role"></a>Přidání role skupiny nebo týmového prostředku

Správa nároků může automaticky přidávat uživatele do skupiny nebo týmu v Microsoft Teams, když jim bude přiřazen přístupový balíček. 

- Pokud je skupina nebo tým součástí balíčku přístupu a uživatel je přiřazen k tomuto přístupu, uživatel je přidán do této skupiny nebo týmu, pokud již není k dispozici.
- Když vyprší platnost přiřazení balíčku přístupu uživatele, jsou odebrány ze skupiny nebo týmu, pokud aktuálně nemají přiřazení k jinému přístupovému balíčku, který zahrnuje stejnou skupinu nebo tým.

Můžete vybrat libovolnou [skupinu zabezpečení Azure AD nebo skupinu Office 365](../fundamentals/active-directory-groups-create-azure-portal.md). Správci mohou do katalogu přidat libovolnou skupinu. vlastníci katalogu mohou do katalogu přidat libovolnou skupinu, pokud jsou vlastníkem skupiny. Při výběru skupiny mějte na paměti následující omezení azure ad:

- Když je uživatel, včetně hosta, přidán jako člen do skupiny nebo týmu, může zobrazit všechny ostatní členy této skupiny nebo týmu.
- Azure AD nemůže změnit členství ve skupině, která byla synchronizována ze služby Windows Server Active Directory pomocí Služby Azure AD Connect nebo která byla vytvořena v Exchange Online jako distribuční skupina.  
- Členství v dynamických skupinách nelze aktualizovat přidáním nebo odebráním člena, takže členství dynamických skupin není vhodné pro použití se správou oprávnění.

Další informace najdete [v tématu Porovnání skupin](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) a skupin Office [365 a Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. Na stránce **Přidat role prostředků pro přístup k balíčku** klikněte na **Skupiny a týmy a** otevřete podokno Vybrat skupiny.

1. Vyberte skupiny a týmy, které chcete zahrnout do balíčku přístupu.

    ![Balíček Access – přidání rolí prostředků – výběr skupin](./media/entitlement-management-access-package-resources/group-select.png)

1. Klepněte na **tlačítko Vybrat**.

    Jakmile vyberete skupinu nebo tým, sloupec **Podtyp** zobrazí jeden z následujících podtypů:

    |  |  |
    | --- | --- |
    | Zabezpečení | Používá se pro udělení přístupu k prostředkům. |
    | Distribuce | Používá se pro odesílání oznámení skupině osob. |
    | O365 | Skupina Office 365, která nemá povoleno teams. Používá se pro spolupráci mezi uživateli, a to jak uvnitř, tak mimo vaši společnost. |
    | Tým | Skupina Office 365 s podporou Teams. Používá se pro spolupráci mezi uživateli, a to jak uvnitř, tak mimo vaši společnost. |

1. V seznamu **Role** vyberte **Vlastník** nebo **Člen**.

    Obvykle vyberete roli člena. Pokud vyberete roli Vlastník, umožní to uživatelům přidat nebo odebrat další členy nebo vlastníky.

    ![Balíček Access – přidání role prostředku pro skupinu nebo tým](./media/entitlement-management-access-package-resources/group-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k přístupovému balíčku se automaticky stanou členy této skupiny nebo týmu, jakmile budou přidáni.

## <a name="add-an-application-resource-role"></a>Přidání role prostředku aplikace

Azure AD můžete automaticky přiřadit uživatelům přístup k podnikové aplikaci Azure AD, včetně aplikací SaaS a aplikací vaší organizace federovaných do Azure AD, když je uživateli přiřazen přístupový balíček. Pro aplikace, které se integrují s Azure AD prostřednictvím federovaného jednotného přihlašování, Azure AD bude vydávat tokeny federace pro uživatele přiřazené k aplikaci.

Aplikace mohou mít více rolí. Při přidávání aplikace do balíčku přístupu, pokud tato aplikace má více než jednu roli, budete muset zadat příslušnou roli pro tyto uživatele. Pokud vyvíjíte aplikace, můžete si přečíst další informace o tom, jak jsou tyto role přidány do vašich aplikací v [postupech: Konfigurace deklarace role vydané v tokenu SAML pro podnikové aplikace](../develop/active-directory-enterprise-app-role-management.md).

Jakmile je role aplikace součástí přístupového balíčku:

- Když je uživateli přiřazen tento přístupový balíček, je přidán do této role aplikace, pokud již není k dispozici.
- Když vyprší platnost přiřazení balíčku přístupu uživatele, bude jeho přístup odebrán z aplikace, pokud nemají přiřazení k jinému přístupovému balíčku, který obsahuje tuto roli aplikace.

Zde jsou některé důležité informace při výběru aplikace:

- Aplikace mohou mít také skupiny přiřazené k jejich rolím.  Můžete přidat skupinu místo role aplikace v přístupovém balíčku, ale aplikace nebude viditelná pro uživatele jako součást balíčku přístupu na portálu Můj přístup.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** klikněte na **Aplikace** a otevřete podokno Vybrat aplikace.

1. Vyberte aplikace, které chcete zahrnout do balíčku přístupu.

    ![Balíček Access – přidání rolí prostředků – výběr aplikací](./media/entitlement-management-access-package-resources/application-select.png)

1. Klepněte na **tlačítko Vybrat**.

1. V seznamu **Role** vyberte roli aplikace.

    ![Balíček access – přidání role prostředku pro aplikaci](./media/entitlement-management-access-package-resources/application-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k přístupovému balíčku budou automaticky mít přístup k této aplikaci při přidání.

## <a name="add-a-sharepoint-site-resource-role"></a>Přidání role zdroje webu služby SharePoint

Azure AD může automaticky přiřadit uživatelům přístup k webu SharePointu Online nebo kolekci webů SharePointu Online, když jim je přiřazen přístupový balíček.

1. Na stránce **Přidat role prostředků pro přístup k balíčku** klikněte na **Weby SharePointu** a otevřete podokno Vybrat weby SharePointu Online.

1. Vyberte weby SharePointu Online, které chcete zahrnout do balíčku pro přístup.

    ![Balíček Access – Přidání rolí prostředků – Výběr webů SharePointu Online](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klepněte na **tlačítko Vybrat**.

1. V seznamu **Role** vyberte roli webu SharePointu Online.

    ![Balíček Access – přidání role prostředku pro web SharePointu Online](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klikněte na **Přidat**.

    Všichni uživatelé s existujícími přiřazeními k přístupovému balíčku budou mít automaticky přístup k tomuto webu SharePointu Online, když ho přidáte.

## <a name="remove-resource-roles"></a>Odebrání rolí prostředků

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. V levé nabídce klikněte na **Role prostředků**.

1. V seznamu rolí prostředků vyhledejte roli prostředku, kterou chcete odebrat.

1. Klepněte na tři tečky (**...**) a potom klepněte na tlačítko **Odebrat roli prostředku**.

    Všem uživatelům s existujícími přiřazeními k balíčku pro přístup bude automaticky odebrán přístup k této roli prostředku při jeho odebrání.

## <a name="when-changes-are-applied"></a>Při použití změn

Ve správě nároků bude Azure AD zpracovávat hromadné změny pro přiřazení a prostředky ve vašich přístupových balíčcích několikrát denně. Pokud tedy provedete přiřazení nebo změníte role prostředků vašeho přístupového balíčku, může trvat až 24 hodin, než se tato změna provede ve službě Azure AD, plus dobu, kterou trvá šíření těchto změn v jiných službách Microsoft Online Services nebo připojeném SaaS. Aplikace. Pokud vaše změna ovlivní pouze několik objektů, změna bude pravděpodobně trvat jen několik minut použít ve službě Azure AD, po kterém další součásti Azure AD pak zjistí, že změna a aktualizace aplikací SaaS. Pokud vaše změna ovlivní tisíce objektů, bude změna trvat déle. Pokud například máte přístupový balíček se 2 aplikacemi a 100 přiřazeními uživatelů a rozhodnete se přidat roli sharepointového webu k balíčku pro přístup, může dojít ke zpoždění, dokud nebudou všichni uživatelé součástí této role webu služby SharePoint. Průběh můžete sledovat prostřednictvím protokolu auditování Azure AD, protokolu zřizování Azure AD a protokolů auditování webu SharePointu.

Když odeberete člena týmu, odebere se i ze skupiny Office 365. Odebrání z týmového chatu může trvat o něco déle. Další informace naleznete v tématu [Členství ve skupině](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Další kroky

- [Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Postup: Konfigurace deklarace role vydané v tokenu SAML pro podnikové aplikace](../develop/active-directory-enterprise-app-role-management.md)
- [Úvod k SharePointu Online](/sharepoint/introduction)