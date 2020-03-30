---
title: Zabezpečení služby Restful ve vašem Azure AD B2C
titleSuffix: Azure AD B2C
description: Zabezpečte své vlastní renomované rozhraní REST API deklarace identity ve vašem Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: aa2e2fb4eb6e269f45494db6d87eef40182971a2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346931"
---
# <a name="secure-your-restful-services"></a>Zabezpečte své služby RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Při integraci rozhraní REST API v rámci cesty uživatele Azure AD B2C, musíte chránit koncový bod rozhraní REST API s ověřováním. Tím je zajištěno, že pouze služby, které mají správné přihlašovací údaje, jako je například Azure AD B2C, můžete volat do koncového bodu rozhraní REST API.

Zjistěte, jak integrovat rozhraní REST API v rámci cesty uživatele Azure AD B2C v [ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md) a přidat [renominy deklarací rozhraní REST API do článků vlastních zásad.](custom-policy-rest-api-claims-exchange.md)

Tento článek se bude zabývat tím, jak zabezpečit rozhraní REST API pomocí základního protokolu HTTP, klientského certifikátu nebo ověřování OAuth2. 

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v jednom z následujících příruček "Jak na to":

- [Integrujte renovované deklarace rozhraní REST API v cestě uživatele Azure AD B2C k ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md).
- [Přidání výměny deklarací rozhraní REST API do vlastních zásad](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Základní ověřování http

Základní ověřování HTTP je definováno v [jazyce RFC 2617](https://tools.ietf.org/html/rfc2617). Základní ověřování funguje takto: Azure AD B2C odešle požadavek HTTP s pověřeními klienta v hlavičce autorizace. Pověření jsou formátována jako base64 kódovaný řetězec "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Přidání uživatelských jmen a zásad zásad rozhraní REST API a hesla

Chcete-li nakonfigurovat technický profil rozhraní REST API pomocí základního ověřování HTTP, vytvořte následující kryptografické klíče pro uložení uživatelského jména a hesla:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + předplatné** a zvolte adresář Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad**a pak vyberte **Přidat**.
1. V **části Možnosti**vyberte **možnost Ručně**.
1. Do **pole Název**zadejte Příkaz **RestApiUsername**.
    Předpona *B2C_1A_* může být přidána automaticky.
1. Do pole **Tajné** zadejte uživatelské jméno rozhraní REST API.
1. V **případě použití klíče**vyberte možnost **Šifrování**.
1. Vyberte **Vytvořit**.
1. Znovu vyberte **klávesy zásad.**
1. Vyberte **Přidat**.
1. V **části Možnosti**vyberte **možnost Ručně**.
1. Do **pole Název**zadejte příkaz **RestApiPassword**.
    Předpona *B2C_1A_* může být přidána automaticky.
1. Do pole **Tajné** zadejte heslo rozhraní REST API.
1. V **případě použití klíče**vyberte možnost **Šifrování**.
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurace technického profilu rozhraní REST API pro použití základního ověřování http

Po vytvoření potřebných klíčů nakonfigurujte metadata technického profilu rozhraní REST API tak, aby odkazovala na pověření.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte technický profil rozhraní REST API. Například `REST-ValidateProfile`, `REST-GetProfile`nebo .
1. Vyhledejte element `<Metadata>`.
1. Změňte *typ* `Basic`ověřování na .
1. Změňte *AllowInsecureAuthInProduction* na `false`.
1. Ihned za `</Metadata>` uzavírací prvek přidejte následující úryvek XML:
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

## <a name="https-client-certificate-authentication"></a>Ověřování klientského certifikátu HTTPS

Ověřování klientského certifikátu je vzájemné ověřování založené na certifikátech, kde klient Azure AD B2C poskytuje serveru svůj klientský certifikát k prokázání jeho identity. K tomu dochází jako součást handshake SSL. K vaší službě rozhraní REST API mají přístup pouze služby, které mají správné certifikáty, jako je azure ad B2C. Klientský certifikát je digitální certifikát X.509. V produkčním prostředí musí být podepsána certifikační autoritou.

### <a name="prepare-a-self-signed-certificate-optional"></a>Příprava certifikátu podepsaného svým držitelem (nepovinné)

Pokud v neprodukčních prostředích ještě nemáte certifikát, můžete použít certifikát podepsaný svým držitelem. V systému Windows můžete ke generování certifikátu použít rutinu [New-SelfSignedCertificate aplikace](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) PowerShell.

1. Spusťte tento příkaz prostředí PowerShell a vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a název klienta Azure AD B2C. Můžete také upravit `-NotAfter` datum a určit pro certifikát jiné vypršení platnosti.
    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Otevřete **možnost Spravovat uživatelské certifikáty** > aktuální**osobní** > **certifikáty** > **uživatelů** > *yourappname.yourtenant.onmicrosoft.com*.
1. Vyberte certifikát > **akce** > **Export všech úkolů** > **.**
1. Vyberte **Ano** > **Další** > **Ano, exportujte soukromý klíč** > **Další**.
1. Přijměte výchozí hodnoty pro **export formátu souboru**.
1. Zadejte heslo pro certifikát.

### <a name="add-a-client-certificate-policy-key"></a>Přidání klíče zásad klientského certifikátu

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + předplatné** a zvolte adresář Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad**a pak vyberte **Přidat**.
1. V poli **Možnosti** vyberte **Nahrát**.
1. Do pole **Název** zadejte **RestApiClientCertificate**.
    Předpona *B2C_1A_* se přidá automaticky.
1. V poli **Nahrání souboru** vyberte soubor .pfx certifikátu se soukromým klíčem.
1. Do pole **Heslo** zadejte heslo certifikátu.
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurace technického profilu rozhraní REST API pro použití ověřování klientského certifikátu

Po vytvoření potřebného klíče nakonfigurujte metadata technického profilu rozhraní REST API tak, aby odkazovala na klientský certifikát.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte technický profil rozhraní REST API. Například `REST-ValidateProfile`, `REST-GetProfile`nebo .
1. Vyhledejte element `<Metadata>`.
1. Změňte *typ* `ClientCertificate`ověřování na .
1. Změňte *AllowInsecureAuthInProduction* na `false`.
1. Ihned za `</Metadata>` uzavírací prvek přidejte následující úryvek XML:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Následuje příklad technického profilu RESTful nakonfigurovaného pomocí klientského certifikátu HTTP:

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

Ověřování nosné tokenu je definováno v [autorizačním rámci OAuth2.0: Použití žetonu nosné žetony (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) Při ověřování nože tokenazure AD B2C odešle požadavek HTTP s tokenem v záhlaví autorizace.

```http
Authorization: Bearer <token>
```

Nosný token je neprůhledný řetězec. Může se jedná o přístupový token JWT nebo libovolný řetězec, který rozhraní REST API očekává, že azure ad B2C k odeslání v záhlaví autorizace. Azure AD B2C podporuje následující typy:

- **Nosné znamení**. Aby bylo možné odeslat žeton nosiče v klidném technickém profilu, vaše zásady musí nejprve získat žeton nosiče a pak jej použít v technickém profilu RESTful.  
- **Statický nosný token**. Tento přístup použijte, když vaše rozhraní REST API vydává token dlouhodobého přístupu. Chcete-li použít statický nosný token, vytvořte klíč zásad y a vytvořte odkaz z technického profilu RESTful na klíč zásad. 

## <a name="using-oauth2-bearer"></a>Použití Nosiče OAuth2  

Následující kroky ukazují, jak pomocí pověření klienta získat token nosiče a předat jej do hlavičky autorizace volání rozhraní REST API.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definovat deklaraci pro uložení tokenu nosiče

Deklarace poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma nároků](claimsschema.md) je místo, kde deklarujete své nároky. Přístupový token musí být uložen v deklaraci, která bude použita později. 

1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Vyhledejte element [BuildingBlocks.](buildingblocks.md) Pokud prvek neexistuje, přidejte jej.
1. Vyhledejte [ClaimsSchema](claimsschema.md) element. Pokud prvek neexistuje, přidejte jej.
1. Přidejte město bearerToken do **ClaimsSchema** element.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Získání přístupového tokenu 

Přístupový token můžete získat jedním z několika způsobů: získáním [od poskytovatele federované identity](idp-pass-through-custom.md), voláním rozhraní REST API, které vrací přístupový token, pomocí [toku ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)nebo pomocí [toku pověření klienta](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow).  

Následující příklad používá technický profil rozhraní REST API k vytvoření požadavku na koncový bod tokenu Azure AD pomocí pověření klienta předaných jako základní ověřování HTTP. Chcete-li to nakonfigurovat ve službě Azure AD, najdete [v tématu platformy identit Microsoftu a toku přihlašovacích údajů klienta OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Možná budete muset upravit tak, aby rozhraní s poskytovatelem identity. 

Pro ServiceUrl nahraďte název tenanta názvem vašeho klienta Azure AD. Všechny dostupné možnosti naleznete v odkazu [na technický profil RESTful.](restful-technical-profile.md)

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Změna technického profilu REST na použití ověřování nože tokenu

Chcete-li podporovat ověřování nože tokenu ve vlastní zásady, upravte rozhraní REST API technický profil s následujícími:

1. V pracovním adresáři otevřete soubor zásad rozšíření *TrustFrameworkExtensions.xml.*
1. Vyhledejte `<TechnicalProfile>` uzel, který `Id="REST-API-SignUp"`obsahuje .
1. Vyhledejte element `<Metadata>`.
1. Změňte *Typ authenticationType* na *nosiče*takto:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Změňte nebo přidejte *UseClaimAsBearerToken* na *bearerToken*, takto. *BearerToken* je název deklarace, ze které bude načten token nosiče `SecureREST-AccessToken`(výstupní deklarace z).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Ujistěte se, že přidáte výše použitou deklaraci jako vstupní deklaraci:

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

Po přidání výše uvedených výstřižků by měl váš technický profil vypadat jako následující kód XML:

```XML
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
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Použití statického nosiče OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Přidání klíče zásad tokenu nosné doručitele OAuth2

Vytvořte klíč zásad pro uložení hodnoty tokenu nosiče.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + předplatné** a zvolte adresář Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad**a pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Zadejte **název** klíče zásady. Například, `RestApiBearerToken`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
1. V **poli Tajné**zadejte tajný klíč klienta, který jste dříve zaznamenali.
1. V případě použití `Encryption` **klíče**vyberte .
1. Vyberte **Vytvořit**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurace technického profilu rozhraní REST API pro použití klíče zásad tokenu nosiče

Po vytvoření potřebného klíče nakonfigurujte metadata technického profilu rozhraní REST API tak, aby odkazovala na token nosiče.

1. V pracovním adresáři otevřete soubor zásad rozšíření (TrustFrameworkExtensions.xml).
1. Vyhledejte technický profil rozhraní REST API. Například `REST-ValidateProfile`, `REST-GetProfile`nebo .
1. Vyhledejte element `<Metadata>`.
1. Změňte *typ* `Bearer`ověřování na .
1. Změňte *AllowInsecureAuthInProduction* na `false`.
1. Ihned za `</Metadata>` uzavírací prvek přidejte následující úryvek XML:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Následuje příklad technického profilu RESTful nakonfigurovaného s ověřováním nože:

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

## <a name="next-steps"></a>Další kroky

- Další informace o prvku [klidového technického profilu](restful-technical-profile.md) naleznete v odkazu ief. 
