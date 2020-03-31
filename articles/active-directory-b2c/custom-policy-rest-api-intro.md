---
title: REST API nároky výměny v B2C vlastní zásady
titleSuffix: Azure AD B2C
description: Úvod k vytvoření cesty uživatele Azure AD B2C, která spolupracuje se službami RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337412"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrace renomovaných deklarací rozhraní REST ve vlastních zásadách Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní Identity Experience Framework, které je základem Azure Active Directory B2C (Azure AD B2C), můžete integrovat s RESTful rozhraní API v rámci cesty uživatele. Tento článek ukazuje, jak vytvořit cestu uživatele, která spolupracuje se službou RESTful pomocí [technického profilu RESTful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster).

Pomocí Azure AD B2C, můžete přidat vlastní obchodní logiku na cestu uživatele voláním vlastní služby RESTful. Rozhraní Identity Experience Framework můžete odesílat a přijímat data z vaší služby RESTful pro výměnu deklarací identity. Můžete například provést následující věci:

- **Ověřte vstupní data uživatele**. Můžete například ověřit, zda e-mailová adresa poskytnutá uživatelem existuje v databázi zákazníka, a pokud ne, zobrazí se chyba.
- **Zpracování deklarací identity**. Pokud uživatel zadá své křestní jméno ve všech malých nebo velkých písmen, rozhraní REST API můžete formátovat název pouze první písmeno velké a vrátit do Azure AD B2C.
- **Obohaťte uživatelská data další integrací s podnikovými aplikacemi**. Vaše služba RESTful můžete přijímat e-mailovou adresu uživatele, dotaz databáze zákazníka a vrátit věrnostní číslo uživatele do Azure AD B2C. Pak vrátí deklarace identity mohou být uloženy v účtu Uživatele Azure AD, vyhodnoceny v dalších krocích orchestrace nebo zahrnuty do přístupového tokenu.
- **Spusťte vlastní obchodní logiku**. Můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a provádět jakékoli další pracovní postupy.

![Diagram výměny deklarací služby RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>Volání služby RESTful

Interakce zahrnuje výměnu deklarací informací mezi deklaracemi rozhraní REST API a Azure AD B2C. Integraci se službami RESTful můžete navrhnout následujícími způsoby:

- **Ověření technického profilu**. Volání služby RESTful probíhá v rámci [ověření technického profilu](validation-technical-profile.md) určeného [samoobslužného technického profilu](self-asserted-technical-profile.md)nebo [ověřovacího ovládacího prvku](display-control-verification.md) [displeje](display-controls.md). Technický profil ověření ověří data zajišťovaná uživatelem dříve, než se cesta uživatele posune vpřed. S technickým profilem ověření můžete:

  - Odesílat deklarace identity do rozhraní REST API.
  - Ověřte deklarace identity a vyvolat vlastní chybové zprávy, které jsou zobrazeny uživateli.
  - Odeslat zpět deklarace z rozhraní REST API na další kroky orchestrace.

- **Výměna pohledávek**. Přímou výměnu deklarací lze nakonfigurovat voláním technického profilu rozhraní REST API přímo z kroku orchestrace [cesty uživatele](userjourneys.md). Tato definice je omezena na:

  - Odesílat deklarace identity do rozhraní REST API.
  - Ověřte deklarace identity a vyvolat vlastní chybové zprávy, které jsou vráceny do aplikace.
  - Odeslat zpět deklarace z rozhraní REST API na další kroky orchestrace.

Můžete přidat volání rozhraní REST API v libovolném kroku v cestě uživatele definované vlastní zásady. Můžete například volat rozhraní REST API:

- Během přihlášení, těsně před Azure AD B2C ověří přihlašovací údaje.
- Ihned po přihlášení.
- Před Azure AD B2C vytvoří nový účet v adresáři.
- Po Azure AD B2C vytvoří nový účet v adresáři.
- Než Azure AD B2C vydá přístupový token.

![Shromažďování technických profilů ověření](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Odesílání dat

V [technickém profilu RESTful](restful-technical-profile.md)obsahuje `InputClaims` prvek seznam deklarací, které je třeba odeslat vaší službě RESTful. Název deklarace identity můžete namapovat na název definovaný ve službě RESTful, nastavit výchozí hodnotu a použít [překladače deklarací .](claim-resolver-overview.md)

Můžete nakonfigurovat, jak jsou vstupní deklarace odesílány zprostředkovateli deklarací RESTful pomocí atributu SendClaimsIn. Možné hodnoty jsou:

- **Tělo**, odeslané v těle požadavku HTTP POST ve formátu JSON.
- **Formulář**, odeslaný v těle požadavku HTTP POST ve formátu hodnoty klíče odděleného & ampersandu .)
- **Hlavička**, odeslaná v hlavičce požadavku HTTP GET.
- **QueryString**, odeslaný v řetězci dotazu požadavku HTTP GET.

Když je nakonfigurována možnost **Tělo,** technický profil rozhraní REST API umožňuje odeslat komplexní datovou část JSON do koncového bodu. Další informace naleznete [v tématu Send a JSON payload](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Příjem dat

`OutputClaims` Prvek [technického profilu RESTful](restful-technical-profile.md) obsahuje seznam deklarací vrácených rozhraním REST API. Možná budete muset namapovat název deklarace deklarace definované ve vaší zásadě na název definovaný v rozhraní REST API. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity rozhraní REST API, pokud nastavíte atribut DefaultValue.

Výstupní deklarace analyzované poskytovatelem deklarací RESTful vždy očekávají, že budou analyzovat plochou odpověď JSON Body, například:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Výstupní deklarace by měly vypadat takto:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Chcete-li analyzovat vnořené JSON body odpověď, nastavte ResolveJsonPathsInJsonTokens metadata true. Ve výstupní deklaraci nastavte Typ partnerclaimtype na prvek cesty JSON, který chcete navýstupu.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


Výstupní deklarace by měly vypadat takto:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Je nutné chránit koncový bod rozhraní REST API tak, aby s ním mohli komunikovat pouze ověření klienti. Rozhraní REST API musí používat koncový bod HTTPS. Nastavte metadata AuthenticationType na jednu z následujících metod ověřování:

- **Klientský certifikát** omezuje přístup pomocí ověřování klientského certifikátu. K vašemu rozhraní API mají přístup pouze služby, které mají příslušné certifikáty. Klientský certifikát uložíte do klíče zásad Azure AD B2C. Další informace o [zabezpečení služby RESTful pomocí klientských certifikátů](secure-rest-api.md#https-client-certificate-authentication).
- **Základní** zabezpečení rozhraní REST API pomocí základního ověřování HTTP. K vašemu rozhraní API mají přístup jenom ověření uživatelé, včetně Azure AD B2C. Uživatelské jméno a heslo jsou uložené v klíčích zásad Azure AD B2C. Zjistěte, jak [zabezpečit služby RESTful pomocí základního ověřování HTTP](secure-rest-api.md#http-basic-authentication).
- **Nosič** omezuje přístup pomocí přístupového tokenu Klienta OAuth2. Přístupový token je uložený v klíči zásad Azure AD B2C. Další informace o tom, jak [zabezpečit službu RESTful pomocí tokenu Nosiče](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Platforma REST API
Vaše rozhraní REST API může být založeno na libovolné platformě a napsáno v libovolném programovém jazyce, pokud je bezpečné a může odesílat a přijímat nároky, jak je uvedeno v [technickém profilu RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Lokalizovat rozhraní REST API
V technickém profilu RESTful můžete chtít odeslat jazyk nebo národní prostředí aktuální relace a v případě potřeby vyvolat lokalizovanou chybovou zprávu. Pomocí [překládání deklarací](claim-resolver-overview.md)identity můžete odeslat kontextovou deklaraci identity, například uživatelský jazyk. Následující příklad ukazuje technický profil RESTful, který tento scénář prokazuje.

```XML
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Zpracování chybových zpráv

Rozhraní REST API může vyžadovat vrácení chybové zprávy, například "Uživatel nebyl v systému CRM nalezen". Pokud dojde k chybě, rozhraní REST API by měl vrátit chybovou zprávu HTTP 409 (Kód stavu konfliktní odpovědi). Další informace naleznete v [technickém profilu restful](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message).

Toho lze dosáhnout pouze voláním technického profilu rozhraní REST API z technického profilu ověření. To umožňuje uživateli opravit data na stránce a spustit ověření znovu po odeslání stránky.

Odpověď HTTP 409 je vyžadována, aby se zabránilo zpracování všech následných ověření technických profilů v rámci tohoto kroku orchestrace.

Pokud odkazujete na technický profil rozhraní REST API přímo z cesty uživatele, uživatel je přesměrován zpět do aplikace předávající strany s příslušnou chybovou zprávou.

## <a name="publishing-your-rest-api"></a>Publikování rozhraní REST API

Požadavek na službu rozhraní REST API pochází ze serverů Azure AD B2C. Služba rozhraní REST API musí být publikována do veřejně přístupného koncového bodu HTTPS. Volání rozhraní REST API dorazí z IP adresy datového centra Azure.

Navrhněte službu ROZHRANÍ REST API a její základní součásti (například databázi a systém souborů), aby byly vysoce dostupné.

## <a name="next-steps"></a>Další kroky

Příklady použití technického profilu RESTful naleznete v následujících článcích:

- [Návod: Integrace serveru REST API pro deklarace identity v cestě uživatele Azure AD B2C jako ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md)
- [Návod: Přidání serveru REST API pro deklarace identity do vlastních zásad ve službě Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpečení služeb rozhraní REST API](secure-rest-api.md)
- [Reference: Technický profil RESTful](restful-technical-profile.md)