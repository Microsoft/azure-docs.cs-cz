---
title: Konfigurace možností poskytovatele služby SAML
title-suffix: Azure Active Directory B2C
description: Postup konfigurace Azure Active Directory B2C možností poskytovatele služeb SAML
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b9a491b639cd1b960ffe3b7164a0940770792148
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107389"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Možnosti registrace aplikace SAML v Azure AD B2C

Tento článek popisuje možnosti konfigurace, které jsou k dispozici při připojování Azure Active Directory (Azure AD B2C) s aplikací SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Šifrované kontrolní výrazy SAML

Pokud vaše aplikace očekává, že se kontrolní výrazy SAML nacházejí v šifrovaném formátu, je nutné zajistit, aby byla v zásadách Azure AD B2C povolena možnost šifrování.

Azure AD B2C používá certifikát veřejného klíče poskytovatele služby k zašifrování kontrolního výrazu SAML. V koncovém bodu metadat aplikace SAML musí být veřejný klíč, s možností použít popisovač klíče nastavenou na hodnotu Encryption, jak je znázorněno v následujícím příkladu:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Pokud chcete povolit Azure AD B2C odesílat šifrované kontrolní výrazy, nastavte položku metadat **WantsEncryptedAssertion** na `true` [technický profil předávající strany](relyingparty.md#technicalprofile). Můžete také nakonfigurovat algoritmus použitý k zašifrování kontrolního výrazu SAML.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Tok iniciované zprostředkovatelem identity

Pokud aplikace očekává přijetí kontrolního výrazu SAML bez předchozího odeslání požadavku SAML AuthN poskytovateli identity, je nutné nakonfigurovat Azure AD B2C pro tok iniciované zprostředkovatelem identity.

V toku iniciované zprostředkovatelem identity se proces přihlášení iniciuje zprostředkovatelem identity (Azure AD B2C), který poskytovateli služby (vaší aplikaci předávající strany) pošle nevyžádaný odpověď SAML.

V současné době nepodporujeme scénáře, ve kterých se zahajuje poskytovatel identity jedná o externího zprostředkovatele identity federovaného s Azure AD B2C, například [AD-FS](identity-provider-adfs.md)nebo [Salesforce](identity-provider-salesforce-saml.md). Podporuje se jenom Azure AD B2C ověřování místního účtu.

Pokud chcete povolit tok iniciované zprostředkovatelem identity,  nastavte `true` v [technickém profilu předávající strany](relyingparty.md#technicalprofile)položku metadat IdpInitiatedProfileEnabled.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Pokud se chcete přihlásit nebo zaregistrovat uživatele prostřednictvím toku iniciované zprostředkovatelem identity, použijte tuto adresu URL:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Nahraďte následující hodnoty:

* **název tenanta** s názvem tenanta
* **název zásady** s názvem zásady předávající strany SAML
* identifikátor **App-Identifier-URI** s `identifierUris` v souboru metadat, jako je například`https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Ukázková zásada

Poskytujeme kompletní ukázkovou zásadu, kterou můžete použít pro testování pomocí testovací aplikace SAML.

1. Stáhněte si [ukázkovou zásadu přihlášení iniciované SAML-SP](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Aktualizujte `TenantId` tak, aby odpovídaly názvu tenanta, například *contoso.b2clogin.com*.
1. Ponechte název zásady *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algoritmus podpisu odpovědi SAML

Můžete nakonfigurovat algoritmus podpisu použitý k podepsání kontrolního výrazu SAML. Možné hodnoty jsou `Sha256` , `Sha384` , `Sha512` nebo `Sha1` . Zajistěte, aby technický profil a aplikace používaly stejný algoritmus podpisu. Používejte jenom algoritmus, který podporuje váš certifikát.

Nakonfigurujte algoritmus podpisu pomocí `XmlSignatureAlgorithm` klíče metadat v uzlu metadat RelyingParty.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Doba životnosti odezvy SAML

Můžete nastavit dobu, po kterou odpověď SAML zůstane platná. Nastavte dobu života pomocí `TokenLifeTimeInSeconds` položky metadat v rámci technického profilu vystavitele tokenu SAML. Tato hodnota je počet sekund, které mohou uplynout od `NotBefore` časového razítka vypočítaného v době vystavení tokenu. Automaticky je čas vybraný pro toto aktuální čas. Výchozí doba života je 300 sekund (5 minut).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>Odezva SAML platná od zkosení

Můžete nakonfigurovat časový posun aplikovaný na `NotBefore` časové razítko odezvy SAML. Tato konfigurace zajišťuje, že pokud se časy mezi dvěma platformami nesynchronizují, bude kontrolní výraz SAML v tomto časovém intervalu považován za platný, i když v této době.

Nastavte časové zkosení pomocí `TokenNotBeforeSkewInSeconds` položky metadat v rámci technického profilu vystavitele tokenu SAML. Hodnota asymetrie je uvedena v sekundách, přičemž výchozí hodnota je 0. Maximální hodnota je 3600 (jedna hodina).

Například když `TokenNotBeforeSkewInSeconds` je nastavené na `120` sekund:

- Token se vydá v 13:05:10 UTC.
- Token je platný od 13:03:10 UTC.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>ID vystavitele Azure AD B2C

Pokud máte více aplikací SAML závislých na různých `entityID` hodnotách, můžete `issueruri` hodnotu v souboru předávající strany přepsat. Pokud chcete přepsat identifikátor URI vystavitele, zkopírujte technický profil s ID "Saml2AssertionIssuer" ze základního souboru a přepište `issueruri` hodnotu.

> [!TIP]
> Zkopírujte `<ClaimsProviders>` část ze základní třídy a zachovejte tyto prvky v rámci zprostředkovatele deklarací: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` a `<DisplayName>Token Issuer</DisplayName>` .
 
Příklad:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Správa relací

Můžete spravovat relaci mezi Azure AD B2C a aplikací předávající strany SAML pomocí `UseTechnicalProfileForSessionManagement` elementu a [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="debug-the-saml-protocol"></a>Ladit protokol SAML

K usnadnění konfigurace a ladění integrace s vaším poskytovatelem služeb můžete použít rozšíření prohlížeče pro protokol SAML, například [rozšíření SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pro Chrome, [trasování SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pro Firefox nebo [vývojové nástroje pro vývojáře v IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Pomocí těchto nástrojů můžete kontrolovat integraci mezi aplikací a Azure AD B2C. Například:

* Zkontrolujte, zda požadavek SAML obsahuje signaturu a určíte, jaký algoritmus se má použít k přihlášení k žádosti o autorizaci.
* Ověřte, zda Azure AD B2C vrátí chybovou zprávu.
* Ověřte, že je oddíl kontrolního výrazu zašifrovaný.

## <a name="next-steps"></a>Další kroky

- Další informace o [protokolu SAML najdete na webu Oasis](https://www.oasis-open.org/).
- Stáhněte si webovou aplikaci testu SAML z [Azure AD B2C úložiště komunity GitHubu](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
