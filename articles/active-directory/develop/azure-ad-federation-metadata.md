---
title: Metadata federování služby Azure AD | Dokumentace Microsoftu
description: Tento článek popisuje dokumentu federačních metadat, který publikuje Azure Active Directory pro služby, které přijímají tokeny služby Azure Active Directory.
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
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: af35a517fcb2a907244272ecdec0d8dde3d827a9
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427405"
---
# <a name="federation-metadata"></a>Metadata federování
Azure Active Directory (Azure AD) publikuje dokumentu metadat federace pro služby, který je nakonfigurovaný tak, aby přijímal tokeny zabezpečení, které Azure AD vydá. Formát dokumentu federačních metadat je popsáno v [Web Services Federation Language (WS-Federation) verze 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), která rozšiřuje [Metadata pro OASIS Security Assertion Markup Language (SAML) 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Specifickým pro tenanta a Tenant nezávislé na koncové body metadat
Azure AD publikuje specifickým pro tenanta a tenant nezávislé na koncové body.

Koncové body specifickým pro tenanta jsou určeny pro konkrétní tenanta. Specifickým pro tenanta federační metadata obsahují informace o tenantovi, včetně informací o vydavatele a koncový bod specifickým pro tenanta. Aplikace, které omezují přístup do jednoho tenanta používají koncové body specifickým pro tenanta.

Koncové body nezávislé na tenanta poskytují informace, které jsou společné pro všechny klienty Azure AD. Tyto informace platí pro klienty hostované v *login.microsoftonline.com* a je sdílen mezi tenanty. Koncové body nezávislé na tenanta se doporučují pro víceklientské aplikace, protože nejsou přidružené žádné konkrétní tenanta.

## <a name="federation-metadata-endpoints"></a>Koncové body metadat federace
Azure AD publikuje federační metadata v `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`.

Pro **koncové body specifickým pro tenanta**, `TenantDomainName` může být jedna z následujících typů:

* Registrovaný název domény služby Azure AD tenanta, jako například: `contoso.onmicrosoft.com`.
* Neměnné ID domény, jako například tenanta `72f988bf-86f1-41af-91ab-2d7cd011db45`.

Pro **koncové body klienta nezávislé**, `TenantDomainName` je `common`. Tento dokument obsahuje seznam pouze prvky federačních metadat, které jsou společné pro všechny tenanty Azure AD, které jsou hostované na login.microsoftonline.com.

Koncový bod specifickým pro tenanta může být například `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`. Koncový bod nezávislé na tenanta je [ https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml ](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Dokument metadat federace můžete zobrazit tak, že zadáte tuto adresu URL v prohlížeči.

## <a name="contents-of-federation-metadata"></a>Obsah federačních metadat
Následující část obsahuje informace potřebné pro služby, které používají tokeny vydané službou Azure AD.

### <a name="entity-id"></a>ID entity
`EntityDescriptor` Obsahuje element `EntityID` atribut. Hodnota `EntityID` atribut představuje vystavitele, to znamená, služby tokenů zabezpečení (STS), která token vydala. Je důležité k ověření vystavitele, když obdrží token.

Následující metadata Zobrazuje ukázku specifickým pro tenanta `EntityDescriptor` element s `EntityID` elementu.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
ID tenanta v koncovém bodě nezávislé na tenanta můžete nahradit za ID vašeho tenanta, chcete-li vytvořit konkrétního klienta `EntityID` hodnotu. Výsledná hodnota bude stejná jako vydavatel tokenu. Strategie umožňuje aplikaci s více tenanty ověření vystavitele pro daného tenanta.

Následující metadata Zobrazuje ukázku nezávislé na tenanta `EntityID` elementu. Mějte prosím na paměti, která `{tenant}` je literál, nikoli zástupný symbol.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Podpisové certifikáty tokenů
Když služba přijímá token, který je vydaný tenanta Azure AD, podpis tokenu musí být ověřené pomocí podpisového klíče, který je publikován v dokumentu federačních metadat. Federační metadata obsahují veřejnou část certifikáty, které klienti používají k podepisování tokenů. Nezpracovaná bajtů certifikátu se zobrazí v `KeyDescriptor` elementu. Podpisový certifikát tokenu je platný pro podepisování pouze tehdy, když hodnota `use` atribut je `signing`.

Dokument metadat federace publikované ve službě Azure AD může mít více podpisové klíče, jako je například, když Azure AD se připravuje k aktualizaci podpisového certifikátu. Pokud dokument metadat federace zahrnuje více než jeden certifikát, službu, která ověřuje tokeny musí podporovat všechny certifikáty v dokumentu.

Následující metadata Zobrazuje ukázku `KeyDescriptor` element s podpisový klíč.

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

`KeyDescriptor` Prvek se zobrazuje na dvou místech v dokumentu federačních metadat, WS-Federation konkrétní části a v části týkající se SAML. Certifikáty publikovaných v obou částech budou stejné.

V části WS-Federation konkrétní by čtečku metadat WS-Federation čtení certifikáty vydané `RoleDescriptor` element s `SecurityTokenServiceType` typu.

Následující metadata Zobrazuje ukázku `RoleDescriptor` elementu.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

V části týkající se SAML by číst čtečka metadat WS-Federation certifikáty vydané `IDPSSODescriptor` elementu.

Následující metadata Zobrazuje ukázku `IDPSSODescriptor` elementu.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Nejsou žádné rozdíly ve formátu specifickým pro tenanta a tenant nezávislé na certifikáty.

### <a name="ws-federation-endpoint-url"></a>Adresa URL koncového bodu WS-Federation
Federační metadata obsahují adresu URL, kterou používá Azure AD pro jednotné přihlašování a odhlašování v protokolu WS-Federation jednou. Tento koncový bod se zobrazí v `PassiveRequestorEndpoint` elementu.

Následující metadata Zobrazuje ukázku `PassiveRequestorEndpoint` – element pro koncový bod specifickým pro tenanta.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Pro tenanta nezávislé na koncový bod WS-Federation adresy URL se zobrazí v koncového bodu WS-Federation, jak je znázorněno v následujícím příkladu.

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
Federační metadata obsahují adresu URL, kterou používá Azure AD pro jednotné přihlašování a odhlašování přes protokol SAML 2.0 protokolu jednou. Tyto koncové body se zobrazí v `IDPSSODescriptor` elementu.

Adresy URL přihlašování a odhlašování se objeví v `SingleSignOnService` a `SingleLogoutService` elementy.

Následující metadata Zobrazuje ukázku `PassiveResistorEndpoint` pro koncový bod specifickým pro tenanta.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobně koncových bodů pro běžné koncové body protokolu SAML 2.0 jsou zveřejněné v tenantovi nezávislé federačních metadat, jak je znázorněno v následujícím příkladu.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
