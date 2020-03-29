---
title: Zobrazit odkaz na ovládací prvek
titleSuffix: Azure AD B2C
description: Odkaz na ovládací prvky zobrazení Azure AD B2C. Pomocí ovládacích prvků zobrazení můžete přizpůsobit cesty uživatelů definované ve vašich vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188728"
---
# <a name="display-controls"></a>Zobrazit ovládací prvky

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Ovládací prvek zobrazení** je prvek uživatelského rozhraní, který má speciální funkce a spolupracuje s back-endovou službou Azure Active Directory B2C (Azure AD B2C). Umožňuje uživateli provádět akce na stránce, které vyvolávají [ověření technický profil](validation-technical-profile.md) na back-end. Ovládací prvky zobrazení jsou zobrazeny na stránce a jsou odkazovány [vlastním technickým profilem](self-asserted-technical-profile.md).

Následující obrázek znázorňuje samostatně uplatněnou registrační stránku se dvěma ovládacími prvky zobrazení, které ověřují primární a sekundární e-mailovou adresu.

![Příklad vykresleného ovládacího prvku zobrazení](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Požadavky

 V části [Metadata](self-asserted-technical-profile.md#metadata) [samoobslužného technického profilu](self-asserted-technical-profile.md)musí být `DataUri` odkazovaná [definice obsahu](contentdefinitions.md) nastavena na stránku verze smlouvy 2.0.0 nebo vyšší. Například:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definování ovládacích prvků zobrazení

Element **DisplayControl** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor, který se používá pro ovládací prvek zobrazení. To může být [odkazoval se](#referencing-display-controls). |
| Typ ovládacího prvku userInterface | Ano | Typ ovládacího prvku zobrazení. Aktuálně podporován je [VerificationControl](display-control-verification.md) |

Element **DisplayControl** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Vstupní deklarace | 0:1 | **InputClaims** se používají k předběžnému vyplnění hodnoty deklarací, které mají být shromažďovány od uživatele. |
| Zobrazit deklarace identity | 0:1 | **DisplayClaims** se používají k reprezentaci deklarací, které mají být shromažďovány od uživatele. |
| OutputClaims | 0:1 | **OutputClaims** se používají k reprezentaci deklarací, které mají být dočasně uloženy pro tento **DisplayControl**. |
| Akce | 0:1 | **Akce** se používají k zobrazení seznamu technických profilů ověření, které mají vyvolat pro akce uživatelů, které se dějí na front-endu. |

### <a name="input-claims"></a>Vstupní deklarace

V ovládacím prvku zobrazení můžete pomocí prvků **InputClaims** předem vyplnit hodnotu deklarací, které mají být inkasovány od uživatele na stránce. Všechny **InputClaimsTransformace** lze definovat v self-tvrdil technický profil, který odkazuje na tento ovládací prvek zobrazení.

Následující příklad předem vyplní e-mailovou adresu, která má být ověřena, s již uvedenou adresou.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Zobrazit nároky

Každý typ ovládacího prvku zobrazení vyžaduje jinou sadu deklarací zobrazení, [výstupních deklarací](#output-claims)a [akcí, které](#display-control-actions) mají být provedeny.

Podobně jako **deklarace identity zobrazení** definované v [samoobslužném technickém profilu](self-asserted-technical-profile.md#display-claims)představují deklarace zobrazení nároky, které mají být shromážděny od uživatele v rámci ovládacího prvku zobrazení. Název **prvku ClaimType,** na který odkazuje, musí určit element **UserInputType** pro typ vstupu `TextBox` `DropdownSingleSelect`uživatele podporovaný azure ad b2c, například nebo . Pokud akce vyžaduje hodnotu **nároku**zobrazení , `true` nastavte atribut **Required** tak, aby uživatel zadával hodnotu pro tuto konkrétní deklaraci zobrazení.

Pro určité typy ovládacího prvku zobrazení jsou vyžadovány určité nároky na zobrazení. Například **VerificationCode** je vyžadován pro ovládací prvek zobrazení typu **VerificationControl**. Pomocí atributu **ControlClaimType** určete, který displayclaim je určen pro tuto požadovanou deklaraci. Například:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Výstupní pohledávky

**Výstupní deklarace** ovládacího prvku zobrazení nejsou odesílány do dalšího kroku orchestrace. Jsou dočasně uloženy pouze pro aktuální relaci ovládacího prvku zobrazení. Tyto dočasné deklarace identity lze sdílet mezi různými akcemi stejného ovládacího prvku zobrazení.

Chcete-li bublina do výstupní deklarace na další krok orchestrace, použijte **OutputClaims** skutečné vlastní uplatněný technický profil, který odkazuje na tento ovládací prvek zobrazení.

### <a name="display-control-actions"></a>Zobrazit akce ovládacího prvku

**Akce** ovládacího prvku zobrazení jsou procedury, ke kterým dochází v back-endu Azure AD B2C, když uživatel provede určitou akci na straně klienta (prohlížeč). Například ověření provést, když uživatel vybere tlačítko na stránce.

Akce definuje seznam **ověření technické profily**. Používají se pro ověření některých nebo všech deklarací zobrazení ovládacího prvku zobrazení. Ověření technický profil ověří vstup uživatele a může vrátit chybu uživateli. Můžete použít **ContinueOnError**, **ContinueOnSuccess**a **Preconditions** v akci ovládacího prvku zobrazení podobně jako ve způsobu, jakým jsou používány v [ověření technické profily](validation-technical-profile.md) v samostatně uplatněný technický profil.

Následující příklad odešle kód v e-mailu nebo SMS na základě výběru deklarace **mfaType** uživatelem.

```XML
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

## <a name="referencing-display-controls"></a>Odkazování na ovládací prvky zobrazení

Ovládací prvky zobrazení jsou odkazovány v [zobrazení tvrzení](self-asserted-technical-profile.md#display-claims) [self-tvrdil technický profil](self-asserted-technical-profile.md).

Například:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
