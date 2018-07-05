---
title: Přidání poskytovatele služby Azure AD pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o vlastních zásadách Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 139658b57cfdce2603d4413b5bf4c9a86b6a8c14
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444323"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: Přihlaste se pomocí účtů v Azure AD

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure Active Directory (Azure AD) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

Tyto kroky zahrnují:

1. Vytváří se službou Azure Active Directory B2C (Azure AD B2C) tenanta.
2. Vytvoření aplikace Azure AD B2C.
3. Registruje se dvěma aplikacemi modul zásad.
4. Nastavení klíče.
5. Nastavení úvodní balíček.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD.

>[!NOTE]
> Používáme "contoso.com" pro organizační tenanta Azure AD a "fabrikamb2c.onmicrosoft.com" jako tenant Azure AD B2C v následujících pokynech.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na horním panelu vyberte svůj účet. Z **Directory** klikněte na položku organizační tenanta Azure AD, ve které chcete zaregistrovat aplikaci (contoso.com).
3. Vyberte **další služby** v levém podokně a vyhledejte "Registrace aplikací."
4. Vyberte **Registrace nové aplikace**.
5. Zadejte název pro vaši aplikaci (například `Azure AD B2C App`).
6. Jako typ aplikace vyberte položku **Webová aplikace / webové rozhraní API**.
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL, kde `yourtenant` se nahradí názvem vašeho tenanta Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Hodnota pro "yourtenant" musí být malými písmeny v **přihlašovací adresa URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. Uložení ID aplikace.
9. Vyberte nově vytvořenou aplikaci.
10. V části **nastavení** okně vyberte **klíče**.
11. Zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zobrazí se hodnota klíče. Zkopírujte jej, protože ho použijete v krocích v další části.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Přidat klíč služby Azure AD do Azure AD B2C

Potřebujete ukládat contoso.com klíč aplikace ve vašem tenantovi Azure AD B2C. Použijte následující postup:
1. Přejděte do svého tenanta Azure AD B2C a vyberte **nastavení B2C** > **architekturu rozhraní identit** > **klíče zásad**.
1. Vyberte **+ přidat**.
1. Vyberte nebo zadejte tyto možnosti:
   * Vyberte **ruční**.
   * Pro **název**, zvolte název, který odpovídá název tenanta Azure AD (například `ContosoAppSecret`).  Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
   * Vložte klíč aplikace v **tajný kód** pole.
   * Vyberte **podpis**.
1. Vyberte **Vytvořit**.
1. Potvrďte, že jste vytvořili klíč `B2C_1A_ContosoAppSecret`.


## <a name="add-a-claims-provider-in-your-base-policy"></a>Přidat zprostředkovatele deklarací identity v základních zásadách

Pokud chcete uživatelům umožní přihlásit pomocí Azure AD, musíte definovat Azure AD jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který Azure AD B2C bude komunikovat s. Koncový bod poskytne sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Azure AD jako poskytovatele deklarací identity můžete definovat tak, že přidáte Azure AD, aby `<ClaimsProvider>` uzel v souboru rozšíření zásady:

1. Otevřete soubor rozšíření (TrustFrameworkExtensions.xml) z pracovního adresáře.
1. Najít `<ClaimsProviders>` oddílu. Pokud neexistuje, přidejte ho do kořenového uzlu.
1. Přidat nový `<ClaimsProvider>` uzel následujícím způsobem:

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. V části `<ClaimsProvider>` uzlu, aktualizujte hodnotu `<Domain>` na jedinečnou hodnotu, která je možné, aby se odlišil od jiných zprostředkovatelů identity.
1. V části `<ClaimsProvider>` uzlu, aktualizujte hodnotu `<DisplayName>` na popisný název pro zprostředkovatele deklarací identity. Tato hodnota není aktuálně používán.

### <a name="update-the-technical-profile"></a>Aktualizace technický profil

Chcete-li získat token z koncového bodu Azure AD, musíte definovat protokoly, které Azure AD B2C použít ke komunikaci s Azure AD. To se provádí uvnitř `<TechnicalProfile>` prvek `<ClaimsProvider>`.
1. Aktualizace ID `<TechnicalProfile>` uzlu. Toto ID se používá k odkazování na tento technický profil z jiných částí zásad.
1. Aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na obrazovce přihlášení.
1. Aktualizujte hodnotu pro `<Description>`.
1. Azure AD používá protokol OpenID Connect, tak zajistěte, aby hodnota `<Protocol>` je `"OpenIdConnect"`.

Je potřeba aktualizovat `<Metadata>` oddílu v souboru XML uvedené dříve tak, aby odrážely nastavení konfigurace pro konkrétní tenanta Azure AD. V souboru XML aktualizujte hodnoty metadat následujícím způsobem:

1. Nastavte `<Item Key="METADATA">` k `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, kde `yourAzureADtenant` je název tenanta Azure AD (contoso.com).
1. Otevřete prohlížeč a přejděte `METADATA` adresy URL, kterou jste právě aktualizovali.
1. V prohlížeči vyhledejte "vystavitele" objekt a zkopírujte jeho hodnotu. Měl by vypadat nějak takto: `https://sts.windows.net/{tenantId}/`.
1. Vložte tuto hodnotu pro `<Item Key="ProviderName">` v souboru XML.
1. Nastavte `<Item Key="client_id">` do ID aplikace z registraci aplikace.
1. Nastavte `<Item Key="IdTokenAudience">` do ID aplikace z registraci aplikace.
1. Ujistěte se, že `<Item Key="response_types">` je nastavena na `id_token`.
1. Ujistěte se, že `<Item Key="UsePolicyInRedirectUri">` je nastavena na `false`.

Budete také potřebovat odkázat tajný klíč služby Azure AD, který je zaregistrovaný ve vašem tenantovi Azure AD B2C do služby Azure AD `<ClaimsProvider>` informace:

* V `<CryptographicKeys>` části v předchozím soubor XML, aktualizujte hodnotu `StorageReferenceId` referenční ID tajný klíč, který jste definovali (například `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresářem Azure AD. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím. Postupujte následovně:

1. Otevřít **všechny zásady** okno ve vašem tenantovi Azure AD B2C.
1. Zkontrolujte, **přepsat zásady, pokud existuje** pole.
1. Nahrát soubor rozšíření (TrustFrameworkExtensions.xml) a ujistěte se, že se selhání ověření.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Zaregistrovat poskytovatele deklarací identity Azure AD pro cestu uživatele

Teď musíte přidat zprostředkovatele identity Azure AD do jednoho z vaší cesty uživatele. V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Chcete-li k dispozici, jsme se vytvořit duplikát cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity Azure AD:

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
1. Najít `<UserJourneys>` elementu a zkopírujte celý `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"`.
1. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
1. Vložit celé `<UserJourney>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.
1. Přejmenovat ID nové cesty uživatele (například přejmenovat jako `SignUpOrSignUsingContoso`).

### <a name="display-the-button"></a>Zobrazit "tlačítko"

`<ClaimsProviderSelection>` Element je obdobou k tlačítku na obrazovce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` – element pro Azure AD, nové tlačítko se zobrazí při uživatel umístil na stránce. Chcete-li přidat tento element:

1. Najít `<OrchestrationStep>` uzel, který zahrnuje `Order="1"` v cestě uživatele, který jste právě vytvořili.
1. Přidejte následující:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. Nastavte `TargetClaimsExchangeId` na odpovídající hodnotu. Doporučujeme stejnou konvencí jako ostatní:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s Azure AD za účelem získání tokenu. Tlačítko odkazu na akci propojením technický profil pro vašeho poskytovatele deklarací identity Azure AD:

1. Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
1. Přidejte následující:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. Aktualizace `Id` na stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
1. Aktualizace `TechnicalProfileReferenceId` ID technického profilu vytvořeného starší (ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Nahrát soubor aktualizované rozšíření

Dokončení úpravy souboru rozšíření. Uložte ho. Pak nahrajte soubor a ujistěte se, že všechny ověření úspěšné.

### <a name="update-the-rp-file"></a>Aktualizovat soubor RP

Teď je potřeba aktualizovat předávající stranu soubor, který opraví, zahájí se cesty uživatele, který jste právě vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml ve svém pracovním adresáři a přejmenujte jej (například, přejmenujte ho na SignUpOrSignInWithAAD.xml).
1. Otevřete nový soubor a aktualizace `PolicyId` atributu `<TrustFrameworkPolicy>` s jedinečnou hodnotu (například SignUpOrSignInWithAAD). <br> To bude název zásady (například B2C\_1A\_SignUpOrSignInWithAAD).
1. Upravit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpOrSignUsingContoso).
1. Uložte změny a nahrajte soubor.

## <a name="troubleshooting"></a>Řešení potíží

Testování vlastní zásady, které jste právě nahráli, otevřením její okno a kliknutím na **spustit nyní**. K diagnostice problémů, přečtěte si informace o [řešení potíží s](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnout zpětnou vazbu k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
