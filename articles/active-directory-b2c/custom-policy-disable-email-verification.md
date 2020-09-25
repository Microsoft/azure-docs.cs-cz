---
title: Zakázat ověřování e-mailů při registraci zákazníka pomocí vlastní zásady
titleSuffix: Azure AD B2C
description: Naučte se, jak zakázat ověřování e-mailů při registraci zákazníka pomocí vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9372a9caff5aefb53bfa8adf7eb1c68d2a3b7b2e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259607"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>Zakázat ověřování e-mailů při registraci zákazníka pomocí vlastní zásady v Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlášení pomocí sociálních a místních účtů.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Přidejte metadata do technického profilu s vlastním uplatněním.

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

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
4. Vyberte **architekturu prostředí identity**.
5. Vyberte **Odeslat vlastní zásadu**a pak nahrajte dva soubory zásad, které jste změnili.
2. Vyberte zásadu registrace nebo přihlašování, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
3. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy bez ověření.


## <a name="next-steps"></a>Další kroky

- Další informace o [technickém profilu s vlastním uplatněním](self-asserted-technical-profile.md) najdete v referenčních informacích k IEF.
