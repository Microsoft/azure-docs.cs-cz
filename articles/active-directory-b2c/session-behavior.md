---
title: Konfigurace chování relace – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat chování relace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579735"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurace chování relace ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Jednotné přihlašování (SSO) zvyšuje zabezpečení a pohodlí, když se uživatelé přihlásí přes aplikace v Azure Active Directory B2C (Azure AD B2C). Tento článek popisuje metody jednotného přihlašování používané v Azure AD B2C a při konfiguraci zásad vám pomůže vybrat nejvhodnější metodu jednotného přihlašování.

S jednotným přihlašováním se uživatelé přihlásí jedním účtem a získají přístup k více aplikacím. Aplikace může být webová, mobilní nebo jednostránková aplikace, bez ohledu na platformu nebo název domény.

Když se uživatel poprvé přihlásí k aplikaci, Azure AD B2C trvá relaci založenou na souborech cookie. Po následné žádosti o ověření Azure AD B2C přečte a ověří relaci založenou na souborech cookie a vydá přístupový token bez vyzvání uživatele k opětovnému přihlášení. Pokud platnost relace na základě souborů cookie vyprší nebo se stala neplatnou, zobrazí se uživateli výzva k opětovnému přihlášení.  

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Přehled relace Azure AD B2C

Integrace s Azure AD B2C zahrnuje tři typy relací jednotného přihlašování:

- **Azure AD B2C** – relace spravovaná pomocí Azure AD B2C
- **Federovaný zprostředkovatel identity** – relace spravovaná zprostředkovatelem identity, například Facebook, Salesforce nebo účet Microsoft
- **Aplikace** – relace spravovaná webovými, mobilními nebo jednostránkové aplikací

![Relace SSO](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C relace 

Když se uživatel úspěšně ověřuje pomocí místního nebo sociálního účtu, Azure AD B2C ukládá relaci založenou na souborech cookie v prohlížeči uživatele. Soubor cookie je uložen pod názvem Azure AD B2C domény klienta, například `https://contoso.b2clogin.com` .

Pokud se uživatel poprvé přihlásí pomocí federovaného účtu a v časovém intervalu relace (Time-to-Live nebo TTL) se přihlásí ke stejné aplikaci nebo jiné aplikaci, Azure AD B2C se pokusí získat nový přístupový token od zprostředkovatele federovaných identit. Pokud vypršela platnost relace federovaného zprostředkovatele identity nebo je neplatný, vyzve se k zadání přihlašovacích údajů k uživateli federovaný zprostředkovatel identity. Pokud je relace stále aktivní (nebo pokud se uživatel přihlásil pomocí místního účtu místo federovaného účtu), Azure AD B2C autorizuje uživatele a eliminuje další výzvy.

Můžete nakonfigurovat chování relace, včetně TTL relace a způsobu, jakým Azure AD B2C sdílet relaci napříč zásadami a aplikacemi.

### <a name="federated-identity-provider-session"></a>Relace federovaného zprostředkovatele identity

Poskytovatel identity pro sociální sítě nebo organizace spravuje svou vlastní relaci. Soubor cookie je uložen pod názvem domény poskytovatele identity, například `https://login.salesforce.com` . Azure AD B2C neřídí relaci federovaného zprostředkovatele identity. Místo toho se chování relace určuje podle federovaného zprostředkovatele identity. 

Představte si následující scénář:

1. Uživatel se přihlásí do Facebooku, aby zkontroloval svůj informační kanál.
2. Později uživatel otevře vaši aplikaci a spustí proces přihlášení. Aplikace přesměruje uživatele na Azure AD B2C k dokončení procesu přihlašování.
3. Na stránce Azure AD B2C registrace nebo přihlášení se uživatel rozhodne přihlašovat pomocí účtu Facebook. Uživatel se přesměruje na Facebook. Pokud je aktivní relace na Facebooku, uživateli se nezobrazí výzva k zadání přihlašovacích údajů a okamžitě se přesměruje na Azure AD B2C s tokenem Facebooku.

### <a name="application-session"></a>Relace aplikace

Aplikaci pro web, mobilní nebo jednoduchou stránku lze chránit pomocí přístupu OAuth, tokeny ID nebo tokeny SAML. Když se uživatel pokusí o přístup k chráněnému prostředku v aplikaci, aplikace zkontroluje, jestli je na straně aplikace aktivní relace. Pokud nedošlo k žádné relaci aplikace nebo vypršela platnost relace, aplikace převezme uživatele, aby Azure AD B2C přihlašovací stránku.

Relace aplikace může být relace založená na souborech cookie uložená v názvu domény aplikace, jako je například `https://contoso.com` . Mobilní aplikace můžou relaci ukládat jiným způsobem, ale s podobným přístupem.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Konfigurace chování relace Azure AD B2C

Můžete nakonfigurovat chování Azure AD B2C relace, včetně:

- **Doba života relace webové aplikace (minuty)** – doba, po kterou je soubor cookie relace Azure AD B2C uložený v prohlížeči uživatele po úspěšném ověření. Můžete nastavit dobu života relace až na 24 hodin.

- **Časový limit relace webové aplikace** – určuje, jak je relace rozšířena nastavením doba života relace nebo nastavením zůstat přihlášení (políčko zůstat přihlášeni).
  -  Označuje, že relace je rozšířená pokaždé, když uživatel provede ověřování na základě souborů cookie (výchozí).
  - **Absolutní** – označuje, že se uživatel bude nucen znovu ověřit po zadaném časovém období.

- **Konfigurace jednotného přihlašování** – relaci Azure AD B2C lze nakonfigurovat s následujícími rozsahy:
  - **Tenant** – toto nastavení je výchozí. Pomocí tohoto nastavení může více aplikací a uživatelských toků v tenantovi B2C sdílet stejnou relaci uživatele. Když se například uživatel přihlásí do aplikace, může se uživatel při přístupu k němu bez problémů přihlásit i k jinému.
  - **Aplikace** – toto nastavení umožňuje udržovat uživatelskou relaci exkluzivně pro aplikaci, nezávisle na jiných aplikacích. Toto nastavení můžete například použít, pokud chcete, aby se uživatel přihlásil ke společnosti Contoso farmacie bez ohledu na to, jestli je uživatel už přihlášený k nákupům společnosti Contoso.
  - **Zásady** – toto nastavení umožňuje udržovat relaci uživatele výhradně pro uživatelský tok, a to nezávisle na aplikacích, které ji používají. Pokud se například uživatel už přihlásil a dokončil krok vícefaktorového ověřování (MFA), může se uživateli udělit přístup k několika částem aplikace s vyšším zabezpečením, pokud relace vázaná na tok uživatele nevyprší.
  - **Potlačené** – toto nastavení vynutí, aby uživatel při každém spuštění zásady spouštěl celý tok uživatele.
- **Zůstat přihlášeni (políčko zůstat přihlášeni)** – rozšiřuje dobu života relace pomocí trvalého souboru cookie. Pokud je tato funkce povolená a uživatel ji vybere, zůstane relace aktivní, i když uživatel zavře a znovu otevře prohlížeč. Relace se odvolá jenom v případě, že se uživatel odhlásí. Funkce políčko zůstat přihlášeni se vztahuje pouze na přihlášení pomocí místních účtů. Funkce políčko zůstat přihlášeni má přednost před životností relace.

::: zone pivot="b2c-user-flow"

Konfigurace chování relace:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Vlastnosti**.
1. Nakonfigurujte **dobu života relace webové aplikace (minuty)**, **časový limit relace webové aplikace**, **konfiguraci jednotného přihlašování** a **v případě potřeby vyžadovat token ID v žádostech o odhlášení** .
1. Klikněte na **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Chcete-li změnit chování relace a konfigurace jednotného přihlašování, přidejte do elementu [RelyingParty](relyingparty.md) element **UserJourneyBehaviors** .  Element **UserJourneyBehaviors** musí následovat za **DefaultUserJourney**. Váš element **UserJourneyBehavors** by měl vypadat jako v tomto příkladu:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>Povolit možnost zůstat přihlášeni (políčko zůstat přihlášeni)

Funkci políčko zůstat přihlášeni můžete povolit pro uživatele vaší webové a nativní aplikace, které mají v adresáři Azure AD B2C místní účty. Když tuto funkci povolíte, můžou uživatelé zůstat přihlášení, takže po zavření prohlížeče zůstane relace aktivní. Pak můžou prohlížeč znovu otevřít, aniž by se zobrazila výzva k opětovnému zadání uživatelského jména a hesla. Tento přístup se odvolá, když se uživatel odhlásí.

![Příklad přihlašovací stránky pro přihlášení, která zobrazuje zaškrtávací políčko zůstat přihlášeni](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

POLÍČKO zůstat přihlášeni je možné konfigurovat na úrovni jednotlivých uživatelských toků. Před povolením políčko zůstat přihlášeni pro toky uživatelů Vezměte v úvahu následující skutečnosti:

- POLÍČKO zůstat přihlášeni se podporuje jenom pro **Doporučené** verze registrací a přihlašování (SUSI), přihlašování a uživatelských toků úprav profilů. Pokud aktuálně máte verze **Standard** nebo **Legacy Preview-v2** těchto uživatelských toků a chcete povolit políčko zůstat přihlášeni, budete muset vytvořit nové, **Doporučené** verze těchto uživatelských toků.
- POLÍČKO zůstat přihlášeni se nepodporuje s resetováním hesla nebo uživatelskými toky pro registraci.
- Pokud chcete povolit políčko zůstat přihlášeni pro všechny aplikace ve vašem tenantovi, doporučujeme povolit políčko zůstat přihlášeni pro všechny toky uživatelů ve vašem tenantovi. Vzhledem k tomu, že se uživatel může během relace zobrazit s více zásadami, je možné, že může dojít k tomu, že políčko zůstat přihlášeni není povolený, což by v relaci odebralo soubor cookie políčko zůstat přihlášeni.
- POLÍČKO zůstat přihlášeni by neměl být povolený na veřejných počítačích.

### <a name="configure-kmsi-for-a-user-flow"></a>Konfigurace políčko zůstat přihlášeni pro tok uživatele

Povolení políčko zůstat přihlášeni pro tok uživatele:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr**   a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3.  ****   V levém horním rohu Azure Portal vyberte všechny služby a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)**.
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **vlastnosti**.

7. V části  **chování relace** vyberte **Povolit možnost zůstat přihlášeni v relaci**. Vedle pole po **přihlášení k relaci (dny)** zadejte hodnotu od 1 do 90 a zadejte počet dní, po který může relace zůstat otevřená.


   ![Povolit možnost zůstat přihlášeni v relaci](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Uživatelé by tuto možnost neměli na veřejných počítačích povolit.

### <a name="configure-the-page-identifier"></a>Konfigurace identifikátoru stránky

Chcete-li povolit políčko zůstat přihlášeni, nastavte element definice obsahu `DataUri` na [identifikátor stránky](contentdefinitions.md#datauri) `unifiedssp` a [verzi stránky](page-layout.md) *1.1.0* nebo vyšší.

1. Otevřete soubor rozšíření vašich zásad. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> . Tento soubor rozšíření je jedním ze souborů zásad, které jsou součástí úvodní sady Custom Policy Pack, které byste měli mít k dispozici v rámci svých požadavků. Začněte [s vlastními zásadami](custom-policy-get-started.md).
1. Vyhledejte element **BuildingBlocks** . Pokud element neexistuje, přidejte jej.
1. Přidejte element **ContentDefinitions** do elementu **BuildingBlocks** zásady.

    Vaše vlastní zásada by měla vypadat jako následující fragment kódu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Přidejte metadata do technického profilu s vlastním uplatněním.

Chcete-li přidat zaškrtávací políčko políčko zůstat přihlášeni do přihlašovací stránky a přihlašovací stránky, nastavte `setting.enableRememberMe` metadata na hodnotu true. V souboru rozšíření přepište technické profily SelfAsserted-LocalAccountSignin-email.

1. Vyhledejte element ClaimsProviders. Pokud element neexistuje, přidejte jej.
1. Do prvku ClaimsProviders přidejte následující zprostředkovatele deklarací identity:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Uložte soubor rozšíření.

### <a name="configure-a-relying-party-file"></a>Konfigurace souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili. Parametr keepAliveInDays umožňuje nakonfigurovat, jak dlouho by měl uchovávat soubory cookie relace políčko zůstat přihlášeni (Keep on signed on). Pokud například nastavíte hodnotu na 30, pak se soubor cookie relace políčko zůstat přihlášeni uchová po dobu 30 dnů. Rozsah hodnoty je od 1 do 90 dnů.

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do uzlu podřízený uzel `<RelyingParty>` . Musí být umístěn hned po `<DefaultUserJourney ReferenceId="User journey Id" />` , například: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek elementu.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Doporučujeme nastavit hodnotu SessionExpiryInSeconds na krátkou dobu (1200 sekund), zatímco hodnota KeepAliveInDays se dá nastavit na poměrně dlouhou dobu (30 dnů), jak je znázorněno v následujícím příkladu:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Odhlášení

Pokud chcete uživatele podepsat z aplikace, nestačí vymazat soubory cookie aplikace nebo jinak ukončit relaci s uživatelem. Chcete-li se odhlásit, je nutné přesměrovat uživatele na Azure AD B2C. V opačném případě se může stát, že uživatel bude moci znovu ověřit aplikace bez zadání přihlašovacích údajů.

Po žádosti o odhlášení Azure AD B2C:

1. Zruší platnost Azure AD B2C relace na základě souborů cookie.
::: zone pivot="b2c-user-flow"
2. Pokusy o odhlášení od federovaných zprostředkovatelů identity
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Pokusy o odhlášení od federovaných zprostředkovatelů identity:
   - OpenId Connect – Pokud se ve správném koncovém bodu konfigurace poskytovatele identity Určuje `end_session_endpoint` umístění. Požadavek na odhlášení nepředává `id_token_hint` parametr. Pokud tento parametr vyžaduje federovaný zprostředkovatel identity, žádost o odhlášení se nezdaří.
   - OAuth2 – Pokud [metadata zprostředkovatele identity](oauth2-technical-profile.md#metadata) obsahují `end_session_endpoint` umístění.
   - SAML – Pokud [metadata zprostředkovatele identity](identity-provider-generic-saml.md) obsahují `SingleLogoutService` umístění.
4. Volitelně můžete odhlásit z jiných aplikací. Další informace najdete v části věnované [jednotnému odhlašování](#single-sign-out) .

> [!NOTE]
> Odhlášení od federovaných zprostředkovatelů identity můžete zakázat nastavením metadat technického profilu zprostředkovatele identity `SingleLogoutEnabled` na `false` .
::: zone-end

Odhlášení vymaže stav jednotného přihlašování uživatele s Azure AD B2C, ale nemusí uživatele podepsat z relace sociální identity Provider. Pokud uživatel vybere stejného poskytovatele identity během následného přihlášení, může se znovu ověřit bez zadání přihlašovacích údajů. Pokud se uživatel chce z aplikace odhlásit, neznamená to nutně, že se chce odhlásit z účtu Facebook. Pokud se ale použijí místní účty, relace uživatele se ukončí správně.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Jednotné odhlašování 

Když uživatele přesměrujete na koncový bod Azure AD B2Cho odhlašování (pro protokoly OAuth2 i SAML), Azure AD B2C vymaže relaci uživatele z prohlížeče. Uživatel se však může stále přihlašovat k jiným aplikacím, které používají Azure AD B2C pro ověřování. Pokud chcete těmto aplikacím povolit, aby si vypnuli uživatele současně, Azure AD B2C odešle požadavek HTTP GET na zaregistrované `LogoutUrl` všechny aplikace, ke kterým je uživatel aktuálně přihlášený.

Aplikace musí na tuto žádost reagovat vymazáním jakékoli relace, která uživatele identifikuje a vrátí `200` odpověď. Pokud chcete v aplikaci podporovat jednotné přihlašování, musíte implementovat `LogoutUrl` v kódu vaší aplikace. 

Aby bylo možné podporovat jednotné přihlašování, musí technické profily vystavitele tokenů pro tokeny JWT i SAML určovat:

- Název protokolu, například `<Protocol Name="OpenIdConnect" />`
- Odkaz na technický profil relace, například `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

Následující příklad znázorňuje vystavitele tokenů JWT a SAML s jednotným odhlášením:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Zabezpečení přesměrování odhlášení

Po odhlášení se uživatel přesměruje na identifikátor URI zadaný v `post_logout_redirect_uri` parametru, a to bez ohledu na adresy URL odpovědi, které byly pro aplikaci zadané. Pokud je ale `id_token_hint` předána platná a **token vyžadovat ID v žádostech o odhlášení** je zapnutý, Azure AD B2C ověří, že hodnota `post_logout_redirect_uri` odpovídá jednomu z nakonfigurovaných identifikátorů URI pro přesměrování aplikace před provedením přesměrování. Pokud pro aplikaci nebyla nakonfigurovaná žádná odpovídající adresa URL odpovědi, zobrazí se chybová zpráva a uživatel nebude přesměrován. 

::: zone pivot="b2c-user-flow"

Vyžadování tokenu ID v žádostech o odhlášení:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Vlastnosti**.
1. **V žádostech o odhlášení povolte token vyžadovat ID**.
1. Vraťte se na  **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte svou aplikaci.
1. Vyberte **Ověřování**.
1. Do textového pole **odhlašovací adresa URL** zadejte identifikátor URI přesměrování po odhlášení a pak vyberte **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Chcete-li vyžadovat token ID v žádostech o odhlášení, přidejte do elementu [RelyingParty](relyingparty.md) element **UserJourneyBehaviors** . Pak nastavte **EnforceIdTokenHintOnLogout** elementu **SingleSignon** na `true` . Váš element **UserJourneyBehaviors** by měl vypadat jako v tomto příkladu:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Konfigurace adresy URL pro odhlášení aplikace:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte svou aplikaci.
1. Vyberte **Ověřování**.
1. Do textového pole **odhlašovací adresa URL** zadejte identifikátor URI přesměrování po odhlášení a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- Naučte se [Konfigurovat tokeny v Azure AD B2C](configure-tokens.md).
