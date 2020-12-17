---
title: Nastavení toku resetování hesla
titleSuffix: Azure AD B2C
description: Přečtěte si, jak nastavit tok resetování hesla v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618756"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Nastavení toku resetování hesla v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Tok REST hesla

Zásada resetování hesla umožňuje uživatelům resetovat svoje vlastní zapomenuté heslo. Tok resetování hesla zahrnuje následující kroky: 
1. Na stránce registrace a přihlášení uživatel klikne na Zapomenuté heslo? . Azure AD B2C vrátí kód chyby AADB2C90118 vaší aplikace. Aplikace zpracovává tento kód chyby vyvoláním zásady resetování hesla. 
1. Uživatelé zadávají a ověřují svůj e-mail s časovým limitem jednorázového hesla.
1. Zadejte nové heslo.

![Tok resetování hesla](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Předpoklady

Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci v Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Vytvoření toku uživatele pro resetování hesla

Pokud chcete uživatelům vaší aplikace povolit resetování hesla, použijte uživatelský tok pro resetování hesla.

1. V nabídce přehled Azure AD B2C tenanta vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte tok uživatele **resetování hesla** . 
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**.
1. Zadejte **název** toku uživatele. Například *passwordreset1*.
1. U **zprostředkovatelů identity** povolte možnost **resetovat heslo pomocí e-mailové adresy**.
2. V části deklarace identity aplikace klikněte na **Zobrazit další** a vyberte deklarace identity, které chcete vrátit do vaší aplikace, do své aplikace. Vyberte například **ID objektu uživatele**.
3. Klikněte na **OK**.
4. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele**, ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a vyberte **pokračovat**.
1. Teď máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat cesty uživatelů. Nabízíme počáteční balíčky s několika předem sestavenými zásadami, včetně registrace a přihlášení, resetování hesla a zásad úprav profilů. Další informace najdete v tématu [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Další kroky

Nastavte [tok úprav profilu](add-profile-editing-policy.md).