---
title: Konfigurace vynuceného toku resetování hesla v Azure AD B2C
titleSuffix: Azure AD B2C
description: Přečtěte si, jak nastavit vynucený tok resetování hesla v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6aae76b0b35f8195fb52b7fb11de43d8fa511ba
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028430"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Nastavení vynucení toku resetování hesla v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Jako správce můžete [resetovat heslo uživatele](manage-users-portal.md#reset-a-users-password) , pokud uživatel zapomene heslo. Nebo byste chtěli vynutit resetování hesla. V tomto článku se dozvíte, jak vynutit resetování hesla v těchto scénářích.

## <a name="overview"></a>Přehled

Když správce resetuje uživatelské heslo pomocí Azure Portal, je hodnota atributu [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) nastavena na `true` .

[Cesta pro přihlášení a registraci](add-sign-up-and-sign-in-policy.md) kontroluje hodnotu tohoto atributu. Po dokončení přihlášení uživatelem, pokud je atribut nastaven na hodnotu `true` , musí uživatel resetovat heslo. Hodnota atributu pak je nastavena na hodnotu zpět `false` .

![Vynutit tok resetování hesla](./media/force-password-reset/force-password-reset-flow.png)

Tok resetování hesla se vztahuje na místní účty v Azure AD B2C, které pro přihlášení používají [e-mailovou adresu](identity-provider-local.md#email-sign-in) nebo [uživatelské jméno](identity-provider-local.md#username-sign-in) s heslem.

### <a name="force-a-password-reset-after-90-days"></a>Vynutit resetování hesla po 90 dnech

Jako správce můžete nastavit vypršení platnosti hesla uživatele na 90 dní pomocí [MS graphu](microsoft-graph-operations.md). Po 90 dnech se hodnota atributu [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) automaticky nastaví na `true` . Další informace o tom, jak nastavit zásady vypršení platnosti hesla uživatele, najdete v tématu [atribut zásad hesel](user-profile-attributes.md#password-policy-attribute).

Po nastavení zásad vypršení platnosti hesla je nutné také nakonfigurovat tok vynuceného resetování hesla, jak je popsáno v tomto článku.  

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Konfigurace zásad

::: zone pivot="b2c-user-flow"

Povolení nastavení **vynuceného resetování hesla** v toku registrace nebo přihlašování uživatele:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte registrace a přihlašování nebo přihlašování uživatele (typu **doporučeno**), který chcete přizpůsobit.
1. V nabídce vlevo v části **Nastavení** vyberte **vlastnosti**.
1. V části **složitost hesla** vyberte **vynucené resetování hesla**.
1. Vyberte **Uložit**.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce uživatele nebo správce hesel. Další informace o dostupných rolích najdete v tématu [přiřazení rolí správce v Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Uživatelé**. Vyhledejte a vyberte uživatele, který použijete k otestování resetování hesla, a pak vyberte **resetovat heslo**.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte uživatelský tok pro registraci nebo přihlašování ( **doporučený** typ), který chcete otestovat.
1. Vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.
1. Přihlaste se pomocí uživatelského účtu, pro který resetujete heslo.
1. Nyní musíte změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. Získejte příklad vynuceného resetování hesla na [GitHubu](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset).
1. V každém souboru nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C. Například pokud je název vašeho tenanta B2C *contosob2c*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosob2c.onmicrosoft.com` .
1. Soubory zásad nahrajte v následujícím pořadí: zásady rozšíření `TrustFrameworkExtensionsCustomForcePasswordReset.xml` a pak zásady předávající strany `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>Testování zásad

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce uživatele nebo správce hesel. Další informace o dostupných rolích najdete v tématu [přiřazení rolí správce v Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Uživatelé**. Vyhledejte a vyberte uživatele, který použijete k otestování resetování hesla, a pak vyberte **resetovat heslo**.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte `B2C_1A_signup_signin_Custom_ForcePasswordReset` zásadu, kterou chcete otevřít. 
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](tutorial-register-applications.md). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Přihlaste se pomocí uživatelského účtu, pro který resetujete heslo.
1. Nyní musíte změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

::: zone-end

## <a name="next-steps"></a>Další kroky

Nastavte [Samoobslužné resetování hesla](add-password-reset-policy.md).
