---
title: Přidání propojené organizace ve správě nároků Azure AD – Azure Active Directory
description: Naučte se, jak umožnit lidem mimo vaši organizaci žádat o přístup k balíčkům, abyste mohli spolupracovat na projektech.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c57f23a5cad2f47311b54728d576eb810ad88a4
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506018"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Přidání propojené organizace v Azure AD – Správa nároků

Díky správě nároků služby Azure Active Directory (Azure AD) můžete spolupracovat s lidmi mimo vaši organizaci. Pokud často spolupracujete s uživateli v externím adresáři služby Azure AD nebo v doméně, můžete je přidat jako připojenou organizaci. Tento článek popisuje, jak přidat propojenou organizaci, abyste mohli uživatelům mimo vaši organizaci umožnit vyžádat si prostředky v adresáři.

## <a name="what-is-a-connected-organization"></a>Co je propojená organizace?

Připojená organizace je externí adresář nebo doména služby Azure AD, ke kterým máte relaci.

Předpokládejme například, že pracujete s Woodgrove bankou a chcete spolupracovat se dvěma externími organizacemi. Tyto dvě organizace mají různé konfigurace:

- Graphics design Institute používá Azure AD a jejich uživatelé mají hlavní název uživatele, který končí na *graphicdesigninstitute.com*.
- Společnost Contoso zatím nepoužívá službu Azure AD. Uživatelé společnosti Contoso mají hlavní název uživatele, který končí na *contoso.com*.

V takovém případě můžete nakonfigurovat dvě připojené organizace. Vytvoříte jednu propojenou organizaci pro Graphics design Institute a jednu pro contoso. Pokud pak přidáte dvě připojené organizace do zásady, můžou uživatelé z každé organizace s hlavním názvem uživatele, který odpovídá zásadám, vyžadovat přístup k balíčkům. Uživatelé s hlavním názvem uživatele, který má doménu *graphicdesigninstitute.com* , by odpovídaly organizaci s grafikou spojené s nástrojem Graphics design a můžou odesílat žádosti. Uživatelé s hlavním názvem uživatele, který má doménu *contoso.com* , by se shodovali s organizací připojenou k společnosti Contoso a měli by taky žádat o balíčky. A protože nástroj Graphics design Institute používá službu Azure AD, všichni uživatelé s hlavním názvem, který se shoduje s [ověřenou doménou](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) , která je přidána do svého tenanta, jako je například *graphicdesigninstitute. example*, by také mohly žádat o přístup k balíčkům pomocí stejné zásady.

![Příklad propojené organizace](./media/entitlement-management-organization/connected-organization-example.png)

Způsob ověřování uživatelů v adresáři nebo doméně služby Azure AD závisí na typu ověřování. Typy ověřování pro připojené organizace jsou:

- Azure AD
- [Přímá federace](../b2b/direct-federation.md)
- [Jednorázové heslo](../b2b/one-time-passcode.md) (doména)

Ukázku, jak přidat propojenou organizaci, najdete v následujícím videu:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Přidání připojené organizace

Pokud chcete přidat externí adresář nebo doménu služby Azure AD jako připojenou organizaci, postupujte podle pokynů v této části.

**Požadovaná role**: *globální správce* nebo *Správce uživatelů*

1. V Azure Portal vyberte **Azure Active Directory**a potom vyberte zásady **správného řízení identity**.

1. V levém podokně vyberte **připojené organizace**a pak vyberte **Přidat připojenou organizaci**.

    ![Tlačítko Přidat připojenou organizaci](./media/entitlement-management-organization/connected-organization.png)

1. Vyberte kartu **základy** a potom zadejte zobrazovaný název a popis pro organizaci.

    ![Podokno základy přidat připojenou organizaci](./media/entitlement-management-organization/organization-basics.png)

1. Vyberte kartu **adresář + doména** a pak vyberte **Přidat adresář + doména**.

    Otevře se podokno **Vybrat adresáře a domény** .

1. Do vyhledávacího pole zadejte název domény, ve které chcete vyhledat adresář nebo doménu služby Azure AD. Nezapomeňte zadat celý název domény.

1. Ověřte, že název organizace a typ ověřování jsou správné. Způsob, jakým se uživatelé přihlásí, závisí na typu ověřování.

    ![Podokno "vybrat adresáře + domény"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Vyberte **Přidat** a přidejte adresář nebo doménu služby Azure AD. V současné době můžete pro každou propojenou organizaci přidat jenom jeden adresář nebo doménu služby Azure AD.

    > [!NOTE]
    > Všichni uživatelé z adresáře nebo domény služby Azure AD budou moci požádat o tento balíček přístupu. To zahrnuje uživatele v Azure AD ze všech subdomén přidružených k adresáři, pokud tyto domény nejsou blokované seznamem povolených a zakázaných aplikací Azure AD Business to Business (B2B). Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](../b2b/allow-deny-list.md).

1. Po přidání adresáře nebo domény služby Azure AD vyberte **Vybrat**.

    V seznamu se zobrazí organizace.

    ![Podokno adresář + doména](./media/entitlement-management-organization/organization-directory-domain.png)

1. Vyberte kartu **sponzoři** a pak přidejte volitelné sponzoři pro tuto připojenou organizaci.

    Sponzoři jsou interní nebo externí uživatelé, kteří jsou již ve vašem adresáři, které jsou kontaktním bodem pro vztah s touto propojenou organizací. Interní sponzoři jsou členské uživatele ve vašem adresáři. Externí sponzoři jsou uživatelé typu host z připojené organizace, kteří byli dříve pozváni a jsou již ve vašem adresáři. Sponzory můžou být využívány jako schvalovatelé, když uživatelé v této připojené organizaci požadují přístup k tomuto balíčku přístupu. Informace o tom, jak pozvat uživatele typu Host do adresáře, najdete [v tématu přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../b2b/add-users-administrator.md).

    Když vyberete možnost **Přidat nebo odebrat**, otevře se podokno, ve kterém můžete zvolit interní nebo externí sponzoři. V podokně se zobrazí nefiltrovaný seznam uživatelů a skupin ve vašem adresáři.

    ![Podokno sponzoři](./media/entitlement-management-organization/organization-sponsors.png)

1. Vyberte kartu **Revize + vytvořit** , zkontrolujte nastavení organizace a pak vyberte **vytvořit**.

    ![Podokno "revize + vytvořit"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aktualizace propojené organizace 

Pokud se připojená organizace změní v jiné doméně, změní se název organizace nebo chcete změnit sponzory, můžete propojenou organizaci aktualizovat podle pokynů uvedených v této části.

**Požadovaná role**: *globální správce* nebo *Správce uživatelů*

1. V Azure Portal vyberte **Azure Active Directory**a potom vyberte zásady **správného řízení identity**.

1. V levém podokně vyberte **propojené organizace**a pak ji otevřete, když vyberete připojenou organizaci.

1. V podokně přehledu připojené organizace vyberte **Upravit** a změňte název nebo popis organizace.  

1. V podokně **adresář a doména** vyberte **aktualizovat adresář + doména** , aby se změnila na jiný adresář nebo doménu.

1. V podokně **sponzoři** vyberte **Přidat interní sponzoři** nebo **přidejte externí sponzoři** a přidejte uživatele jako sponzora. Chcete-li odebrat sponzora, vyberte sponzora a v pravém podokně vyberte **Odstranit**.


## <a name="delete-a-connected-organization"></a>Odstranění propojené organizace

Pokud už nebudete mít relaci s externím adresářem nebo doménou služby Azure AD, můžete propojenou organizaci odstranit.

**Požadovaná role**: *globální správce* nebo *Správce uživatelů*

1. V Azure Portal vyberte **Azure Active Directory**a potom vyberte zásady **správného řízení identity**.

1. V levém podokně vyberte **propojené organizace**a pak ji otevřete, když vyberete připojenou organizaci.

1. V podokně přehledu připojené organizace vyberte **Odstranit** a odstraňte ji.

    V současné době můžete propojenou organizaci odstranit pouze v případě, že nejsou k dispozici žádní uživatelé.

    ![Tlačítko Odstranit připojenou organizaci](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>Další kroky

- [Řízení přístupu pro externí uživatele](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [Řídí přístup pro uživatele, kteří nejsou ve vašem adresáři.](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
