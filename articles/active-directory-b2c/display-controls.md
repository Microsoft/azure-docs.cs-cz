---
title: Odkaz na ovládací prvek zobrazení
titleSuffix: Azure AD B2C
description: Referenční informace pro ovládací prvky zobrazení Azure AD B2C Použijte ovládací prvky zobrazení pro přizpůsobení cest uživatele definovaných ve vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387050"
---
# <a name="display-controls"></a>Ovládací prvky zobrazení

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Ovládací prvek zobrazení** je prvek uživatelského rozhraní, který má zvláštní funkce a komunikuje s back-end službou Azure Active Directory B2C (Azure AD B2C). Umožňuje uživateli provádět akce na stránce, která vyvolá [technický profil ověření](validation-technical-profile.md) na back-endu. Ovládací prvky zobrazení jsou zobrazeny na stránce a jsou odkazovány [technickým profilem s vlastním uplatněním](self-asserted-technical-profile.md).

Následující obrázek znázorňuje přihlašovací stránku s vlastním uplatněním se dvěma ovládacími prvky zobrazení, které ověřují primární a sekundární e-mailovou adresu.

![Příklad ovládacího prvku vykresleného zobrazení](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Požadavky

 V části [metadata](self-asserted-technical-profile.md#metadata) [technického profilu s vlastním](self-asserted-technical-profile.md)přístavem musí být odkazovaný [ContentDefinition](contentdefinitions.md) `DataUri` nastaven na stránku verze kontraktu 2.0.0 nebo vyšší. Například:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definování ovládacích prvků zobrazení

Element **zobrazitelné ovládací** prvky obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Yes | Identifikátor, který se používá pro ovládací prvek zobrazení. Lze na něj [odkazovat](#referencing-display-controls). |
| UserInterfaceControlType | Yes | Typ ovládacího prvku zobrazení Aktuálně se podporuje [VerificationControl](display-control-verification.md) |

Element **Zobrazit ovládací** prvek obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** slouží k předvyplnění hodnoty deklarací, které se mají shromažďovat od uživatele. Další informace naleznete v tématu [InputClaims](technicalprofiles.md#input-claims) element. |
| DisplayClaims | 0:1 | **DisplayClaims** slouží k reprezentaci deklarací, které se mají shromažďovat od uživatele. Další informace naleznete v tématu [DisplayClaim](technicalprofiles.md#displayclaim) element.|
| OutputClaims | 0:1 | **OutputClaims** slouží k reprezentaci deklarací, které se dočasně ukládají pro tento **ovládací prvek** zobrazení. Další informace naleznete v tématu [OutputClaims](technicalprofiles.md#output-claims) element.|
| Akce | 0:1 | **Akce** se používají k vypsání technických profilů ověřování pro vyvolání uživatelských akcí, které probíhají na front-endu. |

### <a name="input-claims"></a>Vstupní deklarace identity

V ovládacím prvku zobrazení můžete použít prvky **InputClaims** k předvyplnění hodnoty deklarace identity, které se mají shromažďovat od uživatele na stránce. Libovolný **InputClaimsTransformations** může být definován v technickém profilu s vlastním uplatněním, který odkazuje na tento ovládací prvek zobrazení.

Následující příklad předem naplní e-mailovou adresu, která se má ověřit s adresou, která již existuje.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Zobrazit deklarace

Každý typ ovládacího prvku zobrazení vyžaduje jinou sadu deklarací zobrazení, [výstupních deklarací identity](#output-claims)a [akcí](#display-control-actions) , které mají být provedeny.

Podobně jako u **deklarací zobrazení** definovaných v [technickém profilu s vlastním uplatněním](self-asserted-technical-profile.md#display-claims)jsou deklarace zobrazení reprezentovány deklaracemi, které se mají shromáždit uživateli v rámci ovládacího prvku zobrazení. Element **ClaimType** , na který je odkazováno, musí specifikovat element **UserInputType** pro uživatelský typ vstupu, který podporuje Azure AD B2C, například `TextBox` nebo `DropdownSingleSelect` . Pokud **Akce** vyžaduje zobrazení hodnoty deklarace identity, nastavte **požadovaný** atribut tak, aby `true` uživateli vynutil zadání hodnoty pro danou konkrétní deklaraci zobrazení.

Pro určité typy ovládacího prvku zobrazení jsou vyžadovány určité deklarace zobrazení. Například **VerificationCode** je nutné pro zobrazení ovládacího prvku typu **VerificationControl**. Pomocí atributu **ControlClaimType** určete, který DisplayClaim je určený pro požadovanou deklaraci identity. Například:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Deklarace výstupů

**Výstupní deklarace** ovládacího prvku zobrazení nejsou odesílány dalšímu kroku orchestrace. Ukládají se dočasně jenom pro aktuální relaci ovládacího prvku zobrazení. Tyto dočasné deklarace identity lze sdílet mezi různými akcemi stejného ovládacího prvku zobrazení.

Chcete-li zobrazit výstupní deklarace identity k dalšímu kroku orchestrace, použijte **OutputClaims** vlastního technického profilu s vlastním uplatněním, který odkazuje na tento ovládací prvek zobrazení.

### <a name="display-control-actions"></a>Akce ovládacího prvku zobrazení

**Akce** ovládacího prvku zobrazení jsou procedury, ke kterým dochází v Azure AD B2C back-endu, když uživatel provede určitou akci na straně klienta (prohlížeč). Například ověření, které se má provést, když uživatel vybere na stránce tlačítko.

Akce definuje seznam **technických profilů ověření**. Používají se k ověřování některých nebo všech zobrazení deklarací ovládacího prvku zobrazení. Technický profil ověření ověřuje vstup uživatele a může uživateli vrátit chybu. V akci ovládacího prvku zobrazení můžete použít **ContinueOnError**, **ContinueOnSuccess** a **předběžné podmínky** podobné způsobu, jakým jsou použity v technických profilech [ověření](validation-technical-profile.md) v rámci kontrolního technického profilu.

#### <a name="actions"></a>Akce

Element **Actions** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Akce | 1: n | Seznam akcí, které mají být provedeny. |

#### <a name="action"></a>Akce

Element **Action** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Yes | Typ operace. Možné hodnoty: `SendCode` nebo `VerifyCode` . `SendCode`Hodnota pošle uživateli kód. Tato akce může obsahovat dva technické profily ověřování: jednu pro vygenerování kódu a jednu pro odeslání. `VerifyCode`Hodnota ověří kód, který uživatel zadal ve vstupním textovém poli. |

Element **Action** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | Identifikátory technických profilů, které se používají k ověření některých nebo všech zobrazených deklarací identity s referenčním technickým profilem. Všechny vstupní deklarace odkazovaného technického profilu se musí zobrazit v deklaracích, které odkazují na technický profil. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

Element **ValidationClaimsExchange** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Technický profil, který se má použít k ověření některých nebo všech zobrazených deklarací identity s referenčním technickým profilem. |

Element **ValidationTechnicalProfile** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Identifikátor technického profilu, který je už definovaný v zásadách nebo v nadřazené zásadě. |
|ContinueOnError|No| Určuje, jestli se má v případě, že bude tento technický profil ověřování zobrazovat chyba, ověřit, jestli má ověřování všech následných technických profilů. Možné hodnoty: `true` nebo `false` (výchozí, zpracování dalších profilů ověřování se zastaví a vrátí se chyba). |
|ContinueOnSuccess | No | Určuje, zda má ověřování všech následných ověřovacích profilů pokračovat v případě úspěšného ověření tohoto technického profilu. Možné hodnoty: `true` nebo `false` . Výchozí hodnota je `true` , což znamená, že zpracování dalších profilů ověření bude pokračovat. |

Element **ValidationTechnicalProfile** obsahuje následující element:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Předběžné podmínky | 0:1 | Seznam předpokladů, které musí být splněny, aby byl technický profil ověření proveden. |

Prvek **předběžné podmínky** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ kontroly nebo dotazu, který má být proveden pro předběžnou podmínku. Možné hodnoty: `ClaimsExist` nebo `ClaimEquals` . `ClaimsExist` Určuje, že akce by se měly provádět v případě, že zadané deklarace existují v aktuální sadě deklarací uživatele. `ClaimEquals` Určuje, že akce mají být provedeny, pokud existuje zadaná deklarace identity a její hodnota je rovna zadané hodnotě. |
| `ExecuteActionsIf` | Yes | Určuje, zda mají být provedeny akce v předběžné podmínce, pokud je test nastaven na hodnotu true nebo false. |

Prvek **podmínky** obsahuje následující prvky:

| Prvek | Výskytů | Description |
| ------- | ----------- | ----------- |
| Hodnota | 1: n | Data, která se používají při kontrole. Pokud je typ této kontroly `ClaimsExist` , toto pole určuje ClaimTypeReferenceId k dotazování na. Pokud je typ kontroly `ClaimEquals` , toto pole určuje ClaimTypeReferenceId k dotazování na. Zadejte hodnotu, kterou chcete zkontrolovat v jiném hodnotovém prvku.|
| Akce | 1:1 | Akce, která má být provedena, pokud je splněna podmínka kontroly předběžných podmínek v rámci kroku orchestrace. Hodnota **Akce** je nastavena na hodnotu `SkipThisValidationTechnicalProfile` , která určuje, zda by neměl být proveden příslušný technický profil ověření. |

Následující příklad odešle a ověří e-mailovou adresu pomocí [technického profilu Azure AD SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

Následující příklad odešle kód v e-mailu nebo SMS na základě výběru deklarace identity **mfaType** s využitím předběžných podmínek.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Odkazy na ovládací prvky zobrazení

Na ovládací prvky zobrazení se odkazuje ve [zobrazení deklarací](self-asserted-technical-profile.md#display-claims) [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md).

Například:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Další kroky

Ukázky použití ovládacího prvku zobrazení najdete v těchto tématech: 

- [Vlastní ověření e-mailu pomocí Mailjet](custom-email-mailjet.md)
- [Vlastní ověření e-mailu pomocí SendGrid](custom-email-sendgrid.md)
