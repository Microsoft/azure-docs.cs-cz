---
title: Kurz konfigurace biozachycení pomocí Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s biozachycením pro identifikaci rizikových a falešných uživatelů
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f15cc294a0d3d930548d7d9bdf1d05b552b60fa5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819357"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>Kurz: Konfigurace biozachycení pomocí Azure Active Directory B2C

V tomto ukázkovém kurzu se dozvíte, jak integrovat Azure Active Directory (AD) B2C ověřování s [Biozachycením](https://www.biocatch.com/) pro další rozšíření stav zabezpečení pro správu identit a přístupu (CIAM) od zákazníka. Biocatch analyzuje fyzické a náročné digitální chování uživatele, aby vygeneroval přehledy, které se liší mezi legitimními zákazníky a internetovými podvodníky.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

- Účet [biocatch](https://www.biocatch.com/contact-us) .

## <a name="scenario-description"></a>Popis scénáře

Integrace biocatch zahrnuje tyto komponenty:

- **Webová aplikace nebo webová služba** – uživatel se nejprve přechází k této webové službě. Tato webová služba vytvoří instanci jedinečného ID klientské relace, která se pošle do biocatch. ID klientské relace pak ihned zahájí přenos vlastností chování uživatele do biocatch.

- **Metoda** – pošle jedinečné ID klientské relace do Azure AD B2C. V uvedeném příkladu je jazyk JavaScript použit k zadání hodnoty do skrytého pole HTML.

- **Azure AD B2C přizpůsobené uživatelské rozhraní** – SKRYJE pole HTML pro vstup ID klientské relace z JavaScriptu, pokud se používá výše uvedenou metodu.

- **Azure AD B2C vlastní zásady**

  - Vezme vlastní ID klientské relace z uživatelského rozhraní ve formě deklarace identity. Toho se dá dosáhnout prostřednictvím technického profilu s vlastním uplatněním.

  - Integruje se s biocatch pomocí REST API zprostředkovatele deklarací identity a předá ID klientské relace k platformě biocatch.

  - Z biocatch se vrátí víc vlastních deklarací, aby se pro vlastní logiku zásad fungovalo.

  - Userjourney, který vyhodnocuje vrácenou deklaraci identity, například riziko relace a podmíněně provede akci, jako je například vyvolání služby Multi-Factor Authentication (MFA).

![Diagram architektury bio catch](media/partner-biocatch/biocatch-architecture-diagram.png)

| Krok  | Description |
|:---|:-----------------------|
|index     | Uživatel prochází webovou službu. Webová služba pak vrátí hodnoty HTML, CSS nebo JavaScript a nakonfiguruje tak, aby načetla sadu SDK pro biocatch JavaScript. JavaScript na straně klienta konfiguruje nebo nastavuje ID klientské relace pro sadu biocatch SDK. Alternativně může webová služba předem nakonfigurovat ID klientské relace a odeslat klientovi.        |
|IB    |  Nakonfigurujte instanci sady SDK pro biozachycení s vytvořeným instancemi proti platformě biocatch. Okamžitě začněte odesílat charakteristiky chování uživatelů pro biozachycení z klientského zařízení pomocí ID klientské relace z kroku 1A.    |
|2     |  Uživatel se pokusí o registraci, přihlášení a přesměrování na Azure AD B2C.      |
|čl    | Součástí userjourney je ClaimsProvider s vlastním kontrolním kódem, který jako vstup přebírá ID klientské relace. Toto pole je na obrazovce skryté. K zadání ID relace do pole můžete použít JavaScript. Kliknutím na tlačítko *Další* pokračujte v procesu registrace nebo přihlašování.|
|3b     | ID klientské relace se odešle do platformy biocatch k určení rizikového skóre. |
|3C     | Biocatch vrátí informace o relaci, jako je například hodnocení rizika, a doporučení ohledně toho, co dělat – povolit nebo zablokovat. |
|3D    |Userjourney má podmíněný kontrolní krok, který funguje na vrácených deklaracích.|
| 4   | Na základě výsledku podmíněné kontroly se vyvolají akce, jako je například *Krok MFA* .|
|5    | Kdykoli od okamžiku, kdy uživatel poprvé narazí na stránku webové služby, může webová služba použít ID relace klienta k dotazování rozhraní API pro biozachycení, aby bylo možné zjistit míru rizika a informace o relaci v reálném čase. |

## <a name="onboard-with-biocatch"></a>Připojení s biozachycením

Kontaktujte [biocatch](https://www.biocatch.com/contact-us) a vytvořte účet.

## <a name="configure-the-custom-ui"></a>Konfigurace vlastního uživatelského rozhraní

Doporučuje se skrýt pole ID relace klienta. Pole skryjte pomocí CSS, JavaScriptu nebo jakékoli jiné metody. Pro účely testování můžete pole zobrazit. Například jazyk JavaScript slouží ke skrytí vstupního pole jako:

```
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Konfigurace Azure AD B2C zásad architektury prostředí identity

1. Nakonfigurujte počáteční [konfiguraci vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started).

2. Vytvořte nový soubor, který dědí ze souboru rozšíření.

    ```
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. Vytvořte odkaz na vlastní uživatelské rozhraní a skryjte vstupní pole pod prostředkem BuildingBlocks.

    ```
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. Do prostředku BuildingBlocks přidejte následující deklarace identity.

    ```
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    <ClaimsSchema> 
    ```

5. Pro pole ID klientské relace nakonfigurujte zprostředkovatele deklarací identity s vlastním uplatněním.

    ```
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. Nakonfigurujte zprostředkovatele deklarací REST API pro biocatch. 

    ```
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item> 

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!Note]
    > Biocatch vám poskytne adresu URL, ID zákazníka a jedinečné ID uživatele (uuID) ke konfiguraci. Deklarace identity SessionID zákazníka je předána jako parametr řetězce dotazu pro biozachycení. Můžete zvolit typ aktivity, například *MAKE_PAYMENT*.

7. Nakonfigurovat userjourney; postupujte podle příkladu

   1. Získání clientSessionID jako deklarace identity

   1. Pro získání informací o relaci zavolejte rozhraní API pro biozachycení.

   1. Pokud *se vrácená deklarace identity* rovná *Nízká*, přeskočte krok MFA, jinak vynuťte uživatele MFA.

    ```
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="9" Type="ClaimsExchange"> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

              </ClaimsExchanges> 

            </OrchestrationStep> 

            <OrchestrationStep Order="10" Type="ClaimsExchange"> 

              <Preconditions> 

                <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

                  <Value>riskLevel</Value> 

                  <Value>LOW</Value> 

                  <Action>SkipThisOrchestrationStep</Action> 

                </Precondition> 

              </Preconditions> 

              <ClaimsExchanges> 

                <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

              </ClaimsExchanges>  

    ```

8. Konfigurovat při konfiguraci předávající strany (volitelné)

    Je užitečné předat do vaší aplikace informace o tom, že se do vaší aplikace vrátí jenom deklarace identity v tokenu, konkrétně *risklevel* a *skore*.

    ```
    <RelyingParty> 

        <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

        <UserJourneyBehaviors> 

          <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

          <SessionExpiryType>Absolute</SessionExpiryType> 

          <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

          <ScriptExecution>Allow</ScriptExecution> 

        </UserJourneyBehaviors> 

        <TechnicalProfile Id="PolicyProfile"> 

          <DisplayName>PolicyProfile</DisplayName> 

          <Protocol Name="OpenIdConnect" /> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="displayName" /> 

            <OutputClaim ClaimTypeReferenceId="givenName" /> 

            <OutputClaim ClaimTypeReferenceId="surname" /> 

            <OutputClaim ClaimTypeReferenceId="email" /> 

            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

            <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

            <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

          </OutputClaims> 

          <SubjectNamingInfo ClaimType="sub" /> 

        </TechnicalProfile> 

      </RelyingParty> 

    ```

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

Chcete-li přidat soubory zásad do Azure AD B2C, postupujte podle těchto kroků.

1. Přihlaste se k  [**Azure Portal**](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte adresář a filtr **předplatného**   a zvolte adresář, který obsahuje vašeho tenanta.

3.  ****   V levém horním rohu Azure Portal vyberte všechny služby, vyhledejte a vyberte Azure AD B2C.

4. Přejít na **Azure AD B2C**   >  **rozhraní identity Experience Framework**

3. Nahrajte všechny soubory zásad do svého tenanta.

## <a name="test-the-solution"></a>Testování řešení

1. [Registrace fiktivní aplikace, která přesměrovává na JWT.MS](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)  

2. V části **Architektura prostředí identity** vyberte zásadu, kterou jste vytvořili.

3. V okně zásady vyberte fiktivní aplikaci JWT.MS a vyberte **spustit hned** .

4. Projděte si postup registrace a vytvořte účet. Token vrácený do JWT.MS by měl mít dvojnásobné deklarace identity pro riskLevel a skóre. Postupujte podle příkladu.  

    ```
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>Další zdroje informací

- [Vlastní zásady v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Začínáme s vlastními zásadami v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
