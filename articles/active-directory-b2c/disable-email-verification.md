---
title: Zakázat ověřování e-mailů při registraci zákazníka
titleSuffix: Azure AD B2C
description: Naučte se zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9d24bd65f1bd473560585ec9d594ddc77d858177
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111273"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ve výchozím nastavení Azure Active Directory B2C (Azure AD B2C) ověřuje e-mailovou adresu vašeho zákazníka u místních účtů (účty pro uživatele, kteří se registrují pomocí e-mailové adresy nebo uživatelského jména). Azure AD B2C zajišťuje platné e-mailové adresy tím, že je vyžaduje, aby je uživatelé během procesu registrace ověřili. Zabraňuje také škodlivým aktérům v použití automatizovaných procesů ke generování podvodných účtů ve vašich aplikacích.

Někteří vývojáři aplikací chtějí během procesu registrace přeskočit ověřování e-mailu a místo toho můžou e-mailové adresy ověřit později. V případě potřeby je Azure AD B2C možné nakonfigurovat pro zákaz ověřování e-mailů. Tím dojde k vytvoření plynulého procesu registrace a vývojářům umožníte odlišit zákazníky, kteří si ověřili jejich e-mailovou adresu od zákazníků, kteří ne.

> [!WARNING]
> Vypnutí ověřování e-mailu v procesu registrace může vést k spamu. Pokud zakážete výchozí ověřování e-mailů poskytnutých Azure AD B2C, doporučujeme implementovat systém náhradního ověřování.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Zakázání ověření e-mailu

::: zone pivot="b2c-user-flow"

Ověřování e-mailu zakážete pomocí těchto kroků:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete zakázat ověřování e-mailů. Například *B2C_1_signinsignup*.
1. Vyberte **rozložení stránky**.
1. Vyberte **stránku pro registraci místního účtu**.
1. V části **atributy uživatele** vyberte **e-mailová adresa**.
1. V rozevíracím seznamu **vyžaduje ověření** vyberte možnost **ne**.
1. Vyberte **Uložit**. Ověřování e-mailu je teď pro tento tok uživatelů zakázané.

::: zone-end

::: zone pivot="b2c-custom-policy"
Technický profil **LocalAccountSignUpWithLogonEmail** je [samostatně](self-asserted-technical-profile.md)vyvolaný, který se vyvolá během procesu registrace. Chcete-li zakázat ověřování e-mailů, nastavte `EnforceEmailVerification` metadata na hodnotu NEPRAVDA. Potlačí technické profily LocalAccountSignUpWithLogonEmail v souboru rozšíření. 

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte `ClaimsProviders` element. Pokud element neexistuje, přidejte jej.
1. Do elementu přidejte následující zprostředkovatele deklarací `ClaimsProviders` :

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Testování zásad 

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete zakázat ověřování e-mailů. Například *B2C_1_signinsignup*.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** .
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy bez ověření.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **architekturu prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu** a pak nahrajte dva soubory zásad, které jste změnili.
1. Vyberte zásadu registrace nebo přihlašování, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy bez ověření.

::: zone-end


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [přizpůsobit uživatelské rozhraní v Azure Active Directory B2C](customize-ui-with-html.md)

