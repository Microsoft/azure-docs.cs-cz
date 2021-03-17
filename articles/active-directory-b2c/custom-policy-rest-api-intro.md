---
title: REST API výměny deklarací identity ve vlastních zásadách B2C
titleSuffix: Azure AD B2C
description: Úvod k vytvoření Azure AD B2C cesty uživatele, která komunikuje s RESTful službami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89594182"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrace REST APIch výměn deklarací identity do vlastních zásad Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Architektura prostředí identity, která je Azure Active Directory B2C (Azure AD B2C), se dá integrovat s rozhraními API RESTful v rámci cesty uživatele. Tento článek ukazuje, jak vytvořit cestu uživatele, která spolupracuje se službou RESTful s využitím [technického profilu RESTful](restful-technical-profile.md).

Pomocí Azure AD B2C můžete přidat vlastní obchodní logiku k cestě uživatele voláním vlastní služby RESTful. Rozhraní identity Experience Framework může odesílat a přijímat data ze služby RESTful k výměně deklarací identity. Můžete například:

- **Ověřte vstupní data uživatele**. Můžete třeba ověřit, že e-mailová adresa zadaná uživatelem existuje v databázi vašeho zákazníka, a pokud ne, zobrazí se chyba.
- **Zpracovat deklarace identity**. Pokud uživatel zadá své křestní jméno do všech malých písmen nebo ze všech velkých písmen, může REST API formátovat název jenom s prvním písmenem velkými písmeny a vrátit ho do Azure AD B2C.
- **Rozšiřte uživatelská data o další integraci firemních obchodních aplikací**. Vaše služba RESTful může obdržet e-mailovou adresu uživatele, zadat dotaz na databázi zákazníka a získat věrnostní číslo uživatele Azure AD B2C. Pak můžete vracet deklarace identity v účtu Azure AD uživatele, vyhodnotit v dalších krocích orchestrace nebo zahrnout do přístupového tokenu.
- **Spusťte vlastní obchodní logiku**. Můžete odesílat nabízená oznámení, aktualizovat podnikové databáze, spouštět proces migrace uživatelů, spravovat oprávnění, auditovat databáze a provádět libovolné další pracovní postupy.

![Diagram výměny deklarací identity služby RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> V případě, že dojde ke zpomalení nebo žádné odezvě služby RESTful na Azure AD B2C, je časový limit 30 sekund a počet opakování je 2 časy (to znamená, že celkový počet pokusů je 3). Nastavení časového limitu a počtu opakování se v tuto chvíli nedají konfigurovat.

## <a name="calling-a-restful-service"></a>Volání služby RESTful

Interakce zahrnuje výměnu deklarací informací mezi REST API deklarací identity a Azure AD B2C. Integraci s RESTful službami můžete navrhovat následujícími způsoby:

- **Technický profil ověření**. Volání služby RESTful se stává v rámci [ověřovacího technického profilu](validation-technical-profile.md) zadaného [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md)nebo [ověřování](display-control-verification.md) ovládacího prvku [zobrazení](display-controls.md). Technický profil ověření ověřuje uživatelem poskytnutá data před tím, než se přesune uživatel do cesty. S technickým profilem ověření můžete:

  - Odešlete deklarace do vašeho REST API.
  - Ověří deklarace identity a vyvolejte vlastní chybové zprávy, které se zobrazí uživateli.
  - Odeslat zpět deklarace z REST API do následujících kroků orchestrace.

- **Výměna deklarací identity**. Přímou výměnu deklarací identity je možné nakonfigurovat voláním REST API technický profil přímo z kroku orchestrace [cesty uživatele](userjourneys.md). Tato definice je omezená na:

  - Odešlete deklarace do vašeho REST API.
  - Ověří deklarace identity a vyvolejte vlastní chybové zprávy, které se vrátí do aplikace.
  - Odeslat zpět deklarace z REST API do následujících kroků orchestrace.

Můžete přidat REST API volání v jakémkoli kroku v cestě uživatele definované vlastními zásadami. Například můžete volat REST API:

- Během přihlašování těsně před Azure AD B2C ověří přihlašovací údaje.
- Ihned po přihlášení.
- Předtím, než Azure AD B2C vytvoří nový účet v adresáři.
- Po Azure AD B2C vytvoří nový účet v adresáři.
- Než Azure AD B2C vydá přístupový token.

![Kolekce Technical Profile ověření](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Odesílání dat

V [technickém profilu RESTful](restful-technical-profile.md) `InputClaims` element obsahuje seznam deklarací, které se mají odeslat službě RESTful. Název deklarace identity můžete namapovat na název definovaný ve službě RESTful, nastavit výchozí hodnotu a použít [překladače deklarací identity](claim-resolver-overview.md).

Můžete nakonfigurovat, jak se vstupní deklarace identity odesílají do zprostředkovatele deklarací RESTful pomocí atributu SendClaimsIn. Možné hodnoty jsou:

- **Tělo**odeslané v těle požadavku HTTP POST ve formátu JSON.
- **Formulář**odeslaný v těle požadavku HTTP POST ve formátu hodnoty "&" oddělených klíčů.
- **Hlavička**, která se odešle v HLAVIČCE požadavku HTTP GET.
- Dotaz **QueryString**odeslaný v řetězci dotazu požadavku HTTP GET.

Když je nakonfigurovaná možnost **tělo** , REST API Technical profil vám umožní poslat do koncového bodu složitou datovou část JSON. Další informace najdete v tématu [Odeslání datové části JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Příjem dat

`OutputClaims`Element [RESTful Technical profil](restful-technical-profile.md) obsahuje seznam deklarací identity vrácených REST API. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v REST API. Můžete také zahrnout deklarace identity, které nevrací poskytovatel REST API identity, pokud nastavíte atribut DefaultValue.

Výstupní deklarace identity, které analyzuje Poskytovatel deklarací RESTful, vždycky očekávají, že by se měla analyzovat nepřímá odpověď těla těla JSON, jako například:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

Deklarace výstupu by měly vypadat takto:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Chcete-li analyzovat vnořenou odpověď těla zprávy JSON, nastavte metadata ResolveJsonPathsInJsonTokens na hodnotu true. Ve výstupní deklaraci identity nastavte PartnerClaimType na element cesty JSON, na který chcete vytvořit výstup.

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
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Je nutné chránit svůj koncový bod REST API tak, aby s ním mohli komunikovat pouze ověření klienti. REST API musí používat koncový bod HTTPS. Nastavte metadata AuthenticationType na jednu z následujících metod ověřování:

- **Certifikát klienta** omezuje přístup pomocí ověřování klientského certifikátu. K vašemu rozhraní API mají přístup jenom služby, které mají příslušné certifikáty. Certifikát klienta uložíte do klíče zásad Azure AD B2C. Přečtěte si další informace o tom, jak [zabezpečit službu RESTful pomocí klientských certifikátů](secure-rest-api.md#https-client-certificate-authentication).
- **Základní** zabezpečuje REST API pomocí ověřování HTTP Basic. K rozhraní API můžou přistupovat jenom ověření uživatelé, včetně Azure AD B2C. Uživatelské jméno a heslo jsou uložené v klíčích zásad Azure AD B2C. Naučte se [zabezpečit služby RESTful pomocí ověřování HTTP Basic](secure-rest-api.md#http-basic-authentication).
- **Nosič** omezuje přístup pomocí přístupového tokenu OAuth2 klienta. Přístupový token je uložený v klíči zásad Azure AD B2C. Přečtěte si další informace o tom, jak [zabezpečit službu RESTful pomocí nosných tokenů](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>REST API platforma
Vaše REST API může být založené na jakékoli platformě a napsané v jakémkoli jazyku programování, pokud je to bezpečné a může odesílat a přijímat deklarace identity, jak je uvedeno v [technickém profilu RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Lokalizovat REST API
V technickém profilu RESTful můžete chtít odeslat jazyk/národní prostředí aktuální relace a v případě potřeby vyvolat lokalizovanou chybovou zprávu. Pomocí [překladače deklarací identity](claim-resolver-overview.md)můžete odeslat kontextovou deklaraci identity, jako je například uživatelský jazyk. Následující příklad ukazuje RESTful technický profil, který demonstruje tento scénář.

```xml
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

## <a name="handling-error-messages"></a>Manipulace s chybovými zprávami

Vaše REST API může potřebovat vrátit chybovou zprávu, například "uživatel nebyl nalezen v systému CRM". Pokud dojde k chybě, REST API by měla vracet chybovou zprávu HTTP 409 (kód stavu odpovědi na konflikt). Další informace najdete v tématu [RESTful Technical Profile](restful-technical-profile.md#returning-validation-error-message).

Toho lze dosáhnout pouze voláním REST API Technical Profile z technického profilu ověření. To umožňuje uživateli opravovat data na stránce a znovu spustit ověřování po odeslání stránky.

Aby se zabránilo zpracování jakýchkoli následných technických profilů ověření v rámci tohoto kroku orchestrace, je vyžadována odpověď HTTP 409.

Pokud odkazujete na REST API technický profil přímo z cesty uživatele, bude uživatel přesměrován zpět do aplikace předávající strany s příslušnou chybovou zprávou.

## <a name="publishing-your-rest-api"></a>Publikování REST API

Požadavek na službu REST API pochází ze serverů Azure AD B2C. Služba REST API musí být publikovaná do veřejně přístupného koncového bodu HTTPS. REST API volání budou přijata z IP adresy datového centra Azure.

Navrhněte svou REST API službu a její základní součásti (například databáze a systém souborů), aby byly vysoce dostupné.

## <a name="next-steps"></a>Další kroky

Příklady použití technického profilu RESTful najdete v následujících článcích:

- [Návod: integrace REST APIch výměn deklarací identity v Azure AD B2C cestě uživatele jako ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md)
- [Návod: Přidání výměn deklarací identity REST API do vlastních zásad v Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Zabezpečení služby REST API Services](secure-rest-api.md)
- [Referenční dokumentace: RESTful Technical Profile](restful-technical-profile.md)
