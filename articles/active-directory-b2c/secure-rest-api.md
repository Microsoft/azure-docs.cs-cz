---
title: Zabezpečení služby RESTful v Azure AD B2C
titleSuffix: Azure AD B2C
description: Zabezpečte svoje vlastní REST APIé výměny deklarací identity v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462d69a8bde0dec2689ac30620276b5bcd335410
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717688"
---
# <a name="secure-your-restful-services"></a>Zabezpečení služeb RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Při integraci REST API v rámci cesty Azure AD B2C uživatele musíte chránit svůj REST API koncový bod pomocí ověřování. Tím se zajistí, že se do vašeho koncového bodu REST API můžou volat jenom služby, které mají správné přihlašovací údaje, třeba Azure AD B2C.

Přečtěte si, jak integrovat REST API v rámci cesty Azure AD B2C uživatele v článku [ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md) a [Přidání REST APIch výměn deklarací identity do vlastních zásad](custom-policy-rest-api-claims-exchange.md) .

Tento článek vás seznámí s postupem zabezpečení REST API pomocí protokolu HTTP Basic, klientského certifikátu nebo ověřování OAuth2. 

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v jednom z následujících pokynů:

- [Integrujte REST API výměn deklarací identity v cestě uživatele Azure AD B2C a ověřte vstup uživatele](custom-policy-rest-api-claims-validation.md).
- [Přidání výměn deklarací identity REST API do vlastních zásad](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Základní ověřování HTTP

Základní ověřování HTTP je definované v [dokumentu RFC 2617](https://tools.ietf.org/html/rfc2617). Základní ověřování funguje takto: Azure AD B2C odešle požadavek HTTP s přihlašovacími údaji klienta v autorizační hlavičce. Přihlašovací údaje jsou formátovány jako řetězec s kódováním base64 "Name: password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Přidání klíčů REST API uživatelské jméno a zásady hesel

Pokud chcete nakonfigurovat REST API technický profil s ověřováním HTTP Basic, vytvořte si uživatelské jméno a heslo pomocí následujících kryptografických klíčů:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Jako **název** zadejte **RestApiUsername**.
    *B2C_1A_* předpony je možné přidat automaticky.
1. Do pole **tajný kód** zadejte REST API uživatelské jméno.
1. V případě **použití klíče** vyberte **šifrování**.
1. Vyberte **Vytvořit**.
1. Znovu vyberte **klíče zásad** .
1. Vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Jako **název** zadejte **RestApiPassword**.
    *B2C_1A_* předpony je možné přidat automaticky.
1. Do pole **tajný kód** zadejte REST API heslo.
1. V případě **použití klíče** vyberte **šifrování**.
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurace technického profilu REST API pro použití základního ověřování HTTP

Po vytvoření potřebných klíčů Nakonfigurujte metadata profilu REST API Technical profil, aby odkazovala na přihlašovací údaje.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte REST API Technical Profile. Například `REST-ValidateProfile` nebo `REST-GetProfile` .
1. Vyhledejte element `<Metadata>`.
1. Změňte *AuthenticationType* na `Basic` .
1. Změňte *AllowInsecureAuthInProduction* na `false` .
1. Hned za uzavírací `</Metadata>` element přidejte následující fragment kódu XML:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Následuje příklad technického profilu RESTful nakonfigurovaného pomocí základního ověřování HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Ověřování klientským certifikátem HTTPS

Ověřování klientským certifikátem je vzájemné ověřování založené na certifikátech, ve kterém klient Azure AD B2C poskytuje svůj klientský certifikát serveru k prokázání jeho identity. K tomu dochází jako součást metody handshake protokolu SSL. Ke službě REST API mají přístup jenom služby, které mají správné certifikáty, například Azure AD B2C. Certifikát klienta je digitální certifikát X. 509. V produkčním prostředí musí být certifikační autoritou podepsaná.

### <a name="prepare-a-self-signed-certificate-optional"></a>Příprava certifikátu podepsaného svým držitelem (volitelné)

V případě neprodukčních prostředí můžete použít certifikát podepsaný svým držitelem (samo-signed certificate) i v případě, že ještě nemáte certifikát. Ve Windows můžete k vygenerování certifikátu použít rutinu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) prostředí PowerShell.

1. Spuštěním tohoto příkazu PowerShellu Vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a Azure AD B2C název tenanta. Můžete také upravit `-NotAfter` Datum a zadat jiné vypršení platnosti certifikátu.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Otevřete **Spravovat uživatelské certifikáty**  >  **aktuální uživatelé**  >  **osobní**  >  **certifikáty**  >  *YourAppName.yourtenant.onmicrosoft.com*.
1. Vyberte certifikát > **akci**  >  **všechny úkoly**  >  **exportovat**.
1. Vyberte **Ano**  >    >  **, další Ano, exportovat privátní klíč**  >  .
1. Přijměte výchozí hodnoty pro **Formát souboru pro export**.
1. Zadejte heslo pro certifikát.

### <a name="add-a-client-certificate-policy-key"></a>Přidat klíč zásad certifikátu klienta

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V poli **Možnosti** vyberte **Odeslat**.
1. Do pole **název** zadejte **RestApiClientCertificate**.
    *B2C_1A_* předpony se přidají automaticky.
1. V poli **nahrávání souboru** vyberte soubor. pfx vašeho certifikátu s privátním klíčem.
1. Do pole **heslo** zadejte heslo certifikátu.
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurace technického profilu REST API pro použití ověřování klientským certifikátem

Po vytvoření potřebného klíče nakonfigurujte REST API metadata technického profilu tak, aby odkazovala na klientský certifikát.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte REST API Technical Profile. Například `REST-ValidateProfile` nebo `REST-GetProfile` .
1. Vyhledejte element `<Metadata>`.
1. Změňte *AuthenticationType* na `ClientCertificate` .
1. Změňte *AllowInsecureAuthInProduction* na `false` .
1. Hned za uzavírací `</Metadata>` element přidejte následující fragment kódu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

V následujícím příkladu je RESTful technický profil nakonfigurovaný pomocí klientského certifikátu HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Ověřování nosiče OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Ověřování nosných tokenů je definováno v [autorizačním rozhraní OAuth 2.0: použití nosných tokenů (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). V případě ověřování nosných tokenů Azure AD B2C odešle požadavek HTTP s tokenem v autorizační hlavičce.

```http
Authorization: Bearer <token>
```

Nosný token je neprůhledný řetězec. Může se jednat o přístupový token JWT nebo libovolný řetězec, který REST API očekává Azure AD B2C odeslání v autorizační hlavičce. Azure AD B2C podporuje následující typy:

- **Nosný token** Aby bylo možné poslat token nosiče v RESTful Technical Profile, musí vaše zásada nejdřív získat nosný token a pak ho použít v technickém profilu RESTful.  
- **Statický nosný token**. Tento postup použijte, když REST API vydá dlouhodobý přístupový token. Pokud chcete použít statický nosný token, vytvořte klíč zásady a vytvořte odkaz z RESTful Technical Profile na svůj klíč zásad. 


## <a name="using-oauth2-bearer"></a>Používání nosiče OAuth2  

Následující kroky ukazují, jak pomocí přihlašovacích údajů klienta získat nosný token a předat ho do autorizační hlavičky REST API volání.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definování deklarace identity pro uložení nosného tokenu

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma deklarací identity](claimsschema.md) je místo, kde deklarujete deklarace identity. Přístupový token musí být uložený v deklaraci, která se má použít později. 

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Do prvku **ClaimsSchema** přidejte následující deklarace identity.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Získání přístupového tokenu 

Přístupový token můžete získat jedním z několika způsobů: získáním [od poskytovatele federované identity](idp-pass-through-user-flow.md)voláním REST API, která vrací přístupový token, pomocí [toku ROPC](../active-directory/develop/v2-oauth-ropc.md)nebo pomocí [toku přihlašovacích údajů klienta](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Tok přihlašovacích údajů klienta se běžně používá pro interakce mezi servery, které musí běžet na pozadí bez okamžité interakce s uživatelem.

#### <a name="acquiring-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD 

Následující příklad používá REST API technický profil k vytvoření požadavku na koncový bod tokenu Azure AD pomocí přihlašovacích údajů klienta předaných jako základní ověřování HTTP. Další informace najdete v tématu [tok přihlašovacích údajů klienta Microsoft Identity Platform a OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). 

Pokud chcete získat přístupový token Azure AD, vytvořte v tenantovi Azure AD aplikaci:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V nabídce vlevo vyberte **Azure Active Directory**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *Client_Credentials_Auth_app*.
1. V části **podporované typy účtů** vyberte **účty jenom v tomto organizačním adresáři**.
1. Vyberte **Zaregistrovat**.
2. Poznamenejte si **ID aplikace (klienta)**. 


Pro tok přihlašovacích údajů klienta je potřeba vytvořit tajný klíč aplikace. Tajný kód klienta je také označován jako heslo aplikace. Pro získání přístupového tokenu bude vaše aplikace používat tajný klíč.

1. Na stránce **Azure AD B2C-registrace aplikací** vyberte aplikaci, kterou jste vytvořili, například *Client_Credentials_Auth_app*.
1. V nabídce vlevo v části **Spravovat** vyberte **certifikáty & tajných** kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Do pole **Popis** zadejte popis tajného klíče klienta. Například *clientsecret1*.
1. Pod položkou **konec platnosti** vyberte dobu, po kterou je tajný kód platný, a pak vyberte **Přidat**.
1. Poznamenejte si **hodnotu** tajného klíče pro použití v kódu klientské aplikace. Tato hodnota tajného klíče se po opuštění této stránky už znovu nezobrazí. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Vytvoření klíčů zásad Azure AD B2C

Je potřeba uložit ID klienta a tajný klíč klienta, které jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad `SecureRESTClientId` . Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče** zadejte ID klienta, které jste si dříve nahráli.
9. Pro **použití klíče** vyberte `Signature` .
10. Klikněte na **Vytvořit**.
11. Vytvořte další klíč zásad s následujícím nastavením:
    -   **Název**: `SecureRESTClientSecret` .
    -   **Tajný kód**: Zadejte svůj tajný klíč klienta, který jste nahráli dříve.

Pro ServiceUrl nahraďte název-tenanta názvem vašeho tenanta Azure AD. Všechny dostupné možnosti najdete v tématu [RESTful Technical Profile](restful-technical-profile.md) reference.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Změna technického profilu REST na používání ověřování pomocí nosných tokenů

Pokud chcete ve vlastních zásadách podporovat ověřování nosných tokenů, upravte REST API technický profil následujícím způsobem:

1. V pracovním adresáři otevřete soubor zásad rozšíření *TrustFrameworkExtensions.xml* .
1. Vyhledejte `<TechnicalProfile>` uzel, který obsahuje `Id="REST-API-SignUp"` .
1. Vyhledejte element `<Metadata>`.
1. Změňte *AuthenticationType* na *nosiče* následujícím způsobem:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Změňte nebo přidejte *UseClaimAsBearerToken* do *bearerToken*, a to následujícím způsobem. *BearerToken* je název deklarace identity, ze které se načte token nosiče (výstupní deklarace identity z `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Ujistěte se, že jste přidali deklaraci, kterou jste použili výše jako vstupní deklaraci identity:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Po přidání výše uvedených fragmentů kódu by váš technický profil měl vypadat jako v následujícím kódu XML:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Použití statického nosiče OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Přidat klíč zásad OAuth2 Bearer tokenu

Pokud chcete nakonfigurovat REST API technický profil s nosným tokenem OAuth2, Získejte přístupový token od vlastníka REST API. Pak vytvořte následující kryptografický klíč pro uložení nosného tokenu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Manual` .
1. Zadejte **název** klíče zásad. Například, `RestApiBearerToken`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do **tajného klíče** zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **použití klíče** vyberte `Encryption` .
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Nakonfigurujte svůj REST API technický profil pro použití klíče zásad nosných tokenů.

Po vytvoření potřebného klíče nakonfigurujte REST API metadata technického profilu tak, aby odkazovala na token nosiče.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte REST API Technical Profile. Například `REST-ValidateProfile` nebo `REST-GetProfile` .
1. Vyhledejte element `<Metadata>`.
1. Změňte *AuthenticationType* na `Bearer` .
1. Změňte *AllowInsecureAuthInProduction* na `false` .
1. Hned za uzavírací `</Metadata>` element přidejte následující fragment kódu XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Tady je příklad technického profilu RESTful s nakonfigurovaným ověřováním pomocí nosných tokenů:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>Ověřování klíčů rozhraní API

Klíč rozhraní API je jedinečný identifikátor, který slouží k ověření uživatele pro přístup ke koncovému bodu REST API. Klíč se odešle ve vlastní hlavičce protokolu HTTP. Například [aktivační událost Azure FUNCTIONS http](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) používá `x-functions-key` k identifikaci žadatele hlavičku HTTP.  

### <a name="add-api-key-policy-keys"></a>Přidat klíče zásad pro klíč rozhraní API

Pokud chcete nakonfigurovat REST API technický profil s ověřováním pomocí klíče rozhraní API, vytvořte následující kryptografický klíč pro uložení klíče rozhraní API:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Jako **název** zadejte **RestApiKey**.
    *B2C_1A_* předpony je možné přidat automaticky.
1. Do pole **tajný kód** zadejte REST API klíč.
1. V případě **použití klíče** vyberte **šifrování**.
1. Vyberte **Vytvořit**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurace technického profilu REST API pro použití ověřování pomocí klíče rozhraní API

Po vytvoření potřebného klíče nakonfigurujte REST API metadata technického profilu, aby odkazovala na přihlašovací údaje.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte REST API Technical Profile. Například `REST-ValidateProfile` nebo `REST-GetProfile` .
1. Vyhledejte element `<Metadata>`.
1. Změňte *AuthenticationType* na `ApiKeyHeader` .
1. Změňte *AllowInsecureAuthInProduction* na `false` .
1. Hned za uzavírací `</Metadata>` element přidejte následující fragment kódu XML:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

**ID** kryptografického klíče definuje hlavičku protokolu HTTP. V tomto příkladu se klíč rozhraní API posílá jako **klíč x-Functions**.

V následujícím příkladu je RESTful technický profil nakonfigurovaný pro volání funkce Azure s ověřováním klíče rozhraní API:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Další kroky

- Další informace o elementu [RESTful Technical Profile](restful-technical-profile.md) najdete v referenčních informacích k IEF.
