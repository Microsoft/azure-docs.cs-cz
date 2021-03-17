---
title: Ověření deklarací identity s ovládacími prvky zobrazení
titleSuffix: Azure AD B2C
description: Naučte se používat ovládací prvky zobrazení Azure AD B2C k ověření deklarací identity uživatelů poskytovaných vašimi vlastními zásadami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd90be6d93dc5ca399ac87daba0ca44fa7e88ff8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94532490"
---
# <a name="verification-display-control"></a>Ovládací prvek zobrazení ověřování

Pomocí ověřovacího [ovládacího prvku pro zobrazení](display-controls.md) ověřte deklaraci identity, například e-mailovou adresu nebo telefonní číslo s ověřovacím kódem odesílaným uživateli.

## <a name="verificationcontrol-actions"></a>Akce VerificationControl

Ovládací prvek zobrazení ověřování se skládá ze dvou kroků (akce):

1. Vyžádejte od uživatele cíl, jako je e-mailová adresa nebo telefonní číslo, na které se má ověřovací kód odeslat. Když uživatel vybere tlačítko pro **odeslání kódu** , spustí se **Akce SendCode** ovládacího prvku pro zobrazení ověření. **Akce SendCode** vygeneruje kód, sestaví obsah, který se má odeslat, a pošle ho uživateli. Hodnota adresy může být předem vyplněná a sloužit jako druhá-Factor Authentication.

    ![Příklad stránky pro akci odeslat kód](media/display-control-verification/display-control-verification-email-action-01.png)

1. Po odeslání kódu uživatel přečte zprávu, zadá ověřovací kód do ovládacího prvku, který je zobrazen ovládacím prvkem zobrazení, a vybere příkaz **ověřit kód**. Výběrem možnosti **ověřit kód** se spustí **Akce VerifyCode** , která ověří kód přidružený k adrese. Pokud uživatel vybere **Odeslat nový kód** , první akce se provede znovu.

    ![Příklad stránky pro ověření akce kódu](media/display-control-verification/display-control-verification-email-action-02.png)

## <a name="verificationcontrol-required-elements"></a>VerificationControl vyžadované prvky

**VerificationControl** musí obsahovat následující prvky:

- Typ `DisplayControl` je `VerificationControl` .
- `DisplayClaims`
  - **Odeslat do** -jednu nebo více deklarací, které určují, kam má být ověřovací kód odeslán. Například *e-mail* nebo *kód země* a *telefonní číslo*.
  - **Ověřovací kód** – deklarace ověřovacího kódu, kterou uživatel poskytne po odeslání kódu. Tato deklarace identity musí být nastavená na hodnotu požadováno a `ControlClaimType` musí být nastavená na `VerificationCode` .
- Výstupní deklarace identity (volitelné), která se má vrátit na samoobslužnou stránku, až uživatel dokončí proces ověření. Například *e-mail* nebo *kód země* a *telefonní číslo*. Technický profil s vlastním uplatněním používá deklarace identity k uchování dat nebo k bublinám výstupních deklarací do dalšího kroku orchestrace.
- Dvě `Action` s s následujícími názvy:
  - **SendCode** – pošle uživateli kód. Tato akce obvykle obsahuje dva technické profily ověření, vygeneruje kód a pošle ho.
  - **VerifyCode** – ověřuje kód. Tato akce obvykle obsahuje jeden technický profil ověření.

V následujícím příkladu se na stránce zobrazí textové pole **e-mailu** . Když uživatel zadá svou e-mailovou adresu a vybere **SendCode** , aktivuje se akce **SendCode** v back-endu Azure AD B2C.

Pak uživatel zadá **verificationCode** a vybere **VerifyCode** , který aktivuje akci **VerifyCode** v back-endu. Pokud jsou všechna ověření splněná, **VerificationControl** se považuje za kompletní a uživatel může pokračovat k dalšímu kroku.

```xml
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
