---
title: Federační metadata Azure AD | Microsoft Docs
description: Tento článek popisuje dokument federačních metadat, který Azure Active Directory zveřejňuje pro služby, které přijímají Azure Active Directory tokeny.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7f3bd8851fe723461c618499e539c987d79c0d68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650137"
---
# <a name="federation-metadata"></a>Metadata federování

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) zveřejňuje dokument federačních metadat pro služby, které jsou nakonfigurované tak, aby přijímaly tokeny zabezpečení, které Azure AD řeší. Formát dokumentu federačních metadat je popsaný v tématu [specifikace Web Services Federation Language (WS-Federation) verze 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), který rozšiřuje [metadata pro Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf).

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Koncové body metadat závislé na klientovi a klientovi
Azure AD publikuje koncové body závislé na klientovi a klientovi.

Koncové body konkrétního tenanta jsou navržené pro konkrétního tenanta. Federační metadata specifická pro tenanta obsahují informace o tenantovi, včetně vystavitele pro konkrétního klienta a informace o koncovém bodu. Aplikace, které omezují přístup k jednomu tenantovi, používají koncové body konkrétního tenanta.

Koncové body nezávislé na klientovi poskytují informace, které jsou společné pro všechny klienty Azure AD. Tyto informace se vztahují na klienty hostované na *Login.microsoftonline.com* a sdílí se mezi klienty. Pro víceklientské aplikace jsou doporučovány koncové body nezávislé na tenantovi, protože nejsou přidruženy k žádnému konkrétnímu tenantovi.

## <a name="federation-metadata-endpoints"></a>Koncové body federačních metadat
Azure AD publikuje federační metadata na adrese `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` .

U **koncových bodů specifických** pro klienta `TenantDomainName` může být jedním z následujících typů:

* Registrovaný název domény tenanta Azure AD, například: `contoso.onmicrosoft.com` .
* Neměnné ID klienta domény, například `72f988bf-86f1-41af-91ab-2d7cd011db45` .

Pro **koncové body nezávislé na klientovi** `TenantDomainName` je `common` . Tento dokument obsahuje jenom prvky federačních metadat, které jsou společné pro všechny klienty Azure AD hostované na adrese login.microsoftonline.com.

Například může být koncový bod specifický pro tenanta `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml` . Koncový bod nezávislý na klientovi je [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) . Dokument federačních metadat můžete zobrazit zadáním této adresy URL do prohlížeče.

## <a name="contents-of-federation-metadata"></a>Obsah federačních metadat
V následující části najdete informace potřebné pro služby, které využívají tokeny vydané službou Azure AD.

### <a name="entity-id"></a>ID entity
`EntityDescriptor`Element obsahuje `EntityID` atribut. Hodnota `EntityID` atributu představuje vystavitele, tedy službu tokenu zabezpečení (STS), která token vystavila. Při obdržení tokenu je důležité ověřit vystavitele.

Následující metadata ukazují ukázkový `EntityDescriptor` element specifický pro tenanta s `EntityID` prvkem.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Pokud chcete vytvořit hodnotu specifickou pro tenanta, můžete nahradit ID tenanta v koncovém bodu nezávislém na klientovi s vaším ID tenanta `EntityID` . Výsledná hodnota bude stejná jako Vystavitel tokenu. Strategie umožňuje více tenantů aplikacím ověřit vystavitele pro daného tenanta.

Následující metadata ukazují ukázkový element nezávislý na tenantovi `EntityID` . Všimněte si, že `{tenant}` je literál, nikoli zástupný symbol.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Podpisové certifikáty tokenů
Když služba obdrží token, který je vydaný tenant služby Azure AD, signatura tokenu musí být ověřená podpisovým klíčem, který je publikovaný v dokumentu federačních metadat. Federační metadata obsahují veřejnou část certifikátů, které klienti používají pro podepisování tokenů. V elementu se zobrazí nezpracované bajty certifikátů `KeyDescriptor` . Podpisový certifikát tokenu je platný pro podepisování pouze v případě, že hodnota `use` atributu je `signing` .

Dokument federačních metadat publikovaný službou Azure AD může mít víc podpisových klíčů, například když se služba Azure AD připravuje k aktualizaci podpisového certifikátu. Pokud dokument federačních metadat obsahuje více než jeden certifikát, musí služba ověřující tokeny podporovat všechny certifikáty v dokumentu.

Následující metadata ukazují vzorový `KeyDescriptor` prvek s podpisovým klíčem.

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

`KeyDescriptor`Prvek se zobrazí na dvou místech dokumentu federačních metadat; v části WS-Federation-Specific a v části specifické pro SAML. Certifikáty publikované v obou částech budou stejné.

V části specifické pro WS-Federation by WS-Federation čtecí modul metadat načetl certifikáty z `RoleDescriptor` prvku s `SecurityTokenServiceType` typem.

Následující metadata ukazují vzorový `RoleDescriptor` prvek.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType" protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

V části specifické pro SAML by WS-Federation čtecí modul metadat přečetl certifikáty z `IDPSSODescriptor` elementu.

Následující metadata ukazují vzorový `IDPSSODescriptor` prvek.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Ve formátu certifikátů specifických pro tenanta a klientů nezávisle na klientovi nejsou žádné rozdíly.

### <a name="ws-federation-endpoint-url"></a>Adresa URL koncového bodu WS-Federation
Federační metadata obsahují adresu URL, která je službou Azure AD používána pro jednotné přihlašování a jednotné přihlašování v WS-Federationm protokolu. Tento koncový bod se zobrazí v `PassiveRequestorEndpoint` elementu.

Následující metadata ukazují vzorový `PassiveRequestorEndpoint` prvek pro koncový bod pro konkrétního klienta.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
V případě koncového bodu nezávislého na klientovi se adresa URL WS-Federation zobrazí v koncovém bodu WS-Federation, jak je znázorněno v následující ukázce.

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
Federační metadata obsahují adresu URL, kterou Azure AD používá pro jednotné přihlašování a jednotné přihlašování v protokolu SAML 2,0. Tyto koncové body se zobrazí v `IDPSSODescriptor` elementu.

Přihlášení a adresy URL pro odhlášení se zobrazí v `SingleSignOnService` `SingleLogoutService` prvcích a.

Následující metadata ukazují ukázku `PassiveResistorEndpoint` pro koncový bod pro konkrétního klienta.

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
