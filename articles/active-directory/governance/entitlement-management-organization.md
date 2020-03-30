---
title: Přidání připojené organizace ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak povolit lidem mimo vaši organizaci, aby požádali o přístupové balíčky, abyste mohli spolupracovat na projektech.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee370bc9c381eb11ae7cae53b31d0c987c52733c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128615"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Přidání připojené organizace ve správě nároků Azure AD

Se správou nároků Azure Active Directory (Azure AD) můžete spolupracovat s lidmi mimo vaši organizaci. Pokud často spolupracujete s uživateli v externím adresáři nebo doméně Azure AD, můžete je přidat jako připojenou organizaci. Tento článek popisuje, jak přidat připojenou organizaci, abyste mohli uživatelům mimo vaši organizaci povolit požadovat prostředky ve vašem adresáři.

## <a name="what-is-a-connected-organization"></a>Co je připojená organizace?

Připojená organizace je externí adresář nebo doména Azure AD, se kterou máte vztah.

Předpokládejme například, že pracujete ve společnosti Woodgrove Bank a chcete spolupracovat se dvěma externími organizacemi. Tyto dvě organizace mají různé konfigurace:

- Graphic Design Institute používá Azure AD a jejich uživatelé mají hlavní název uživatele, který končí *graphicdesigninstitute.com*.
- Contoso ještě nepoužívá Azure AD. Uživatelé společnosti Contoso mají hlavní uživatelské jméno, které končí *contoso.com*.

V takovém případě můžete nakonfigurovat dvě připojené organizace. Vytvoříte jednu propojenou organizaci pro Institut grafického návrhu a jednu pro společnost Contoso. Pokud potom přidáte dvě připojené organizace do zásady, mohou uživatelé z každé organizace s hlavním jménem uživatele, který odpovídá zásadě, požádat o přístupové balíčky. Uživatelé s hlavním uživatelským názvem, který má doménu *graphicdesigninstitute.com* by odpovídalorganizaci připojené k Institutu grafického designu a mohli by odesílat žádosti. Uživatelé s hlavním uživatelským názvem, který má doménu *contoso.com* by odpovídala organizaci připojená ke společnosti Contoso a také by mohli požadovat balíčky. A protože Graphic Design Institute používá Azure AD, všichni uživatelé s hlavním názvem, který odpovídá [ověřené domény,](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) která je přidána do jejich tenanta, jako je například *graphicdesigninstitute.example*, by také moci požádat o přístup balíčky pomocí stejné zásady.

![Příklad propojené organizace](./media/entitlement-management-organization/connected-organization-example.png)

Způsob, jakým uživatelé z adresáře Azure AD nebo ověření domény závisí na typu ověřování. Typy ověřování pro připojené organizace jsou:

- Azure AD
- [Přímá federace](../b2b/direct-federation.md)
- [Jednorázový přístupový kód](../b2b/one-time-passcode.md) (doména)

Ukázku toho, jak přidat připojenou organizaci, nazívisí následující video:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Přidání připojené organizace

Pokud chcete přidat externí adresář nebo doménu Azure AD jako připojenou organizaci, postupujte podle pokynů v této části.

**Role předpokladu**: *Globální správce*, *Správce uživatele*nebo *Uživatel*

1. Na webu Azure Portal vyberte **Azure Active Directory**a pak vyberte **Zásadsprávné řízení identit**.

1. V levém podokně vyberte **Připojené organizace**a pak vyberte **Přidat připojenou organizaci**.

    ![Tlačítko Přidat připojenou organizaci](./media/entitlement-management-organization/connected-organization.png)

1. Vyberte kartu **Základy** a zadejte zobrazovaný název a popis organizace.

    ![Podokno Základy pro přidání připojené organizace](./media/entitlement-management-organization/organization-basics.png)

1. Vyberte kartu **Adresář + doména** a pak vyberte **Přidat adresář + doménu**.

    Otevře se podokno **Vybrat adresáře + domény.**

1. Do vyhledávacího pole zadejte název domény a vyhledejte adresář nebo doménu Azure AD. Nezapomeňte zadat celý název domény.

1. Ověřte, zda je název organizace a typ ověřování správné. Způsob přihlášení uživatelů závisí na typu ověřování.

    ![Podokno Vybrat adresáře + domény](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Vyberte **Přidat,** chcete-li přidat adresář nebo doménu Azure AD. V současné době můžete přidat pouze jeden adresář azure ad nebo domény na připojené organizace.

    > [!NOTE]
    > Všichni uživatelé z adresáře nebo domény Azure AD budou moct požádat o tento přístupový balíček. To zahrnuje uživatele ve službě Azure AD ze všech subdomén přidružených k adresáři, pokud tyto domény nejsou blokovány firmou Azure AD na obchodní (B2B) povolit nebo odepřít seznam. Další informace naleznete v [tématu Povolit nebo blokovat pozvánky uživatelům B2B z konkrétních organizací](../b2b/allow-deny-list.md).

1. Po přidání adresáře nebo domény Azure AD vyberte **Vybrat**.

    Organizace se zobrazí v seznamu.

    ![Podokno Adresář + doména](./media/entitlement-management-organization/organization-directory-domain.png)

1. Vyberte kartu **Sponzoři** a přidejte volitelné sponzory pro tuto připojenou organizaci.

    Sponzoři jsou interní nebo externí uživatelé, kteří jsou již ve vašem adresáři a jsou kontaktním místem pro vztah s touto připojenou organizací. Interní sponzoři jsou členy uživatele ve vašem adresáři. Externí sponzoři jsou uživatelé typu Host z připojené organizace, kteří byli dříve pozváni a jsou již ve vašem adresáři. Sponzoři mohou být využíváni jako schvalovatelé, když uživatelé v této připojené organizaci požadují přístup k tomuto přístupu. Informace o tom, jak pozvat uživatele typu Host do adresáře, najdete [v tématu Přidání uživatelů spolupráce Služby Azure AD B2B na webu Azure Portal](../b2b/add-users-administrator.md).

    Když vyberete **Přidat nebo odebrat**, otevře se podokno, ve kterém můžete vybrat interní nebo externí sponzory. Podokno zobrazuje nefiltrovaný seznam uživatelů a skupin v adresáři.

    ![Podokno Sponzoři](./media/entitlement-management-organization/organization-sponsors.png)

1. Vyberte kartu **Revize + vytvoření,** zkontrolujte nastavení organizace a pak vyberte **Vytvořit**.

    ![Podokno Revize + vytvoření](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aktualizace připojené organizace 

Pokud se připojená organizace změní na jinou doménu, změní se název organizace nebo chcete změnit sponzory, můžete připojenou organizaci aktualizovat podle pokynů v této části.

**Role předpokladu**: *Globální správce*, *Správce uživatele*nebo *Uživatel*

1. Na webu Azure Portal vyberte **Azure Active Directory**a pak vyberte **Zásadsprávné řízení identit**.

1. V levém podokně vyberte **Připojené organizace**a pak ji otevřete vyberte připojenou organizaci.

1. V podokně přehledu připojené organizace vyberte **Upravit** a změňte název nebo popis organizace.  

1. V podokně **Adresář + doména** vyberte **Aktualizovat adresář + doménu,** kterou chcete změnit na jiný adresář nebo doménu.

1. V podokně **Sponzoři** vyberte **Přidat interní sponzory** nebo **Přidat externí sponzory,** abyste přidali uživatele jako sponzora. Chcete-li odebrat sponzora, vyberte sponzora a v pravém podokně vyberte **odstranit**.


## <a name="delete-a-connected-organization"></a>Odstranění připojené organizace

Pokud už nemáte vztah s externím adresářem nebo doménou Azure AD, můžete odstranit připojenou organizaci.

**Role předpokladu**: *Globální správce*, *Správce uživatele*nebo *Uživatel*

1. Na webu Azure Portal vyberte **Azure Active Directory**a pak vyberte **Zásadsprávné řízení identit**.

1. V levém podokně vyberte **Připojené organizace**a pak ji otevřete vyberte připojenou organizaci.

1. V podokně přehledu připojené organizace vyberte **Odstranit,** abyste ho odstranili.

    V současné době můžete odstranit připojenou organizaci pouze v případě, že nejsou připojeni žádní uživatelé.

    ![Tlačítko Odstranit připojenou organizaci](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Řídit přístup uživatelů, kteří nejsou ve vašem adresáři](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
