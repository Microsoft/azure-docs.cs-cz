---
title: Technické profily
titleSuffix: Azure AD B2C
description: Zadejte element TechnicalProfiles vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631791"
---
# <a name="technical-profiles"></a>Technické profily

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

*Technický profil* poskytuje rozhraní s integrovaným mechanismem pro komunikaci s různými typy stran. Technické profily se používají ke komunikaci s vaším klientem Azure Active Directory B2C (Azure AD B2C) k vytvoření uživatele nebo čtení profilu uživatele. Technický profil může být vlastním uplatněním, aby bylo možné interakci s uživatelem. Technický profil může například shromažďovat přihlašovací údaje uživatele pro přihlášení a pak vykreslovat stránku pro registraci nebo resetování hesla.

## <a name="types-of-technical-profiles"></a>Typy technických profilů

Technický profil umožňuje tyto typy scénářů:

- [Application Insights](analytics-with-application-insights.md): odesílá data události do [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure AD](active-directory-technical-profile.md): poskytuje podporu pro Azure AD B2C správu uživatelů.
- Vícefaktorové [ověřování Azure AD](multi-factor-auth-technical-profile.md): poskytuje podporu pro ověření telefonního čísla pomocí vícefaktorového ověřování Azure AD.
- [Transformace deklarací identity](claims-transformation-technical-profile.md): volá výstupní transformace deklarací identity za účelem manipulace s hodnotami deklarací identity, ověřování deklarací identity nebo nastavení výchozích hodnot pro sadu výstupních deklarací.
- [Pomocný parametr tokenu ID](id-token-hint.md): ověří `id_token_hint` podpis tokenu JWT, název vystavitele a cílovou skupinu tokenů a extrahuje deklaraci identity z příchozího tokenu.
- [Vystavitel tokenu JWT](jwt-issuer-technical-profile.md): vygeneruje token JWT, který se vrátí zpět do aplikace předávající strany.
- [OAuth1](oauth1-technical-profile.md): federace s jakýmkoli poskytovatelem identity protokolu OAuth 1,0.
- [OAuth2](oauth2-technical-profile.md): federace s jakýmkoli poskytovatelem identity protokolu OAuth 2,0.
- [Jednorázové heslo](one-time-password-technical-profile.md): poskytuje podporu pro správu generování a ověřování jednorázového hesla.
- [OpenID Connect](openid-connect-technical-profile.md): federace s jakýmkoli poskytovatelem identity protokolu OpenID Connect.
- [Telefonní faktor](phone-factor-technical-profile.md): podporuje registraci a ověření telefonních čísel.
- [Poskytovatel RESTful](restful-technical-profile.md): volá služby REST API Services, jako je ověřování vstupu uživatele, rozšiřování uživatelských dat nebo integrace s podnikovými aplikacemi.
- [Zprostředkovatel identity SAML](identity-provider-generic-saml.md): federace s jakýmkoli poskytovatelem identity protokolu SAML.
- [Vystavitel tokenu SAML](saml-service-provider.md): vygeneruje token SAML, který se vrátí zpátky do aplikace předávající strany.
- Uživatel s [vlastním uplatněním](self-asserted-technical-profile.md): komunikuje s uživatelem. Například shromažďuje přihlašovací údaje uživatele pro přihlášení, vykreslí stránku pro registraci nebo resetování hesla.
- [Správa relací](custom-policy-reference-sso.md): zpracovává různé typy relací.

## <a name="technical-profile-flow"></a>Tok technického profilu

Všechny typy technických profilů sdílejí stejný koncept. Spustí se čtením vstupních deklarací identity a spuštěním transformací deklarací identity. Potom komunikují s nakonfigurovanou stranou, jako je zprostředkovatel identity, REST API nebo adresářové služby Azure AD. Až se proces dokončí, technický profil vrátí výstupní deklarace identity a může spustit výstupní transformace deklarací identity. Následující diagram znázorňuje, jak se zpracovávají transformace a mapování, na které se odkazuje v technickém profilu. Po provedení transformace deklarací identity jsou výstupní deklarace identity okamžitě uložené v kontejneru deklarací identity, bez ohledu na stranu, se kterou technický profil komunikuje.

![Diagram, který znázorňuje tok technického profilu.](./media/technical-profiles/technical-profile-flow.png)

1. **Správa relací jednotného přihlašování (SSO)**: obnoví stav relace technického profilu pomocí [správy relace jednotného](custom-policy-reference-sso.md)přihlašování.
1. **Transformace vstupních deklarací identity**: před zahájením technického profilu Azure AD B2C spustí [transformaci vstupních deklarací](claimstransformations.md).
1. **Vstupní deklarace identity**: deklarace identity se vybírají z kontejneru deklarací identity, které se používají pro technický profil.
1. **Provádění technického profilu**: technický profil vyměňuje deklarace identity s konfigurovanou stranou. Například:
    - Přesměruje uživatele na zprostředkovatele identity, aby bylo možné dokončit přihlášení. Po úspěšném přihlášení se uživatel vrátí zpět a provádění technického profilu bude pokračovat.
    - Volá REST API při posílání parametrů jako InputClaims a získávání informací zpět jako OutputClaims.
    - Vytvoří nebo aktualizuje uživatelský účet.
    - Odešle a ověří textovou zprávu vícefaktorového ověřování.
1. **Technické profily ověření**: [technický profil](self-asserted-technical-profile.md) pro ověřování může volat [technické profily ověření](validation-technical-profile.md) k ověření dat profilování uživatelem.
1. **Deklarace výstupů**: deklarace identity se vrátí zpět do kontejneru deklarací identity. Tyto deklarace identity můžete použít v kroku další orchestrace nebo v transformacích s výstupními deklaracemi.
1. **Transformace výstupních deklarací identity**: po dokončení technického profilu Azure AD B2C spustí [transformaci](claimstransformations.md)výstupních deklarací.
1. **Správa relací jednotného přihlašování**: uchovává data technického profilu v relaci pomocí [správy relací jednotného přihlašování](custom-policy-reference-sso.md).

Element **TechnicalProfiles** obsahuje sadu technických profilů podporované zprostředkovatelem deklarací identity. Každý zprostředkovatel deklarací musí mít alespoň jeden technický profil. Technický profil určuje koncové body a protokoly, které jsou potřeba ke komunikaci se zprostředkovatelem deklarací. Zprostředkovatel deklarací může mít více technických profilů.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Element **TechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
|---------|---------|---------|
| Id | Yes | Jedinečný identifikátor technického profilu. Na technický profil se dá odkazovat pomocí tohoto identifikátoru z dalších prvků v souboru zásad. Příklady jsou **OrchestrationSteps** a **ValidationTechnicalProfile**. |

Element **TechnicalProfile** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Doména | 0:1 | Název domény pro technický profil. Pokud například váš technický profil určuje poskytovatele identity Facebooku, název domény je Facebook.com. |
| DisplayName | 1:1 | Zobrazovaný název technického profilu. |
| Description | 0:1 | Popis technického profilu. |
| Protokol | 1:1 | Protokol používaný pro komunikaci s druhou stranou. |
| Metadata | 0:1 | Sada klíčů a hodnot, které řídí chování technického profilu. |
| InputTokenFormat | 0:1 | Formát vstupního tokenu. Možné hodnoty jsou `JSON` , `JWT` , `SAML11` nebo `SAML2` . `JWT`Hodnota představuje JSON web token podle specifikace IETF. `SAML11`Hodnota představuje token zabezpečení SAML 1,1 pro Oasis specifikaci. `SAML2`Hodnota představuje token zabezpečení SAML 2,0 pro Oasis specifikaci. |
| OutputTokenFormat | 0:1 | Formát výstupního tokenu. Možné hodnoty jsou `JSON` , `JWT` , `SAML11` nebo `SAML2` . |
| CryptographicKeys | 0:1 | Seznam kryptografických klíčů, které jsou používány v technickém profilu. |
| InputClaimsTransformations | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací identity, které by měly být provedeny před odesláním jakýchkoli deklarací do zprostředkovatele deklarací nebo předávající strany. |
| InputClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací identity, které jsou pořízeny jako vstup v technickém profilu. |
| PersistedClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které budou zachovány technickým profilem. |
| DisplayClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou prezentovány [technickým profilem s vlastním uplatněním](self-asserted-technical-profile.md). Funkce DisplayClaims je aktuálně ve verzi Preview. |
| OutputClaims | 0:1 | Seznam dříve definovaných odkazů na typy deklarací, které jsou pořízeny jako výstup v technickém profilu. |
| OutputClaimsTransformations | 0:1 | Seznam dříve definovaných odkazů na transformace deklarací identity, které by měly být provedeny po přijetí deklarací identity od zprostředkovatele deklarací identity. |
| ValidationTechnicalProfiles | 0: n | Seznam odkazů na jiné technické profily, které technický profil používá pro účely ověření. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md).|
| SubjectNamingInfo | 0:1 | Řídí produkci názvu subjektu v tokenech, kde je název subjektu zadán nezávisle na deklaracích identity. Příklady jsou OAuth nebo SAML. |
| IncludeInSso | 0:1 | Zda použití tohoto technického profilu by mělo pro relaci použít chování jednotného přihlašování, nebo místo toho vyžadovat explicitní interakci. Tento prvek je platný jenom v profilech SelfAsserted používaných v rámci technického profilu ověření. Možné hodnoty jsou `true` (výchozí) nebo `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Identifikátor technického profilu, ze kterého chcete přidat všechny deklarace identity vstupu a výstupu do tohoto technického profilu. Odkazovaný technický profil musí být definován ve stejném souboru zásad. |
| IncludeTechnicalProfile |0:1 | Identifikátor technického profilu, ze kterého chcete přidat všechna data do tohoto technického profilu. |
| UseTechnicalProfileForSessionManagement | 0:1 | Jiný technický profil, který se má použít pro správu relací. |
|EnabledForUserJourneys| 0:1 |Řídí, jestli se technický profil spustí na cestě uživatele. |

## <a name="protocol"></a>Protokol

Element **Protocol** určuje protokol, který se má použít pro komunikaci s druhou stranou. Element **Protocol** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Název | Yes | Název platného protokolu podporovaného Azure AD B2C, který se používá jako součást technického profilu. Možné hodnoty jsou `OAuth1` , `OAuth2` , `SAML2` , `OpenIdConnect` , `Proprietary` nebo `None` . |
| Obslužná rutina | No | Pokud je název protokolu nastavený na `Proprietary` , určuje název sestavení, které používá Azure AD B2C k určení obslužné rutiny protokolu. |

## <a name="metadata"></a>Metadata

Element **metadata** obsahuje relevantní možnosti konfigurace pro konkrétní protokol. Seznam podporovaných metadat je zdokumentován v odpovídající specifikaci [technického profilu](#types-of-technical-profiles) . Element **metadata** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Položka | 0: n | Metadata vztahující se k technickému profilu. Každý typ technického profilu má jinou sadu položek metadat. Další informace najdete v části typy technických profilů. |

### <a name="item"></a>Položka

Element **Item** elementu **metadata** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Klíč | Yes | Klíč metadat. Seznam položek metadat najdete v každém [typu technického profilu](#types-of-technical-profiles) . |

Následující příklad ilustruje použití metadat relevantních pro [technický profil OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

Následující příklad ilustruje použití metadat relevantních pro [REST API technický profil](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Kryptografické klíče

K navázání vztahu důvěryhodnosti se službami, které integruje, Azure AD B2C ukládá tajné klíče a certifikáty ve formě [klíčů zásad](policy-keys-overview.md). Během provádění technického profilu Azure AD B2C načítá kryptografické klíče z klíčů zásad Azure AD B2C. Pak Azure AD B2C používá klíče k navázání vztahu důvěryhodnosti nebo k šifrování nebo podepsání tokenu. Tyto vztahy důvěryhodnosti se skládají z těchto vztahů:

- Federace s poskytovateli identity [OAuth1](oauth1-technical-profile.md#cryptographic-keys), [OAuth2](oauth2-technical-profile.md#cryptographic-keys)a [SAML](identity-provider-generic-saml.md) .
- Zabezpečení připojení pomocí [služby REST API Services](secure-rest-api.md).
- Podepisování a šifrování tokenů [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) a [SAML](saml-service-provider.md) .

Element **CryptographicKeys** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Klíč | 1: n | Kryptografický klíč použitý v tomto technickém profilu. |

### <a name="key"></a>Klíč

**Klíčový** element obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | No | Jedinečný identifikátor konkrétního páru klíčů, na který se odkazuje z dalších prvků v souboru zásad. |
| StorageReferenceId | Yes | Identifikátorem kontejneru klíčů úložiště, na který odkazuje jiný element v souboru zásad. |

## <a name="input-claims-transformations"></a>Transformace vstupních deklarací identity

Element **InputClaimsTransformations** může obsahovat kolekci vstupních transformačních prvků deklarací identity, které se používají k úpravě vstupních deklarací nebo generování nových.

Výstupní deklarace identity předchozí transformace deklarací identity v kolekci transformací deklarací identity můžou být vstupními deklaracemi následných transformací vstupních deklarací. Tímto způsobem můžete mít posloupnost transformací deklarací identity, které jsou na sobě navzájem závislé.

Element **InputClaimsTransformations** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | Identifikátor transformace deklarací identity, který by měl být proveden před odesláním jakýchkoli deklarací do zprostředkovatele deklarací nebo předávající strany. Transformaci deklarací identity lze použít k úpravě stávajících deklarací ClaimsSchema nebo k vygenerování nových. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Element **InputClaimsTransformation** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor transformace deklarací identity už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

Následující technické profily odkazují na transformaci deklarací identity **CreateOtherMailsFromEmail** . Transformace deklarací přidá hodnotu `email` deklarace identity do `otherMails` kolekce před tím, než se data uloží do adresáře.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** vybírá deklarace identity z kontejneru deklarací identity, které se používají pro technický profil. Například [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) používá vstupní deklarace identity, které vyplní výstupní deklarace identity, které uživatel poskytuje. REST API technický profil používá vstupní deklarace identity k posílání vstupních parametrů do koncového bodu REST API. Azure AD používá ke čtení, aktualizaci nebo odstranění účtu vstupní deklaraci identity jako jedinečný identifikátor.

Element **InputClaims** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | Očekával se vstupní typ deklarace identity. |

### <a name="inputclaim"></a>InputClaim

Element **InputClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identifikátor typu deklarace. Deklarace identity už je definovaná v části schématu deklarací v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která má být použita k vytvoření deklarace identity, pokud deklarace identity uvedená v ClaimTypeReferenceId neexistuje, aby mohla být výsledná deklarace použita jako prvek InputClaim technického profilu. |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace zásad namapován na typ deklarace identity partnera se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Příkladem je, že první název deklarace identity je *zadán*, zatímco partner používá deklaraci s názvem *first_name*. |

## <a name="display-claims"></a>Zobrazit deklarace

Element **DisplayClaims** obsahuje seznam deklarací identity, které se mají zobrazit na obrazovce pro shromažďování dat od uživatele. V kolekci zobrazit deklarace identity můžete zahrnout odkaz na [typ deklarace identity](claimsschema.md) nebo na [ovládací prvek zobrazení](display-controls.md) , který jste vytvořili.

- Typ deklarace identity je odkaz na deklaraci identity, která se má zobrazit na obrazovce.
  - Chcete-li vynutit, aby uživatel poskytoval hodnotu pro konkrétní deklaraci identity, nastavte **požadovaný** atribut prvku **DisplayClaim** na hodnotu `true` .
  - Chcete-li předem naplnit hodnoty pro zobrazení deklarací identity, použijte vstupní deklarace, které byly dříve popsány. Element může obsahovat také výchozí hodnotu.
  - Element **ClaimType** v kolekci **DisplayClaims** musí nastavit element **UserInputType** na libovolný typ vstupu uživatele podporovaný Azure AD B2C. Příklady jsou `TextBox` nebo `DropdownSingleSelect` .
- Ovládací prvek zobrazení je prvek uživatelského rozhraní, který má zvláštní funkce a komunikuje s Azure AD B2C back-end službou. Umožňuje uživateli provádět akce na stránce, která vyvolá technický profil ověření na back-endu. Příklad ověřuje e-mailovou adresu, telefonní číslo nebo věrnostní číslo zákazníka.

Pořadí prvků v **DisplayClaims** určuje pořadí, ve kterém Azure AD B2C vykreslí deklarace na obrazovce.

Element **DisplayClaims** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Očekával se vstupní typ deklarace identity. |

### <a name="displayclaim"></a>DisplayClaim

Element **DisplayClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| DisplayControlReferenceId | No | Identifikátor [ovládacího prvku zobrazení](display-controls.md) , který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Vyžadováno | No | Určuje, zda je požadována deklarace identity zobrazení. |

Následující příklad ilustruje použití zobrazení deklarací identity a zobrazení v technickém profilu s vlastním uplatněním.

![Snímek obrazovky, který zobrazuje technický profil s vlastním uplatněním s deklaracemi identity zobrazení](./media/technical-profiles/display-claims.png)

V následujícím technickém profilu:

- První deklarace identity zobrazení vytvoří odkaz na `emailVerificationControl` ovládací prvek zobrazení, který shromažďuje a ověřuje e-mailovou adresu.
- Pátá deklarace zobrazení vytvoří odkaz na `phoneVerificationControl` ovládací prvek zobrazení, který shromáždí a ověří telefonní číslo.
- Ostatní deklarace identity zobrazení jsou prvky ClaimType, které mají být shromažďovány od uživatele.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

## <a name="persisted-claims"></a>Trvalé deklarace identity

Element **PersistedClaims** obsahuje všechny hodnoty, které by měly být zachovány [technickým profilem Azure AD](active-directory-technical-profile.md) s možnými informacemi o mapování mezi typem deklarace identity, který je už definovaný v části [ClaimsSchema](claimsschema.md) v zásadách a v názvu atributu Azure AD.

Název deklarace identity je název [atributu Azure AD](user-profile-attributes.md) , pokud není zadaný atribut **PartnerClaimType** , který obsahuje název atributu Azure AD.

Element **PersistedClaims** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Typ deklarace identity, který se má zachovat |

### <a name="persistedclaim"></a>PersistedClaim

Element **PersistedClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která se má použít k vytvoření deklarace identity, pokud deklarace identity neexistuje. |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadán atribut PartnerClaimType, je zadaný typ deklarace zásad namapován na typ deklarace identity partnera se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Příkladem je, že první název deklarace identity je *zadán*, zatímco partner používá deklaraci s názvem *first_name*. |

V následujícím příkladu se v nástroji **AAD-UserWriteUsingLogonEmail** Technical Profile nebo [počáteční Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), který vytvoří nový místní účet, přetrvá následující deklarace identity:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** je kolekce deklarací, které se vrátí zpět do kontejneru deklarací identity po dokončení technického profilu. Tyto deklarace identity můžete použít v kroku další orchestrace nebo v transformacích s výstupními deklaracemi. Element **OutputClaims** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1: n | Očekával se výstupní typ deklarace identity. |

### <a name="outputclaim"></a>OutputClaim

Element **OutputClaim** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad nebo v nadřazeném souboru zásad. |
| Hodnot | No | Výchozí hodnota, která se má použít k vytvoření deklarace identity, pokud deklarace identity neexistuje. |
|AlwaysUseDefaultValue |No |Vynutí použití výchozí hodnoty. |
| PartnerClaimType | No | Identifikátor typu deklarace externího partnera, na který se mapuje zadaný typ deklarace identity. Pokud není zadaný atribut typu deklarace identity, namapuje se zadaný typ deklarace identity na partnerský typ deklarace se stejným názvem. Tuto vlastnost použijte v případě, že se název typu deklarace identity liší od druhé strany. Příkladem je, že první název deklarace identity je *zadán*, zatímco partner používá deklaraci s názvem *first_name*. |

## <a name="output-claims-transformations"></a>Transformace výstupních deklarací identity

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** . Výstupní transformace deklarací identity se používají k úpravě výstupních deklarací identity nebo k vygenerování nových. Po spuštění se výstupní deklarace identity vrátí do kontejneru deklarací identity. Tyto deklarace identity můžete použít v dalším kroku orchestrace.

Výstupní deklarace identity předchozí transformace deklarací identity v kolekci transformací deklarací identity můžou být vstupními deklaracemi následných transformací vstupních deklarací. Tímto způsobem můžete mít posloupnost transformací deklarací identity, které jsou na sobě navzájem závislé.

Element **OutputClaimsTransformations** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | Identifikátory transformací deklarací identity, které se mají provést před odesláním deklarací do zprostředkovatele deklarací nebo předávající strany. Transformaci deklarací identity lze použít k úpravě stávajících deklarací ClaimsSchema nebo k vygenerování nových. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Element **OutputClaimsTransformation** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor transformace deklarací identity už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

Následující technický profil odkazuje na transformaci deklarací AssertAccountEnabledIsTrue k vyhodnocení, jestli je účet povolený, nebo ne po přečtení `accountEnabled` deklarace z tohoto adresáře.

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Technické profily ověřování

Technický profil ověřování se používá k ověřování výstupních deklarací v [technickém profilu s vlastním uplatněním](self-asserted-technical-profile.md#validation-technical-profiles). Technický profil ověření je běžný technický profil z libovolného protokolu, jako je například [Azure AD](active-directory-technical-profile.md) nebo [REST API](restful-technical-profile.md). Technický profil ověřování vrátí deklarace výstupů nebo vrátí kód chyby. Chybová zpráva se vykreslí uživateli na obrazovce, který uživateli umožňuje opakovat akci.

Následující diagram znázorňuje, jak Azure AD B2C používá k ověření přihlašovacích údajů uživatele technický profil ověřování.

![Diagram, který zobrazuje tok technického profilu ověření.](./media/technical-profiles/validation-technical-profile.png)

Element **ValidationTechnicalProfiles** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Identifikátory technických profilů, které se používají, ověřují některé nebo všechny výstupní deklarace identity odkazujícího technického profilu. Všechny vstupní deklarace odkazovaného technického profilu se musí objevit ve výstupních deklaracích referenčního technického profilu. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Element **ValidationTechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor technického profilu, který už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

Element **SubjectNamingInfo** definuje název subjektu, který se používá v tokenech v [zásadách předávající strany](relyingparty.md#subjectnaminginfo). Element **SubjectNamingInfo** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ClaimType | Yes | Identifikátor typu deklarace identity, který je už definovaný v oddílu ClaimsSchema v souboru zásad. |

## <a name="include-technical-profile"></a>Zahrnout technický profil

Technický profil může zahrnovat jiný technický profil pro změnu nastavení nebo přidání nových funkcí. Element **IncludeTechnicalProfile** je odkaz na společný technický profil, ze kterého je odvozen technický profil. Pokud chcete snížit redundanci a složitost prvků zásad, použijte zahrnutí, pokud máte více technických profilů, které sdílejí základní prvky. Využijte společný technický profil se společnou sadou konfigurací spolu s konkrétními technickými profily úkolů, které zahrnují společný technický profil.

Předpokládejme, že máte [REST API technický profil](restful-technical-profile.md) s jedním koncovým bodem, kde potřebujete posílat různé sady deklarací pro různé scénáře. Vytvořte společný technický profil se sdílenými funkcemi, jako je REST API identifikátor URI koncového bodu, metadata, typ ověřování a kryptografické klíče. Vytvořte konkrétní technické profily úkolů, které zahrnují společný technický profil. Pak přidejte vstupní a výstupní deklarace identity nebo přepište identifikátor URI koncového bodu REST API relevantního pro daný technický profil.

Element **IncludeTechnicalProfile** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor technického profilu, který už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

Následující příklad ilustruje použití zahrnutí:

- **REST API – společné**: běžný technický profil se základní konfigurací.
- **REST-ValidateProfile**: zahrnuje technický profil **REST-API-Common** a určuje vstupní a výstupní deklarace identity.
- **REST-UpdateProfile**: zahrnuje technický profil **REST-API-Common** , určuje vstupní deklarace identity a Přepisuje `ServiceUrl` metadata.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>Zahrnutí na více úrovní

Technický profil může obsahovat jeden technický profil. Počet úrovní zařazení není nijak omezený. Například technický profil **AAD-UserReadUsingAlternativeSecurityId-inerror** zahrnuje **AAD-UserReadUsingAlternativeSecurityId**. Tento technický profil nastaví `RaiseErrorIfClaimsPrincipalDoesNotExist` položku metadat na `true` a vyvolá chybu, pokud v adresáři neexistuje účet sociální sítě. **AAD-UserReadUsingAlternativeSecurityId-** informování tohoto chování potlačí a zakáže tuto chybovou zprávu.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId** zahrnuje `AAD-Common` technický profil.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Parametr **AAD-UserReadUsingAlternativeSecurityId-inerror** a **AAD-UserReadUsingAlternativeSecurityId** Nezaurčují požadovaný element **protokolu** , protože je zadaný v technickém profilu **AAD-Common** .

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Použití technického profilu pro správu relací

Element **UseTechnicalProfileForSessionManagement** odkazuje na [technický profil relace jednotného přihlašování](custom-policy-reference-sso.md). Element **UseTechnicalProfileForSessionManagement** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor technického profilu, který už je definovaný v souboru zásad nebo v nadřazeném souboru zásad. |

## <a name="enabled-for-user-journeys"></a>Povoleno pro cesty uživatelů

[ClaimsProviderSelections](userjourneys.md#claims-provider-selection) v cestě uživatele definuje seznam možností výběru zprostředkovatele deklarací a jejich pořadí. Pomocí elementu **EnabledForUserJourneys** můžete filtrovat, který zprostředkovatel deklarací identity je k dispozici pro uživatele. Element **EnabledForUserJourneys** obsahuje jednu z následujících hodnot:

- **Always**: spustí technický profil.
- **Nikdy**: přeskočí technický profil.
- **OnClaimsExistence**: provede se jenom v případě, že existuje určitá deklarace identity zadaná v technickém profilu.
- **OnItemExistenceInStringCollectionClaim**: provede se pouze v případě, že položka existuje v deklaraci kolekce řetězců.
- **OnItemAbsenceInStringCollectionClaim**: provede se pouze v případě, že položka v deklaraci kolekce řetězců neexistuje.

Použití **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** nebo **OnItemAbsenceInStringCollectionClaim** vyžaduje, abyste zadali následující metadata:

- **ClaimTypeOnWhichToEnable**: Určuje typ deklarace identity, který se má vyhodnotit.
- **ClaimValueOnWhichToEnable**: Určuje hodnotu, která se má porovnat.

Následující technický profil se spustí pouze v případě, že kolekce řetězců **identityProviders** obsahuje hodnotu `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
