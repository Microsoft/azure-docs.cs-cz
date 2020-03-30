---
title: Metadata federace Azure AD | Dokumenty společnosti Microsoft
description: Tento článek popisuje dokument metadat federace, který Služba Azure Active Directory publikuje pro služby, které přijímají tokeny služby Azure Active Directory.
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
ms.openlocfilehash: bcc44f61ccb7b4a19e7df39ab979669c5aa37da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154895"
---
# <a name="federation-metadata"></a>Metadata federování

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publikuje dokument metadat federace pro služby, které jsou nakonfigurované tak, aby přijímalo tokeny zabezpečení, které azure ad problémy. Formát dokumentu metadat federace je popsán ve [verzi 1.2 jazyka WS-Federation (Web Services Federation Language)](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)– Federation Language , který rozšiřuje [metadata pro jazyk saml (Security Assertionup Language) (SAML) v.](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Koncové body metadat specifické pro klienta a nezávislé na tenantovi
Azure AD publikuje koncové body specifické pro klienta a nezávislé na tenantovi.

Koncové body specifické pro klienta jsou určeny pro konkrétního klienta. Metadata federace specifické pro klienta obsahuje informace o tenantovi, včetně informací o vystavitně specifickém pro klienta a koncového bodu. Aplikace, které omezují přístup k jednomu klientovi, používají koncové body specifické pro klienta.

Koncové body nezávislé na tenantovi poskytují informace, které jsou společné pro všechny klienty Azure AD. Tyto informace se vztahují na klienty hostované v *login.microsoftonline.com* a jsou sdíleny mezi klienty. Koncové body nezávislé na tenantovi se doporučují pro víceklientské aplikace, protože nejsou přidruženy k žádnému konkrétnímu tenantovi.

## <a name="federation-metadata-endpoints"></a>Koncové body metadat federace
Azure AD publikuje metadata federace na . `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`

Pro koncové body specifické `TenantDomainName` pro **klienta**může být jedním z následujících typů:

* Registrovaný název domény klienta Azure AD, `contoso.onmicrosoft.com`například: .
* Neměnné ID klienta domény, `72f988bf-86f1-41af-91ab-2d7cd011db45`například .

Pro koncové body nezávislé `TenantDomainName` na `common` **tenantovi**je . Tento dokument obsahuje pouze prvky metadat federace, které jsou společné pro všechny klienty Azure AD, které jsou hostované na login.microsoftonline.com.

Koncový bod specifický pro klienta `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`může být například . Koncový bod nezávislý na [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)tenantovi je . Dokument metadat federace můžete zobrazit zadáním této adresy URL do prohlížeče.

## <a name="contents-of-federation-metadata"></a>Obsah metadat federace
Následující část obsahuje informace potřebné pro služby, které spotřebovávají tokeny vydané Službou Azure AD.

### <a name="entity-id"></a>Entity ID
Prvek `EntityDescriptor` obsahuje `EntityID` atribut. Hodnota atributu `EntityID` představuje vystavittele, to znamená službu tokenu zabezpečení (STS), která vydala token. Je důležité ověřit vystavithona při přijetí tokenu.

Následující metadata zobrazuje ukázkový `EntityDescriptor` prvek specifický `EntityID` pro klienta s elementem.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
ID klienta v koncovém bodě nezávislém na tenantovi můžete nahradit `EntityID` id klienta a vytvořit hodnotu specifickou pro klienta. Výsledná hodnota bude stejná jako vystavitel tokenu. Strategie umožňuje víceklientské aplikace k ověření vystavittele pro daného klienta.

Následující metadata zobrazuje ukázkový `EntityID` prvek nezávislý na tenantovi. Vezměte prosím na `{tenant}` vědomí, že je doslovný, nikoli zástupný symbol.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Podpisové certifikáty tokenů
Když služba obdrží token, který je vydán klientem Azure AD, podpis tokenu musí být ověřen podpisovým klíčem, který je publikovaný v dokumentu metadat federace. Metadata federace zahrnuje veřejnou část certifikátů, které klienti používají pro podepisování tokenů. V elementu se zobrazí `KeyDescriptor` nezpracované bajty certifikátu. Podpisový certifikát tokenu je platný pro `use` podepisování pouze v případě, že hodnota atributu je `signing`.

Dokument metadat federace publikovaný službou Azure AD může mít více podpisových klíčů, například když se Azure AD připravuje na aktualizaci podpisového certifikátu. Pokud dokument metadat federace obsahuje více než jeden certifikát, služba, která ověřuje tokeny, by měla podporovat všechny certifikáty v dokumentu.

Následující metadata zobrazují `KeyDescriptor` ukázkový prvek s podpisovým klíčem.

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

Prvek `KeyDescriptor` se zobrazí na dvou místech v dokumentu metadat federace; v části specifické pro WS-Federation a v části specifické pro SAML. Certifikáty publikované v obou částech budou stejné.

V části ws-federation specifické pro WS federace čtení metadat WS-Federation by číst certifikáty z `RoleDescriptor` prvku s typem. `SecurityTokenServiceType`

Následující metadata zobrazují `RoleDescriptor` ukázkový prvek.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

V části specifické pro SAML ws-federation čtečka metadat by `IDPSSODescriptor` číst certifikáty z prvku.

Následující metadata zobrazují `IDPSSODescriptor` ukázkový prvek.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Neexistují žádné rozdíly ve formátu certifikátů specifických pro klienta a nezávislých na tenantovi.

### <a name="ws-federation-endpoint-url"></a>Adresa URL koncového bodu WS-Federation
Metadata federace obsahuje adresu URL, která je Azure AD používá pro jednotné přihlášení a jednotné přihlášení v protokolu WS-Federation. Tento koncový bod `PassiveRequestorEndpoint` se zobrazí v prvku.

Následující metadata zobrazuje `PassiveRequestorEndpoint` ukázkový prvek pro koncový bod specifický pro klienta.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Pro koncový bod nezávislý na tenantovi se adresa URL WS-Federation zobrazí v koncovém bodu WS-Federation, jak je znázorněno v následující ukázce.

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
Metadata federace obsahuje adresu URL, kterou služba Azure AD používá pro jednotné přihlášení a jednotné přihlášení v protokolu SAML 2.0. Tyto koncové body `IDPSSODescriptor` se zobrazí v prvku.

Adresy URL přihlášení a odhlášení se zobrazí `SingleSignOnService` `SingleLogoutService` v elementech a.

Následující metadata ukazuje `PassiveResistorEndpoint` ukázku pro koncový bod specifický pro klienta.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Podobně koncové body pro běžné koncové body protokolu SAML 2.0 jsou publikovány v metadatech federace nezávislých na tenantovi, jak je znázorněno v následující ukázce.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
