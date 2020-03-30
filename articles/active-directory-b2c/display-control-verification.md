---
title: Ověření deklarací pomocí ovládacích prvků zobrazení
titleSuffix: Azure AD B2C
description: Zjistěte, jak pomocí ovládacích prvků zobrazení Azure AD B2C ověřit deklarace identity v cestách uživatelů poskytovaných vlastními zásadami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188779"
---
# <a name="verification-display-control"></a>Ovládací prvek ověření zobrazení

Pomocí [ovládacího prvku](display-controls.md) zobrazení ověření ověřte deklaraci, například e-mailovou adresu nebo telefonní číslo, s ověřovacím kódem odeslaným uživateli.

## <a name="verificationcontrol-actions"></a>Ověřovacířídicí akce

Ovládací prvek ověřovacího zobrazení se skládá ze dvou kroků (akcí):

1. Vyžádejte si od uživatele cíl, například e-mailovou adresu nebo telefonní číslo, na které má být ověřovací kód odeslán. Když uživatel vybere tlačítko **Odeslat kód,** spustí se **akce SendCode** ovládacího prvku ověřovacího zobrazení. **Akce SendCode** vygeneruje kód, vytvoří obsah, který má být odeslán, a odešle jej uživateli. Hodnota adresy může být předem vyplněna a sloužit jako ověřování druhého faktoru.

    ![Příklad stránky pro akci odeslat kód](media/display-control-verification/display-control-verification-email-action-01.png)

1. Po odeslání kódu uživatel přečte zprávu, zadá ověřovací kód do ovládacího prvku poskytovaného ovládacím prvkem zobrazení a vybere **možnost Ověřit kód**. Výběrem **možnosti Ověřit kód**se provede **akce VerifyCode** za účelem ověření kódu přidruženého k adrese. Pokud uživatel vybere **odeslat nový kód**, první akce se provede znovu.

    ![Ukázková stránka pro ověření akce kódu](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>OvěřovacíKontrolní požadované prvky

**Kontrola ověření** musí obsahovat následující prvky:

- Typ `DisplayControl` je `VerificationControl`.
- `DisplayClaims`
  - **Odeslat do** - Jeden nebo více deklarací, které specifikují, kam se má ověřovací kód odeslat. Například *e-mail* nebo *kód země* a *telefonní číslo*.
  - **Ověřovací kód** – tvrzení ověřovacího kódu, které uživatel zadává po odeslání kódu. Tato deklarace musí být nastavena `ControlClaimType` podle `VerificationCode`potřeby a musí být nastavena na .
- Výstupní deklarace (nepovinné), která má být vrácena na stránku se dekobovaným uživatelem po dokončení procesu ověření uživatelem. Například *e-mail* nebo *kód země* a *telefonní číslo*. Vlastní uplatněný technický profil používá deklarace identity k zachování dat nebo bublině do výstupních deklarací až do dalšího kroku orchestrace.
- Dva `Action`s s následujícími názvy:
  - **SendCode** - Odešle kód uživateli. Tato akce obvykle obsahuje dva ověření technický profil, generovat kód a odeslat jej.
  - **VerifyCode** - Ověří kód. Tato akce obvykle obsahuje jeden technický profil ověření.

V níže uvedeném příkladu se na stránce zobrazí textové pole **e-mailu.** Když uživatel zadá svou e-mailovou adresu a vybere **SendCode**, akce **SendCode** se aktivuje v back-endu Azure AD B2C.

Potom uživatel zadá **ověřovací kód** a vybere **VerifyCode** pro aktivaci akce **VerifyCode** v back-endu. Pokud všechna ověření projít, **VerificationControl** je považován za dokončený a uživatel může pokračovat k dalšímu kroku.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
