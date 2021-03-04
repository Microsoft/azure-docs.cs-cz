---
title: Nastavení přihlášení s možnostmi zprostředkovatele identity SAML
titleSuffix: Azure Active Directory B2C
description: Nakonfigurujte možnosti IdP (přihlásit se) zprostředkovatele identity SAML v Azure Active Directory B2C.
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
ms.openlocfilehash: 075b04414c752ce87365d03212fcdabab6eaa7dd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119820"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Konfigurace možností zprostředkovatele identity SAML pomocí Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje federaci s poskytovateli identit SAML 2,0. Tento článek popisuje možnosti konfigurace, které jsou k dispozici při povolování přihlášení pomocí zprostředkovatele identity SAML.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Mapování deklarací identity

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity SAML. Musíte namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Seznam deklarací identity (kontrolní výrazy) najdete u svého zprostředkovatele identity. Můžete také ověřit obsah odpovědi SAML, kterou váš poskytovatel identity vrátí. Další informace najdete v tématu [ladění zpráv SAML](#debug-saml-protocol). Pokud chcete přidat deklaraci identity, nejdřív [Definujte deklaraci](claimsschema.md)identity a pak přidejte deklaraci do výstupní kolekce deklarací.

Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut. Výchozí hodnota může být statická nebo dynamická pomocí [deklarací identity kontextu](#enable-use-of-context-claims).

Výstupní element deklarace identity obsahuje následující atributy:

- **ClaimTypeReferenceId** je odkaz na typ deklarace identity. 
- **PartnerClaimType** je název vlastnosti, která se zobrazí jako kontrolní výraz SAML. 
- **DefaultValue** je předdefinovaná výchozí hodnota. Pokud je deklarace identity prázdná, použije se výchozí hodnota. [Překladače deklarací identity](claim-resolver-overview.md) můžete použít také s kontextovou hodnotou, jako je ID korelace, nebo IP adresa uživatele.

### <a name="subject-name"></a>Název předmětu

Chcete-li v **předmětu** načíst kontrolní výraz SAML **NameId** jako normalizovanou deklaraci identity, nastavte deklaraci identity **PartnerClaimType** na hodnotu `SPNameQualifier` atributu. Pokud `SPNameQualifier` atribut není zobrazen, nastavte deklaraci identity **PartnerClaimType** na hodnotu `NameQualifier` atributu.

Kontrolní výraz SAML:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Výstupní deklarace identity:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Pokud se `SPNameQualifier` `NameQualifier` v kontrolním výrazu SAML neprezentují oba atributy nebo, nastavte **PartnerClaimType** deklarací na `assertionSubjectName` . Ujistěte se, že **NameId** je první hodnotou v XML kontrolního výrazu. Při definování více než jednoho kontrolního výrazu Azure AD B2C vybere hodnotu předmětu z posledního kontrolního výrazu.


## <a name="configure-saml-protocol-bindings"></a>Konfigurovat vazby protokolu SAML

Požadavky SAML se odesílají zprostředkovateli identity, jak je uvedeno v elementu metadat poskytovatele identity `SingleSignOnService` . Většina požadavků na autorizaci zprostředkovatele identity se přenášejí přímo v řetězci dotazu URL požadavku HTTP GET (protože zprávy jsou relativně krátké). Informace o tom, jak nakonfigurovat vazby pro oba požadavky SAML, najdete v dokumentaci ke zprostředkovateli identity.

Níže je příklad služby jednotného přihlašování k metadatům Azure AD se dvěma vazbami. Má `HTTP-Redirect` přednost před rozhraním, `HTTP-POST` protože se zobrazuje jako první v metadatech zprostředkovatele identity SAML.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

Odpovědi SAML jsou přenášeny do Azure AD B2C prostřednictvím vazby HTTP POST. Metadata zásad Azure AD B2C nastaví `AssertionConsumerService` vazbu na `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` .

Následuje příklad Assertion Consumer Service elementu Azure AD B2C metadat zásad.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>Konfigurace podpisu žádosti SAML

Azure AD B2C podepisuje všechny požadavky na odchozí ověřování pomocí kryptografického klíče **SamlMessageSigning** . Pokud chcete podpis požadavku SAML zakázat, nastavte **WantsSignedRequests** na `false` . Pokud jsou metadata nastavena na `false` , jsou z požadavku vynechány parametry **SigAlg** a **signatury** (řetězec dotazu nebo parametr post).

Tato metadata také řídí atribut **AuthnRequestsSigned** , který je součástí metadat Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity. Azure AD B2C nepodepisuje požadavek, pokud je hodnota **WantsSignedRequests** v metadatech technického profilu nastavená na `false` a **WantAuthnRequestsSigned** metadata poskytovatele identity je nastavená na `false` nebo není zadaná.

Následující příklad odstraní signaturu z požadavku SAML.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Algoritmus podpisu

Azure AD B2C používá `Sha1` k podepsání požadavku SAML. Použijte metadata **XmlSignatureAlgorithm** ke konfiguraci algoritmu, který se má použít. Možné hodnoty jsou `Sha256` , `Sha384` , `Sha512` nebo `Sha1` (výchozí). Tato metadata řídí hodnotu parametru  **SigAlg** (řetězec dotazu nebo parametr post) v požadavku SAML. Nezapomeňte nakonfigurovat algoritmus podpisu na obou stranách se stejnou hodnotou. Používejte jenom algoritmus, který podporuje váš certifikát.

### <a name="include-key-info"></a>Zahrnout klíčové informace

Když poskytovatel identity indikuje, že Azure AD B2C vazba je nastavená na `HTTP-POST` , Azure AD B2C obsahuje signaturu a algoritmus v těle požadavku SAML. Službu Azure AD můžete také nakonfigurovat tak, aby zahrnovala veřejný klíč certifikátu, když je vazba nastavená na `HTTP-POST` . Použijte metadata **IncludeKeyInfo** pro `true` , nebo `false` . V následujícím příkladu služba Azure AD nebude obsahovat veřejný klíč certifikátu.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>Konfigurovat ID názvu žádosti SAML

Element žádosti o autorizaci SAML `<NameID>` indikuje formát identifikátoru názvu SAML. V této části je popsána výchozí konfigurace a postup přizpůsobení prvku ID názvu.

## <a name="preferred-username"></a>Preferované uživatelské jméno

Během cesty uživatele přihlašování může aplikace předávající strany cílit na konkrétního uživatele. Azure AD B2C umožňuje odeslat upřednostňované uživatelské jméno zprostředkovateli identity SAML. Element **InputClaims** se používá k odeslání **NameId** v rámci **požadavku na** autorizaci SAML.

Chcete-li do žádosti o autorizaci zahrnout ID subjektu, přidejte následující `<InputClaims>` element hned za `<CryptographicKeys>` . **PartnerClaimType** musí být nastavená na `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

V tomto příkladu Azure AD B2C odesílá hodnotu deklarace identity **signInName** s **NameId** v rámci **subjektu** žádosti o autorizaci SAML.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

Můžete použít [deklarace kontextu](claim-resolver-overview.md), například `{OIDC:LoginHint}` k naplnění hodnoty deklarace identity.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Formát zásad ID názvu

Ve výchozím nastavení vydává požadavek na autorizaci SAML `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` zásadu. To znamená, že je možné použít jakýkoli typ identifikátoru podporovaný zprostředkovatelem identity pro požadovaný předmět.

Pokud chcete toto chování změnit, přečtěte si dokumentaci poskytovatele identity, kde najdete pokyny k tomu, které zásady názvového ID se podporují. Pak přidejte `NameIdPolicyFormat` metadata do odpovídajícího formátu zásad. Například:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

Následující žádost o autorizaci SAML obsahuje zásady ID názvu.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Povolení vytváření nových účtů

Pokud zadáte [Formát zásad ID názvu](#name-id-policy-format), můžete také zadat `AllowCreate` vlastnost **NameIDPolicy** , která určuje, jestli má zprostředkovatel identity během přihlašování vytvořit nový účet. Pokyny najdete v dokumentaci poskytovatele identity.

Azure AD B2C `AllowCreate` ve výchozím nastavení vynechá vlastnost. Toto chování můžete změnit pomocí `NameIdPolicyAllowCreate` metadat. Hodnota těchto metadat je `true` nebo `false` .

Následující příklad ukazuje, jak nastavit `AllowCreate` vlastnost `NameIDPolicy` na `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


Následující příklad ukazuje žádost o autorizaci s **AllowCreateem** elementu **NameIDPolicy** v žádosti o autorizaci.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Zahrnout odkazy na třídy kontextu ověřování

Žádost o autorizaci SAML může obsahovat element **AuthnContext** , který určuje kontext žádosti o autorizaci. Element může obsahovat odkaz třídy kontextu ověřování, který oznamuje zprostředkovateli identity SAML, který mechanismus ověřování má k dispozici uživateli.

Chcete-li konfigurovat odkazy na třídy kontextu ověřování, přidejte metadata **IncludeAuthnContextClassReferences** . V poli hodnota zadejte jeden nebo více odkazů identifikátorů URI, které identifikují třídy kontextu ověřování. Zadejte více identifikátorů URI jako seznam oddělených čárkami. Pokyny k **AuthnContextClassRef** identifikátorům URI, které jsou podporované, najdete v dokumentaci poskytovatele identity.

Následující příklad umožňuje uživatelům přihlásit se pomocí uživatelského jména a hesla a přihlásit se pomocí uživatelského jména a hesla přes chráněnou relaci (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

Následující žádost o autorizaci SAML obsahuje odkazy na třídy kontextu ověřování.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Zahrnout vlastní data do žádosti o autorizaci

Volitelně můžete zahrnout prvky rozšíření zprávy protokolu, které jsou odsouhlaseny službou Azure AD BC a vaším poskytovatelem identity. Přípona je prezentována ve formátu XML. Prvky rozšíření můžete zahrnout přidáním dat XML do elementu CDATA `<![CDATA[Your IDP metadata]]>` . Zkontrolujte dokumentaci poskytovatele identity a zjistěte, jestli je element Extensions podporovaný.

Následující příklad ilustruje použití dat rozšíření:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Při použití rozšíření zprávy protokolu SAML bude odpověď SAML vypadat jako v následujícím příkladu:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Vyžadovat podepsané odezvy SAML

Azure AD B2C vyžaduje, aby byly podepsány všechny příchozí kontrolní výrazy. Tento požadavek můžete odebrat nastavením **WantsSignedAssertions** na `false` . Zprostředkovatel identity by v tomto případě neměli podepisovat kontrolní výrazy, ale i v případě, Azure AD B2C podpis neověřuje.

Metadata **WantsSignedAssertions** řídí příznak metadat SAML **WantAssertionsSigned**, který je zahrnutý v metadatech Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Pokud zakážete ověření kontrolního výrazu, možná budete chtít zakázat ověření podpisu zprávy odpovědi. Nastavte metadata **ResponsesSigned** na `false` . Poskytovatel identity by v tomto případě neměl podepsat zprávu odpovědi SAML, ale i v případě, Azure AD B2C neověřuje podpis.

V následujícím příkladu je odebrána zpráva i signatura kontrolního výrazu:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Vyžadovat šifrované odpovědi SAML

Chcete-li vyžadovat šifrování všech příchozích kontrolních výrazů, nastavte metadata **WantsEncryptedAssertions** . Když se vyžaduje šifrování, zprostředkovatel identity použije veřejný klíč šifrovacího certifikátu v Azure AD B2C Technical Profile. Azure AD B2C dešifruje kontrolní výraz odpovědi pomocí soukromé části certifikátu šifrování.

Pokud povolíte šifrování kontrolních výrazů, může být také nutné zakázat ověřování podpisů odpovědí (Další informace najdete v tématu [vyžadování podepsaných odpovědí SAML](#require-signed-saml-responses).

Pokud jsou metadata **WantsEncryptedAssertions** nastavena na `true` , metadata profilu Azure AD B2C Technical profil obsahuje oddíl **šifrování** . Zprostředkovatel identity přečte metadata a zašifruje kontrolní výraz odpovědi SAML pomocí veřejného klíče, který je k dispozici v metadatech Azure AD B2C Technical Profile.

Následující příklad ukazuje oddíl popisovače klíče metadat SAML používaných k šifrování:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

Postup při šifrování kontrolního výrazu odpovědi SAML:

1. [Vytvořte klíč zásad](identity-provider-generic-saml.md#create-a-policy-key) s jedinečným identifikátorem. Například, `B2C_1A_SAMLEncryptionCert`.
2. V kolekci **CryptographicKeys** Technical Profile SAML. Nastavte **StorageReferenceId** na název klíče zásad, který jste vytvořili v prvním kroku. `SamlAssertionDecryption`ID označuje použití kryptografického klíče k šifrování a dešifrování kontrolního výrazu odpovědi SAML.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Nastavte metadata technického profilu **WantsEncryptedAssertions** na `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Aktualizujte zprostředkovatele identity pomocí nových metadat Azure AD B2C Technical Profile. Měl by se zobrazit **popisovač** klíče s nastavenou vlastností **Use** na `encryption` obsahující veřejný klíč certifikátu.

## <a name="enable-use-of-context-claims"></a>Povolit používání deklarací kontextu

Ve vstupní a výstupní kolekci deklarací identity můžete zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut. Můžete také použít [deklarace identity kontextu](claim-resolver-overview.md) , které se mají zahrnout do technického profilu. Použití deklarace kontextu:

1. Přidejte typ deklarace identity do prvku [ClaimsSchema](claimsschema.md) v rámci [BuildingBlocks](buildingblocks.md).
2. Přidejte výstupní deklaraci identity do vstupní nebo výstupní kolekce. V následujícím příkladu nastaví první deklarace hodnotu poskytovatele identity. Druhá deklarace používá [deklarace kontextu](claim-resolver-overview.md)IP adresy uživatele.
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Zakázat jednotné odhlášení

Po žádosti o odhlášení aplikace se Azure AD B2C pokusí odhlásit od poskytovatele identity SAML. Další informace najdete v tématu věnovaném [odhlášení Azure AD B2C relace](session-behavior.md#sign-out).  Pokud chcete zakázat chování jednotného odhlašování, nastavte metadata **SingleLogoutEnabled** na `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Ladit protokol SAML

Pro pomoc s konfigurací a laděním federace s poskytovatelem identity SAML můžete použít rozšíření prohlížeče pro protokol SAML, jako je například [rozšíření SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pro Chrome, [trasování SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pro Firefox nebo aplikace [Edge nebo IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Pomocí těchto nástrojů můžete kontrolovat integraci mezi Azure AD B2C a vaším poskytovatelem identity SAML. Například:

* Zkontrolujte, zda požadavek SAML obsahuje signaturu a určíte, jaký algoritmus se má použít k přihlášení k žádosti o autorizaci.
* Získejte deklarace (kontrolní výrazy) v `AttributeStatement` části.
* Zkontroluje, jestli zprostředkovatel identity vrací chybovou zprávu.
* Ověřte, zda je oddíl kontrolního výrazu zašifrovaný.

## <a name="next-steps"></a>Další kroky

- Naučte se diagnostikovat problémy s vlastními zásadami pomocí [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
