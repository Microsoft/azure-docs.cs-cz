---
title: Federační metadata Azure AD | Microsoft Docs
description: Tento článek popisuje dokument federačních metadat, který Azure Active Directory zveřejňuje pro služby, které přijímají Azure Active Directory tokeny.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: ff034da1f2f40ad0162e5b9fad477d066bc4c3e7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165095"
---
# <a name="federation-metadata"></a>Metadata federování

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) zveřejňuje dokument federačních metadat pro služby, které jsou nakonfigurované tak, aby přijímaly tokeny zabezpečení, které Azure AD řeší. Formát dokumentu federačních metadat je popsaný v tématu [specifikace Web Services Federation Language (WS-Federation) verze 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), který rozšiřuje [metadata pro Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Koncové body metadat závislé na klientovi a klientovi
Azure AD publikuje koncové body závislé na klientovi a klientovi.

Koncové body konkrétního tenanta jsou navržené pro konkrétního tenanta. Federační metadata specifická pro tenanta obsahují informace o tenantovi, včetně vystavitele pro konkrétního klienta a informace o koncovém bodu. Aplikace, které omezují přístup k jednomu tenantovi, používají koncové body konkrétního tenanta.

Koncové body nezávislé na klientovi poskytují informace, které jsou společné pro všechny klienty Azure AD. Tyto informace se vztahují na klienty hostované na *Login.microsoftonline.com* a sdílí se mezi klienty. Pro víceklientské aplikace jsou doporučovány koncové body nezávislé na tenantovi, protože nejsou přidruženy k žádnému konkrétnímu tenantovi.

## <a name="federation-metadata-endpoints"></a>Koncové body federačních metadat
Azure AD publikuje federační metadata na `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

U **koncových bodů specifických pro klienta**může být `TenantDomainName` jeden z následujících typů:

* Registrovaný název domény tenanta Azure AD, například: `contoso.onmicrosoft.com`.
* Neměnné ID klienta domény, například `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Pro **koncové body nezávislé na klientovi**je `TenantDomainName` `common`. Tento dokument obsahuje jenom prvky federačních metadat, které jsou společné pro všechny klienty Azure AD hostované na adrese login.microsoftonline.com.

Například koncový bod pro konkrétního klienta může být `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Koncový bod nezávislý na klientovi je [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Dokument federačních metadat můžete zobrazit zadáním této adresy URL do prohlížeče.

## <a name="contents-of-federation-metadata"></a>Obsah federačních metadat
V následující části najdete informace potřebné pro služby, které využívají tokeny vydané službou Azure AD.

### <a name="entity-id"></a>ID entity
Element `EntityDescriptor` obsahuje atribut `EntityID`. Hodnota atributu `EntityID` představuje vystavitele, tedy službu tokenů zabezpečení (STS), která token vystavila. Při obdržení tokenu je důležité ověřit vystavitele.

Následující metadata ukazují ukázkový prvek `EntityDescriptor` specifický pro tenanta s prvkem `EntityID`.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
ID tenanta v koncovém bodu nezávislém na klientovi můžete nahradit vaším ID tenanta, aby se vytvořila hodnota `EntityID` pro konkrétního tenanta. Výsledná hodnota bude stejná jako Vystavitel tokenu. Strategie umožňuje více tenantů aplikacím ověřit vystavitele pro daného tenanta.

Následující metadata ukazují ukázkový element `EntityID` nezávislý na tenantovi. Všimněte si, že `{tenant}` je literál, nikoli zástupný symbol.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Podpisové certifikáty tokenů
Když služba obdrží token, který je vydaný tenant služby Azure AD, signatura tokenu musí být ověřená podpisovým klíčem, který je publikovaný v dokumentu federačních metadat. Federační metadata obsahují veřejnou část certifikátů, které klienti používají pro podepisování tokenů. Nezpracované bajty certifikátu se zobrazí v elementu `KeyDescriptor`. Podpisový certifikát tokenu je platný pro podepisování pouze v případě, že je hodnota atributu `use` `signing`.

Dokument federačních metadat publikovaný službou Azure AD může mít víc podpisových klíčů, například když se služba Azure AD připravuje k aktualizaci podpisového certifikátu. Pokud dokument federačních metadat obsahuje více než jeden certifikát, musí služba ověřující tokeny podporovat všechny certifikáty v dokumentu.

Následující metadata ukazují vzorový `KeyDescriptor` element s podpisovým klíčem.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Element `KeyDescriptor` se zobrazí na dvou místech v dokumentu federačních metadat; v části specifické pro WS-Federation a v části specifické pro SAML. Certifikáty publikované v obou částech budou stejné.

V části specifické pro WS-Federation by modul pro čtení metadat WS-Federation načetl certifikáty z prvku `RoleDescriptor` s typem `SecurityTokenServiceType`.

Následující metadata ukazují vzorový `RoleDescriptor` element.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

V části specifické pro SAML by čtecí modul pro čtení metadat WS-Federation načetl certifikáty z `IDPSSODescriptor` elementu.

Následující metadata ukazují vzorový `IDPSSODescriptor` element.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Ve formátu certifikátů specifických pro tenanta a klientů nezávisle na klientovi nejsou žádné rozdíly.

### <a name="ws-federation-endpoint-url"></a>Adresa URL koncového bodu WS-Federation
Federační metadata obsahují adresu URL, která je službou Azure AD používána pro jednotné přihlašování a jednotné přihlašování v protokolu WS-Federation. Tento koncový bod se zobrazí v prvku `PassiveRequestorEndpoint`.

Následující metadata ukazují vzorový `PassiveRequestorEndpoint` element pro koncový bod specifický pro klienta.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
V případě koncového bodu nezávislého na klientovi se adresa URL WS-Federation zobrazuje v koncovém bodu WS-Federation, jak je znázorněno v následující ukázce.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Adresa URL koncového bodu protokolu SAML
Federační metadata obsahují adresu URL, kterou Azure AD používá pro jednotné přihlašování a jednotné přihlašování v protokolu SAML 2,0. Tyto koncové body se zobrazí v prvku `IDPSSODescriptor`.

Přihlášení a adresy URL pro odhlášení se zobrazí v prvcích `SingleSignOnService` a `SingleLogoutService`.

Následující metadata ukazují vzorový `PassiveResistorEndpoint` pro koncový bod pro konkrétního klienta.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobně jsou koncové body pro Common koncových bodů protokolu SAML 2,0 publikovány v federačních metadatech nezávislých na klientovi, jak je znázorněno v následující ukázce.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
