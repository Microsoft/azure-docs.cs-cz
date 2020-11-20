---
title: Definování technického profilu s pomocným tokenem ID v vlastní zásadě
titleSuffix: Azure AD B2C
description: Definujte technický profil s pomocným tokenem ID pro vlastní zásadu v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 79a99d9f0ca117d8f47d56d76399210a72b91bb7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951651"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definice technického profilu pomocného tokenu ID v Azure Active Directory B2C vlastní zásady

Azure AD B2C umožňuje aplikacím předávající strany Odeslat příchozí token JWT v rámci žádosti o autorizaci OAuth2. Token JWT může být vydán aplikací předávající strany nebo poskytovatelem identity a může předat nápovědu týkající se uživatele nebo žádosti o autorizaci. Azure AD B2C ověří podpis, název vystavitele a cílovou skupinu tokenů a extrahuje deklaraci identity z příchozího tokenu.

## <a name="use-cases"></a>Případy použití

Pomocí tohoto řešení můžete odesílat data do Azure AD B2C zapouzdřená v jednom tokenu JWT. [Registrace pomocí řešení pozvání k e-mailu](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), kde může správce systému poslat uživatelům podepsané pozvánky, je založený na id_token_hint. Jenom uživatelé s přístupem k e-mailu s pozvánkou můžou vytvořit účet v adresáři.

## <a name="token-signing-approach"></a>Přístup k podepisování tokenů

Pomocí id_token_hint Vystavitel tokenu (aplikace předávající strany nebo poskytovatel identity) vytvoří token a pak ho podepíše pomocí podpisového klíče, který potvrdí, že token pochází z důvěryhodného zdroje. Podpisový klíč může být symetrický nebo asymetrický. Symetrická kryptografie nebo kryptografie s privátním klíčem používá sdílený tajný klíč k podepisování a ověřování podpisu. Asymetrická kryptografie nebo kryptografie s veřejným klíčem jsou šifrovací systém, který používá privátní klíč a veřejný klíč. Privátní klíč je známý pouze pro vystavitele tokenu a používá se k podpisu tokenu. Veřejný klíč se sdílí se zásadami Azure AD B2C k ověření podpisu tokenu.

## <a name="token-format"></a>Formát tokenu

Id_token_hint musí být platný token JWT. V následující tabulce jsou uvedeny deklarace identity, které jsou povinné. Další deklarace identity jsou volitelné.

| Name | Deklarovat | Příklad hodnoty | Popis |
| ---- | ----- | ------------- | ----------- |
| Cílová skupina | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifikuje zamýšleného příjemce tokenu. Toto je libovolný řetězec definovaný vystavitelem tokenu. Azure AD B2C ověří tuto hodnotu a odmítne token, pokud se neshoduje.  |
| Vystavitel | `iss` |`https://localhost` | Identifikuje službu tokenu zabezpečení (Vystavitel tokenů). Toto je libovolný identifikátor URI definovaný vystavitelem tokenu. Azure AD B2C ověří tuto hodnotu a odmítne token, pokud se neshoduje.  |
| Čas vypršení platnosti | `exp` | `1600087315` | Čas, kdy se token stal neplatným, reprezentovaný v epocha času. Azure AD B2C neověřuje tuto deklaraci identity. |
| Ne před | `nbf` | `1599482515` | Čas, kdy bude token platný, reprezentovaný v epocha času. Tato doba je obvykle stejná jako čas, kdy byl token vydán. Azure AD B2C neověřuje tuto deklaraci identity. |

 Následující token je příkladem platného tokenu ID:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `None` . Například protokol pro **IdTokenHint_ExtractClaims** Technical profil je `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Technický profil se volá z kroku orchestrace s typem `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací, které se mají extrahovat z tokenu JWT. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v tokenu JWT. Můžete také zahrnout deklarace identity, které nejsou vráceny tokenem JWT, pokud nastavíte `DefaultValue` atribut.

## <a name="metadata"></a>Metadata

Následující metadata jsou relevantní při použití symetrického klíče. 

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| issuer | Yes | Identifikuje službu tokenu zabezpečení (Vystavitel tokenů). Tato hodnota musí být shodná s `iss` deklarací identity v rámci deklarace tokenu JWT. | 
| IdTokenAudience | Yes | Identifikuje zamýšleného příjemce tokenu. Musí být identické `aud` s deklarací identity s deklarací tokenu JWT. | 

Následující metadata jsou relevantní při použití asymetrického klíče. 

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| MEZIPAMĚŤ| Yes | Adresa URL, která odkazuje na dokument konfigurace vystavitele tokenu, který je také známý jako OpenID dobře známý koncový bod konfigurace.   |
| issuer | Ne | Identifikuje službu tokenu zabezpečení (Vystavitel tokenů). Tato hodnota se dá použít k přepsání hodnoty nakonfigurované v metadatech a musí být identická s `iss` deklarací identity v rámci deklarace tokenu JWT. |  
| IdTokenAudience | Ne | Identifikuje zamýšleného příjemce tokenu. Musí být identické `aud` s deklarací identity s deklarací tokenu JWT. |  

## <a name="cryptographic-keys"></a>Kryptografické klíče

Při použití symetrického klíče obsahuje element **CryptographicKeys** následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| client_secret | Yes | Kryptografický klíč, který se používá k ověření podpisu tokenu JWT.|


## <a name="how-to-guide"></a>Praktičtí průvodci

### <a name="issue-a-token-with-symmetric-keys"></a>Vystavení tokenu pomocí symetrických klíčů

#### <a name="step-1-create-a-shared-key"></a>Krok 1. Vytvoření sdíleného klíče 

Vytvořte klíč, který se dá použít k podepsání tokenu. Použijte například následující kód PowerShellu pro vygenerování klíče.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Tento kód vytvoří tajný řetězec jako `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Krok 2. Přidejte podpisový klíč pro Azure AD B2C

Stejný klíč, který používá Vydavatel tokenů, musí být vytvořený v klíčích zásad Azure AD B2C.  

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled v části **zásady** vyberte **Architektura prostředí identity**.
1. Vybrat **klíče zásad** 
1. Vyberte **ručně**.
1. Pro **název** použijte `IdTokenHintKey` .  
   Předponu `B2C_1A_` lze přidat automaticky.
1. Do pole **tajný kód** zadejte klíč pro přihlášení, který jste předtím vygenerovali.
1. Pro **použití klíče** použijte **šifrování**.
1. Vyberte **Vytvořit**.
1. Potvrďte, že jste vytvořili klíč `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Krok 3. Přidejte technický profil pro token ID.

Následující technický profil ověří token a extrahuje deklarace. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Krok 4: Příprava zásad

Dokončete krok [Konfigurace zásad](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Krok 5. Příprava kódu  

[Ukázka GitHubu](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) je webová aplikace ASP.NET a Konzolová aplikace, která GENERUJE token ID, který je podepsaný pomocí symetrického klíče. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Vydání tokenu pomocí asymetrických klíčů

Pomocí asymetrického klíče je token podepsaný pomocí certifikátů RSA. Tato aplikace je hostitelem otevřeného koncového bodu metadat Connect metadata a koncového bodu JWKs (JSON web Keys), který je používán Azure AD B2C k ověření podpisu tokenu ID.

Vystavitel tokenu musí poskytovat následující koncové body:

* `/.well-known/openid-configuration` – Dobře známý koncový bod konfigurace se souvisejícími informacemi o tokenu, jako je název vystavitele tokenu a odkaz na koncový bod JWK. 
* `/.well-known/keys` – koncový bod JSON web Key (JWK) s veřejným klíčem, který se používá k podepsání klíče (s částí certifikátu privátního klíče).

Podívejte se na ukázku kontroleru [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Krok 1. Příprava certifikátu podepsaného svým držitelem

Pokud ještě certifikát nemáte, můžete k tomuto průvodci použít certifikát podepsaný svým držitelem. Ve Windows můžete k vygenerování certifikátu použít rutinu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) prostředí PowerShell.

Spuštěním tohoto příkazu PowerShellu Vygenerujte certifikát podepsaný svým držitelem. Upravte `-Subject` argument podle potřeby pro vaši aplikaci a Azure AD B2C název tenanta. Můžete také upravit `-NotAfter` Datum a zadat jiné vypršení platnosti certifikátu.

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


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Krok 2. Přidejte technický profil pro token ID. 

Následující technický profil ověří token a extrahuje deklarace. Změňte identifikátor URI metadat na svůj známý koncový bod konfigurace vystavitele tokenu.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Krok 3. Příprava zásad

Dokončete krok [Konfigurace zásad](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Krok 4: Příprava kódu 

Tato [ukázková](https://github.com/azure-ad-b2c/id-token-builder) webová aplikace pro GitHub ASP.NET generuje tokeny ID a hostuje koncové body metadat vyžadované k použití parametru "id_token_hint" v Azure AD B2C.


### <a name="configure-your-policy"></a>Konfigurace zásad

V případě symetrických i asymetrických přístupů `id_token_hint` je technický profil volán z kroku orchestrace s typem `GetClaims` a musí určovat vstupní deklarace zásad předávající strany.

1. Přidejte profil IdTokenHint_ExtractClaims Technical do zásad rozšíření.
1. Jako první položku přidejte na cestu uživatele následující krok orchestrace.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. V zásadách předávající strany opakujte stejné vstupní deklarace identity, které jste nakonfigurovali v IdTokenHint_ExtractClaims Technical Profile. Například:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

V závislosti na potřebách vaší firmy možná budete muset přidat ověření tokenů, například vypršení platnosti tokenu, formát e-mailové adresy a další. Uděláte to tak, že přidáte kroky orchestrace, které vyvolají [technický profil transformace deklarací identity](claims-transformation-technical-profile.md). Pokud chcete zobrazit chybovou zprávu, přidejte také [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) . 

### <a name="create-and-sign-a-token"></a>Vytvoření a podepsání tokenu

Ukázky na GitHubu ukazují, jak vytvořit takový token, který vydává token JWT, který se později poslal jako `id_token_hint` parametr řetězce dotazu. Následuje příklad žádosti o autorizaci s parametrem id_token_hint.
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Další kroky

- Podívejte se na [registraci v e-mailovém řešení pozvat](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) na úložiště GitHub komunity Azure AD B2C.