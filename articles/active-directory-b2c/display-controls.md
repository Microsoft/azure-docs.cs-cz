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
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 131ecd010cba55f08199f713654792c0844a47e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202292"
---
# <a name="display-controls"></a>Ovládací prvky zobrazení

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Ovládací prvek zobrazení** je prvek uživatelského rozhraní, který má zvláštní funkce a komunikuje s back-end službou Azure Active Directory B2C (Azure AD B2C). Umožňuje uživateli provádět akce na stránce, která vyvolá [technický profil ověření](validation-technical-profile.md) na back-endu. Ovládací prvky zobrazení jsou zobrazeny na stránce a jsou odkazovány [technickým profilem s vlastním uplatněním](self-asserted-technical-profile.md).

Následující obrázek znázorňuje přihlašovací stránku s vlastním uplatněním se dvěma ovládacími prvky zobrazení, které ověřují primární a sekundární e-mailovou adresu.

![Příklad ovládacího prvku vykresleného zobrazení](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Předpoklady

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
| Id | Ano | Identifikátor, který se používá pro ovládací prvek zobrazení. Lze na něj [odkazovat](#referencing-display-controls). |
| UserInterfaceControlType | Ano | Typ ovládacího prvku zobrazení Aktuálně se podporuje [VerificationControl](display-control-verification.md) |

Element **Zobrazit ovládací** prvek obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** slouží k předvyplnění hodnoty deklarací, které se mají shromažďovat od uživatele. |
| DisplayClaims | 0:1 | **DisplayClaims** slouží k reprezentaci deklarací, které se mají shromažďovat od uživatele. |
| OutputClaims | 0:1 | **OutputClaims** slouží k reprezentaci deklarací, které se dočasně ukládají pro tento **ovládací prvek**zobrazení. |
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

Podobně jako u **deklarací zobrazení** definovaných v [technickém profilu s vlastním uplatněním](self-asserted-technical-profile.md#display-claims)jsou deklarace zobrazení reprezentovány deklaracemi, které se mají shromáždit uživateli v rámci ovládacího prvku zobrazení. Element **ClaimType** , na který je odkazováno, musí specifikovat element **UserInputType** pro uživatelský typ vstupu, který podporuje Azure AD B2C, například `TextBox` nebo `DropdownSingleSelect` . Pokud **Akce**vyžaduje zobrazení hodnoty deklarace identity, nastavte **požadovaný** atribut tak, aby `true` uživateli vynutil zadání hodnoty pro danou konkrétní deklaraci zobrazení.

Pro určité typy ovládacího prvku zobrazení jsou vyžadovány určité deklarace zobrazení. Například **VerificationCode** je nutné pro zobrazení ovládacího prvku typu **VerificationControl**. Pomocí atributu **ControlClaimType** určete, který DisplayClaim je určený pro požadovanou deklaraci identity. Například:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Deklarace výstupů

**Výstupní deklarace** ovládacího prvku zobrazení nejsou odesílány dalšímu kroku orchestrace. Ukládají se dočasně jenom pro aktuální relaci ovládacího prvku zobrazení. Tyto dočasné deklarace identity lze sdílet mezi různými akcemi stejného ovládacího prvku zobrazení.

Chcete-li zobrazit výstupní deklarace identity k dalšímu kroku orchestrace, použijte **OutputClaims** vlastního technického profilu s vlastním uplatněním, který odkazuje na tento ovládací prvek zobrazení.

### <a name="display-control-actions"></a>Akce ovládacího prvku zobrazení

**Akce** ovládacího prvku zobrazení jsou procedury, ke kterým dochází v Azure AD B2C back-endu, když uživatel provede určitou akci na straně klienta (prohlížeč). Například ověření, které se má provést, když uživatel vybere na stránce tlačítko.

Akce definuje seznam **technických profilů ověření**. Používají se k ověřování některých nebo všech zobrazení deklarací ovládacího prvku zobrazení. Technický profil ověření ověřuje vstup uživatele a může uživateli vrátit chybu. V akci ovládacího prvku zobrazení můžete použít **ContinueOnError**, **ContinueOnSuccess**a **předběžné podmínky** podobné způsobu, jakým jsou použity v technických profilech [ověření](validation-technical-profile.md) v rámci kontrolního technického profilu.

Následující příklad odešle kód v e-mailu nebo SMS na základě výběru deklarace identity **mfaType** uživatele.

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

Příklad:

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
