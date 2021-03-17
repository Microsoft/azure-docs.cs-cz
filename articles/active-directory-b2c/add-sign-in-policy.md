---
title: Nastavení toku přihlášení
titleSuffix: Azure Active Directory B2C
description: Naučte se, jak nastavit tok pro přihlašování v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c84966f7b4dc1740235fe6414da2ba832a1334fd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119905"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Nastavení toku přihlášení v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Přehled toku přihlášení

Zásady přihlašování umožňují uživatelům: 

* Uživatelé se můžou přihlásit pomocí Azure AD B2C místního účtu.
* Registrace nebo přihlášení pomocí účtu sociální sítě
* Resetování hesla
* Uživatelé se nemohou zaregistrovat k Azure AD B2C místní účet. K vytvoření účtu může správce použít [Azure Portal](manage-users-portal.md#create-a-consumer-user)nebo [MS Graph API](microsoft-graph-operations.md).

![Tok úprav profilu](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Požadavky

Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci v Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Vytvoření toku uživatele pro přihlašování

Přidání zásady přihlašování:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte tok uživatele **přihlášení** .
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)
1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V případě **zprostředkovatelů identity** vyberte **přihlášení k e-mailu**.
1. V případě **deklarací identity aplikací** vyberte deklarace identity a atributy, které chcete do aplikace odeslat. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace identity pro **zobrazované jméno**, **KŘESTNÍ jméno**,  **příjmení** a **ID objektu uživatele**. Klikněte na **OK**.
1. Kliknutím na **vytvořit** přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele**.
1. Měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili (pomocí programu MS Graph API), bez odkazu na registraci. Vrácený token zahrnuje deklarace, které jste vybrali.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Odebrat odkaz pro registraci

Technický profil **SelfAsserted-LocalAccountSignin-email** je [samostatně](self-asserted-technical-profile.md)vyvolaný, který je vyvolán během registrace nebo přihlašování. Chcete-li odebrat odkaz pro registraci, nastavte `setting.showSignupLink` metadata na hodnotu `false` . V souboru rozšíření přepište technické profily SelfAsserted-LocalAccountSignin-email. 

1. Otevřete soubor rozšíření vaší zásady. Například _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Vyhledejte `ClaimsProviders` element. Pokud element neexistuje, přidejte jej.
1. Do elementu přidejte následující zprostředkovatele deklarací `ClaimsProviders` :

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. V rámci `<BuildingBlocks>` elementu přidejte následující [ContentDefinition](contentdefinitions.md) , které odkazují na verzi 1.2.0, nebo na novější identifikátor URI dat:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Aktualizace a testování zásad

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **architekturu prostředí identity**.
1. Vyberte **nahrát vlastní zásadu** a pak nahrajte soubor zásad, který jste změnili, *TrustFrameworkExtensions.xml*.
1. Vyberte zásadu přihlášení, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
1. Měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili (pomocí programu MS Graph API), bez odkazu na registraci.

::: zone-end

## <a name="next-steps"></a>Další kroky

* Přidejte [přihlášení pomocí zprostředkovatele sociální identity](add-identity-provider.md).
* Nastavte [tok resetování hesla](add-password-reset-policy.md).
