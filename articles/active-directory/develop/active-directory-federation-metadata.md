---
title: Federační Metadata Azure AD | Microsoft Docs
description: Tento článek popisuje federační metadata dokumentu, který publikuje Azure Active Directory pro služby, které přijímají tokeny služby Azure Active Directory.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: cfc79b451eafe7dcdd0b8f4285f92714138260bb
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156122"
---
# <a name="federation-metadata"></a>Metadata federování
Azure Active Directory (Azure AD) publikuje dokumentu metadat federace pro služby nakonfigurovaný tak, aby přijímal tokeny zabezpečení, které Azure AD vydá. Formát dokumentu metadat federace je popsaný v tématu [Web Services Federation Language (WS-Federation) verze 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), který rozšiřuje [Metadata pro v2.0 OASIS Security Assertion Markup Language (SAML)](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Specifické pro klienta a koncové body metadat nezávislé na klienta
Azure AD publikuje koncové body konkrétního klienta a nezávislé na klienta.

Koncové body konkrétního klienta jsou navrženy pro konkrétního klienta. Specifické pro klienta federační metadata obsahují informace o klientovi, včetně informací o konkrétního klienta vystavitele a koncového bodu. Aplikace, které omezují přístup na jednoho klienta pomocí konkrétního klienta koncové body.

Koncové body klienta nezávislé poskytují informace, které jsou společné pro všechny klienty Azure AD. Tyto informace platí pro klienty hostovanou na *login.microsoftonline.com* , že je sdílená mezi klienty. Nezávislé na klienta koncových bodů – se doporučují pro víceklientské aplikace, vzhledem k tomu, že nejsou přidružené žádné konkrétní klienta.

## <a name="federation-metadata-endpoints"></a>Koncové body federačních metadat
Azure AD publikuje federační metadata v `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Pro **koncové body konkrétního klienta**, `TenantDomainName` může být jedna z následujících typů:

* Klienta registrovaný název domény služby Azure AD, jako například: `contoso.onmicrosoft.com`.
* Neměnné ID domény, jako například klienta `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Pro **koncové body klienta nezávislé**, `TenantDomainName` je `common`. Tento dokument uvádí jenom prvky federačních metadat, které jsou společné pro všechny klienty Azure AD, které jsou hostované v login.microsoftonline.com.

Koncový bod konkrétního klienta může být například `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Koncový bod nezávislé na klienta je [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Dokument metadat federace můžete zobrazit tak, že zadáte tuto adresu URL v prohlížeči.

## <a name="contents-of-federation-metadata"></a>Obsah federační Metadata
Následující část obsahuje informace potřebné pro služby, které používají tokeny vydané službou Azure AD.

### <a name="entity-id"></a>ID entity
`EntityDescriptor` Obsahuje element `EntityID` atribut. Hodnota `EntityID` atribut představuje vystavitele, tedy služby tokenů zabezpečení (STS) vydaný token. Je důležité k ověření vystavitele, když obdrží token.

Následující metadata zobrazí ukázku konkrétního klienta `EntityDescriptor` element s `EntityID` elementu.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
ID klienta v koncovém bodě nezávislé na klienta můžete nahradit vaše ID klienta k vytvoření konkrétního klienta `EntityID` hodnotu. Výsledná hodnota bude stejné jako vydavatel tokenu. Strategie umožňuje víceklientské aplikace k ověření vystavitele pro daného klienta.

Následující metadata zobrazí ukázku nezávislé na klienta `EntityID` elementu. Upozorňujeme, že `{tenant}` je literál, není zástupný symbol.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Podpisové certifikáty tokenu
Když služba přijme token, který je vydaný klienta Azure AD, musí být podpis tokenu ověřené pomocí podpisový klíč, který je publikován v dokument federačních metadat. Federační metadata obsahují veřejnou část certifikátů, které klienti používat k podepisování tokenů. Nezpracovaná bajtů certifikátu se zobrazí v `KeyDescriptor` elementu. Podpisový certifikát tokenu je platný pro podepisování pouze tehdy, když hodnota `use` atribut je `signing`.

Dokument metadat federace publikována ve službě Azure AD může mít několik podpisové klíče, například když se připravuje Azure AD k aktualizaci podpisového certifikátu. Pokud dokument federačních metadat obsahuje více než jeden certifikát, služba, která je ověřování tokenů by měly podporovat všechny certifikáty v dokumentu.

Následující metadata zobrazí ukázku `KeyDescriptor` element s podpisový klíč.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` Prvek se zobrazuje na dvou místech v dokument federačních metadat, v části WS-Federation konkrétní a v části konkrétní SAML. Certifikáty, které jsou publikovány v obou částech budou stejné.

V části WS-Federation konkrétní by čtečku metadat specifikace WS-Federation číst certifikáty z `RoleDescriptor` element s `SecurityTokenServiceType` typu.

Následující metadata zobrazí ukázku `RoleDescriptor` elementu.

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

V části konkrétní SAML by čtečku metadat specifikace WS-Federation číst certifikáty z `IDPSSODescriptor` elementu.

Následující metadata zobrazí ukázku `IDPSSODescriptor` elementu.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nejsou žádné rozdíly ve formátu konkrétního klienta a nezávislé na klientské certifikáty.

### <a name="ws-federation-endpoint-url"></a>Adresa URL koncového bodu služby WS-Federation
Federační metadata obsahují adresu URL, která se používá Azure AD pro jeden přihlašování a odhlašování v protokolu WS-Federation jednou. Tento koncový bod se zobrazí v `PassiveRequestorEndpoint` elementu.

Následující metadata zobrazí ukázku `PassiveRequestorEndpoint` element pro koncový bod konkrétního klienta.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Pro koncový bod nezávislé na klienta adresa URL služby WS-Federation se zobrazí v koncovém bodě WS-Federation, jak znázorňuje následující ukázka.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>Adresa URL koncového bodu protokolu SAML
Federační metadata obsahují adresu URL, která používá Azure AD pro jeden přihlašování a odhlašování v protokolu SAML 2.0 jednou. Tyto koncové body se zobrazí v `IDPSSODescriptor` elementu.

Přihlášení a odhlášení adresy URL se zobrazí v `SingleSignOnService` a `SingleLogoutService` elementy.

Následující metadata zobrazí ukázku `PassiveResistorEndpoint` pro koncový bod konkrétního klienta.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobně jako koncové body pro běžné koncové body protokolu SAML 2.0 jsou publikovány v nezávislé na klienta federačních metadat, jak znázorňuje následující ukázka.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
