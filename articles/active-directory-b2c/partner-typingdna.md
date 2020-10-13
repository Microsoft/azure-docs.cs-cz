---
title: TypingDNA s Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Naučte se integrovat Azure AD B2C ověřování pomocí TypingDNA, abyste mohli pomáhat s ověřováním identity a kontrolou na základě vzoru pro psaní uživatelů, poskytuje řešení pro ověřování ID, které vynucuje vícefaktorové ověřování a pomáhá splnit požadavky SCA na službu pro platební služby. 2 (PSD2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f7d89942ad5209b854b8df486ad3e59a3976edfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259047"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Kurz pro konfiguraci TypingDNA s využitím Azure Active Directory B2C

V tomto návodu se dozvíte, jak integrovat ukázkovou online platební aplikaci v Azure Active Directory B2C s aplikací TypingDNA. Pomocí aplikace TypingDNA můžou zákazníci dodržovat požadavky Azure AD B2C na transakce 2 (PSD2) pro [platební služby](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) prostřednictvím klávesových zkratek Dynamics a silného ověřování zákazníků. Další informace o TypingDNA najdete [tady](https://www.typingdna.com/).

 Azure AD B2C používá technologie TypingDNA k zachycení vlastností pro psaní uživatelů a jejich zaznamenávání a analyzování pro známé účely při každém ověřování. Tím přidáte vrstvu ochrany související s riskiness ověřování a vyhodnocujete úrovně rizika. Azure AD B2C může vyvolat jiné mechanismy, aby bylo možné zajistit další jistotu, že uživatel je vyvolán vyvoláním Azure MFA, vynucením ověřování e-mailu nebo jakékoli jiné vlastní logiky pro váš scénář.

>[!NOTE]
> Tato ukázková zásada je založená na [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Start Pack.

## <a name="scenario-description"></a>Popis scénáře

![Snímek obrazovky s diagramem architektury TypingDNA](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Registrace

1. Azure AD B2C stránky používají knihovnu JavaScript TypingDNA k záznamu vzoru zápisu uživatele. Například uživatelské jméno a heslo se zaznamenávají při registraci při prvotní registraci a pak při každém přihlášení k ověření.

2. Když uživatel stránku odešle, bude knihovna TypingDNA počítat s charakteristikou zadání uživatele. Potom tyto informace vložte do skrytého textového pole, které Azure AD B2C vykreslené. Toto pole je skryté pomocí šablon stylů CSS.  

    [Ukázka obsahuje soubory HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) s úpravami jazyka JavaScript a šablon stylů CSS a na ně odkazuje `api.selfasserted.tdnasignin` `api.selfasserted.tdnasignup` definice obsahu a. Chcete-li hostovat soubory HTML, přečtěte si téma [hostování obsahu stránky](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content) .

3. Pokud uživatel odešle svoje přihlašovací údaje, Azure AD B2C nyní má v kontejneru deklarací identity typový vzor. Pro předání těchto dat do koncového bodu TypingDNA REST API musí volat rozhraní API (vaše). Toto rozhraní API je zahrnuté v [ukázce (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. Rozhraní API střední vrstvy pak předá data vzorového vzoru do TypingDNA REST API. Při registraci se zavolá [koncový bod uživatele](https://api.typingdna.com/index.html#api-API_Services-GetUser) , který potvrdí, že uživatel neexistoval, a potom se zavolá koncový bod [vzorového](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) souboru, který bude ukládat první vzor zápisu uživatele.

> [!NOTE]
> Všechna volání do koncového bodu TypingDNA REST API odesílají identifikátor UserId. Musí se jednat o hodnotu hash. Azure AD B2C používá `HashObjectIdWithEmail` transformaci deklarací k hashování e-mailu pomocí náhodné soli a tajného klíče.  

Volání REST API jsou modelována `validationTechnicalProfiles` v rámci `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Přihlášení

Při následném přihlašování se vzorek psaní uživatelem počítá stejným způsobem jako při registraci pomocí [vlastního HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Jakmile je typový profil v rámci Azure AD B2C kontejneru deklarací identity, Azure AD B2C voláním rozhraní API zavolá REST API koncový bod TypingDNA. Je volána koncová koncová verze [uživatele](https://api.typingdna.com/index.html#api-API_Services-GetUser) , aby bylo možné potvrdit, že uživatel existuje. Dále ověřte, že je volán koncový bod [vzoru](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) , který vrátí `net_score` . `net_score`V této části najdete informace o tom, jak se při registraci měl vzorek psaní do původního umístění.

Tento vzor psaní je modelován `validationTechnicalProfiles` v rámci `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Pokud uživatel získá typový vzor, který má vysoké maximum `net_score` , můžete ho uložit pomocí koncového bodu TypingDNA [Uložit zadání](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) .  

Rozhraní API musí vracet deklaraci identity,  `saveTypingPattern` Pokud chcete, aby byl koncový bod TypingDNA Saved Pattern volán pomocí Azure AD B2C (prostřednictvím rozhraní API).

Příklad v úložišti obsahuje rozhraní API (TypingDNA-API-Interface), které je nakonfigurované s následujícími vlastnostmi.

- Školicí režim – Pokud má uživatel méně než dva uložené vzory, vždy se zeptat na MFA.

- Pokud má uživatel 2-5 uložených vzorcích a `net_score` je nižší než 50, vyzvat k ověřování MFA.

- Pokud má uživatel 5 + vzor uložený a `net_score` je nižší než 65, zobrazí se výzva k MFA.

Tyto prahové hodnoty by měly být upraveny na vašem případu použití.

- Po vyhodnocení rozhraní API `net_score` by měl vrátit logickou deklaraci identity na B2C- `promptMFA` .

- `promptMFA`Deklarace identity se používá v rámci předběžného předpokladu k podmíněnému provádění Azure MFA.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Zprovoznění s TypingDNA

1. Zaregistrujte se [do TypingDNA.](https://www.typingdna.com/)
2. Přihlaste se k řídicímu panelu TypingDNA a získejte **klíč rozhraní API** a **tajný klíč rozhraní API**. To bude potřeba v nastavení rozhraní API později.

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integrace TypingDNA s Azure AD B2C

1. Hostování [rozhraní TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) u poskytovatele hostingu dle vlastního výběru
2. Nahraďte všechny instance `apiKey` a `apiSecret` v řešení [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) pomocí přihlašovacích údajů z řídicího panelu TypingDNA.
3. Hostovat soubory HTML podle vašeho poskytovatele dle požadavků CORS uvedených [tady](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors)
4. Nahraďte elementy LoadURI pro `api.selfasserted.tdnasignup` `api.selfasserted.tdnasignin` definice obsahu a v `TrustFrameworkExtensions.xml` souboru s identifikátorem URI hostovaných souborů HTML v uvedeném pořadí.
5. Vytvořte klíč zásad B2C v části architektura prostředí identit v okně Azure AD v **Azure Portal**. Použijte `Generate` možnost a pojmenujte tento klíč `tdnaHashedId` .
6. Nahradit TenantId v souborech zásad
7. Nahraďte ServiceURLs ve všech TypingDNA REST API Technical profiles (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) pomocí koncového bodu [rozhraní API pro rozhraní API TypingDNA](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Nahrajte [soubory zásad](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) do svého tenanta.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete tenanta B2C a vyberte Architektura prostředí identity.
2. Vyberte dříve vytvořený **tok uživatele**.
3. Vyberte **Spustit** tok uživatele.

    a. **Aplikace** – vyberte registrovanou aplikaci (ukázka je JWT).

    b. **Adresa URL odpovědi** – vyberte adresu URL pro přesměrování.

    c. Vyberte **Spustit tok uživatele**.
  
4. Projděte si registrační tok a vytvořte účet.
5. Odhlásit se
6. Projděte si tok přihlášení.
7. Výsledný výsledek JWT zobrazí TypingDNA výsledky.

## <a name="live-version"></a>Živá verze

• MFA je v této verzi testu zakázané, ale můžete se podívat na výsledek toho, jestli by deklarace identity po ověření požadovala MFA `promptMFA` .

• Zaregistrujte se [tady](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) a přihlaste se [sem](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) .

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Vlastní zásady v AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
