---
title: Definování technického profilu s vlastním uplatněním v vlastní zásadě
titleSuffix: Azure AD B2C
description: V Azure Active Directory B2C ve vlastních zásadách definujte technický profil s vlastním uplatněním.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e0a282be9b8a20c64cd3e74e7860a289baa5aec6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183801"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu s vlastním uplatněním v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Všechny interakce v Azure Active Directory B2C (Azure AD B2C), kde se očekává, že uživatel zadává vstup, jsou technické profily s vlastním uplatněním. Například stránku pro registraci, přihlašovací stránku nebo stránku pro resetování hesla.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `Proprietary`. Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C, pro použití s vlastním kontrolním výrazem: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil s vlastním uplatněním pro registraci e-mailu:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Vstupní deklarace identity

V technickém profilu s vlastním oceněním můžete použít prvky **InputClaims** a **InputClaimsTransformations** k předvyplnění hodnoty deklarací identity, které se zobrazí na stránce s vlastním kontrolním jménem (Zobrazit deklarace identity). Například v zásadě upravit zásadu profilace uživatele nejprve načte profil uživatele z adresářové služby Azure AD B2C, potom technický profil s vlastním uplatněním nastaví vstupní deklarace identity s uživatelskými daty uloženými v profilu uživatele. Tyto deklarace se shromažďují z profilu uživatele a pak se zobrazí uživateli, který pak může upravovat stávající data.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Zobrazit deklarace

Funkce Zobrazit deklarace identity je aktuálně ve **verzi Preview**.

Element **DisplayClaims** obsahuje seznam deklarací, které mají být zobrazeny na obrazovce pro shromažďování dat od uživatele. Chcete-li předem naplnit hodnoty pro zobrazení deklarací identity, použijte vstupní deklarace, které byly dříve popsány. Element může obsahovat také výchozí hodnotu.

Pořadí deklarací v **DisplayClaims** určuje pořadí, ve kterém Azure AD B2C vykreslí deklarace na obrazovce. Pokud chcete vynutit, aby uživatel poskytoval hodnotu pro konkrétní deklaraci identity, nastavte **požadovaný** atribut prvku **DisplayClaim** na `true`.

Element **ClaimType** v kolekci **DisplayClaims** musí nastavit element **UserInputType** na libovolný typ vstupu uživatele podporovaný Azure AD B2C. Například `TextBox` nebo `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Přidat odkaz na ovládací prvek.

V části Zobrazit kolekci deklarací můžete zahrnout odkaz na [ovládací prvek](display-controls.md) zobrazení, který jste vytvořili. Ovládací prvek zobrazení je prvek uživatelského rozhraní, který má zvláštní funkce a komunikuje s Azure AD B2C back-end službou. Umožňuje uživateli provádět akce na stránce, která vyvolá technický profil ověření na back-endu. Například ověřte e-mailovou adresu, telefonní číslo nebo věrnostní číslo zákazníka.

Následující příklad `TechnicalProfile` ukazuje použití deklarace zobrazení s ovládacími prvky zobrazení.

* První deklarace identity zobrazí odkaz na ovládací prvek zobrazení `emailVerificationControl`, který shromažďuje a ověřuje e-mailovou adresu.
* Pátá deklarace zobrazení vytvoří odkaz na ovládací prvek zobrazení `phoneVerificationControl`, který shromažďuje a ověřuje telefonní číslo.
* Ostatní zobrazované deklarace identity jsou ClaimTypes, které se mají shromáždit od uživatele.

```XML
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

Jak bylo zmíněno, zobrazení deklarace s odkazem na ovládací prvek zobrazení může spustit vlastní ověřování, například ověření e-mailové adresy. Kromě toho Stránka s vlastním kontrolním jménem podporuje použití technického profilu ověření k ověření celé stránky, včetně uživatelského vstupu (typy deklarací nebo ovládacích prvků zobrazení), před přechodem k dalšímu kroku orchestrace.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Kombinovat využití deklarací zobrazení a deklarací výstupních událostí pečlivě

Pokud zadáte jeden nebo více **DisplayClaim** prvků v technickém profilu s vlastním uplatněním, musíte použít DisplayClaim pro *každou* deklaraci identity, kterou chcete zobrazit na obrazovce a shromažďovat ji od uživatele. Technický profil, který obsahuje alespoň jednu deklaraci identity zobrazení, nezobrazuje žádné deklarace výstupu.

Vezměte v úvahu následující příklad, ve kterém je deklarace identity `age` definovaná jako **výstupní** deklarace identity v základní zásadě. Před přidáním všech zobrazených deklarací do technického profilu s vlastním uplatněním se na obrazovce zobrazí deklarace `age` pro shromažďování dat od uživatele:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Pokud koncová zásada, která dědí tuto základnu, následně určí `officeNumber` jako **zobrazení** deklarace identity:

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Deklarace `age` v základních zásadách už není na obrazovce k dispozici pro uživatele – je to efektivně "skryté". Pokud chcete zobrazit `age` deklaraci identity a shromažďovat věkovou hodnotu od uživatele, musíte přidat `age` **DisplayClaim**.

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity, které se mají vrátit do dalšího kroku orchestrace. Atribut **DefaultValue** se uplatní pouze v případě, že deklarace identity nebyla nikdy nastavena. Pokud byl nastaven v předchozím kroku orchestrace, výchozí hodnota se neprojeví i v případě, že uživatel opustí hodnotu prázdnou. Chcete-li vynutit použití výchozí hodnoty, nastavte atribut **AlwaysUseDefaultValue** na hodnotu `true`.

> [!NOTE]
> V předchozích verzích architektury IEF (identity Experience Framework) byly pro shromažďování dat od uživatele použity výstupní deklarace identity. K shromažďování dat od uživatele použijte místo toho kolekci **DisplayClaims** .

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="when-you-should-use-output-claims"></a>Pokud byste měli použít výstupní deklarace identity

V technickém profilu s vlastním výkonem vrátí kolekce deklarací identity následující krok orchestrace.

Výstupní deklarace identity by měly být použity v těchto případech:

- **Deklarace identity jsou výstupní transformací deklarací výstupů**.
- **Nastavení výchozí hodnoty ve výstupní deklaraci** , aniž by bylo potřeba shromažďovat data od uživatele nebo vracet data z technického profilu ověření. `LocalAccountSignUpWithLogonEmail` technický profil s vlastním uplatněním nastavuje deklaraci **SelfAsserted-Input** na `true`.
- **Technický profil ověření vrací deklarace identity** – váš technický profil může zavolat technický profil ověření, který vrací některé deklarace identity. Je možné, že budete chtít deklarace identity a vrátit je k dalším krokům orchestrace v cestě uživatele. Například když se přihlašujete pomocí místního účtu, technický profil s názvem `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověřování s názvem `login-NonInteractive`. Tento technický profil ověří přihlašovací údaje uživatele a také vrátí profil uživatele. Například "userPrincipalName", "DisplayName", "dodaný" a "příjmení".
- **Ovládací prvek zobrazení vrací výstupní deklarace** – váš technický profil může mít odkaz na [ovládací prvek zobrazení](display-controls.md). Ovládací prvek zobrazení vrátí některé deklarace identity, například ověřenou e-mailovou adresu. Je možné, že budete chtít deklarace identity a vrátit je k dalším krokům orchestrace v cestě uživatele. Funkce řízení zobrazení je nyní ve **verzi Preview**.

Následující příklad ukazuje použití technického profilu s vlastním uplatněním, který používá zobrazení deklarací identity a výstupní deklarace identity.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Zachovat deklarace identity

Pokud chybí element **PersistedClaims** , technický profil s vlastním uplatněním neuchovává data Azure AD B2C. Místo toho je provedeno volání na technický profil ověření, který je zodpovědný za uchování dat. Například zásada registrace používá ke shromáždění nového profilu uživatele `LocalAccountSignUpWithLogonEmail` technický profil s vlastním uplatněním. Technický profil `LocalAccountSignUpWithLogonEmail` volá technický profil ověření pro vytvoření účtu v Azure AD B2C.

## <a name="validation-technical-profiles"></a>Technické profily ověřování

Pro ověření některých nebo všech výstupních deklarací odkazujícího technického profilu se používá ověřovací technický profil. Vstupní deklarace technického profilu ověření se musí objevit ve výstupních deklaracích technického profilu s vlastním uplatněním. Technický profil ověření ověřuje vstup uživatele a může uživateli vrátit chybu.

Technický profil ověření může být jakýkoliv technický profil v zásadách, například [Azure Active Directory](active-directory-technical-profile.md) nebo [REST API](restful-technical-profile.md) technickými profily. V předchozím příkladu `LocalAccountSignUpWithLogonEmail` technický profil ověří, že signinName v adresáři neexistuje. Pokud ne, technický profil ověření vytvoří místní účet a vrátí objectId, authenticationSource, Nový_uživatel. Technický profil `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověření `login-NonInteractive` a ověří přihlašovací údaje uživatele.

Pomocí obchodní logiky můžete také volat REST API technický profil, přepsat vstupní deklarace identity nebo rozšířit uživatelská data další integrací s podnikovou obchodní aplikací. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md) .

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| nastavení. operatingMode <sup>1</sup>| Ne | Pro přihlašovací stránku Tato vlastnost řídí chování pole username, jako je například ověřování vstupu a chybové zprávy. Očekávané hodnoty: `Username` nebo `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Ne| Povoluje generování deklarace identity s hodnotou null. Například v případě, že uživatel nevybere zaškrtávací políčko.|
| ContentDefinitionReferenceId | Ano | Identifikátor [definice obsahu](contentdefinitions.md) přidruženého k tomuto technickému profilu. |
| EnforceEmailVerification | Ne | Pro registraci nebo úpravy profilu vynutilo ověřování e-mailů. Možné hodnoty: `true` (výchozí) nebo `false`. |
| nastavení. retryLimit | Ne | Určuje počet pokusů, kolikrát se uživatel může pokusit zadat data, která jsou zkontrolována na technický profil ověření. Uživatel se například pokusí zaregistrovat pomocí účtu, který už existuje, a pokračuje v tom, dokud nedosáhne limitu.
| SignUpTarget <sup>1</sup>| Ne | Identifikátor cílového Exchange registrace. Když uživatel klikne na tlačítko pro registraci, Azure AD B2C spustí zadaný identifikátor Exchange. |
| setting.showCancelButton | Ne | Zobrazí tlačítko Storno. Možné hodnoty: `true` (výchozí) nebo `false` |
| setting.showContinueButton | Ne | Zobrazí tlačítko pokračovat. Možné hodnoty: `true` (výchozí) nebo `false` |
| nastavení. showSignupLink <sup>2</sup>| Ne | Zobrazí tlačítko pro registraci. Možné hodnoty: `true` (výchozí) nebo `false` |
| nastavení. forgotPasswordLinkLocation <sup>2</sup>| Ne| Zobrazí odkaz zapomenuté heslo. Možné hodnoty: `AfterInput` (výchozí) odkaz se zobrazí v dolní části stránky, nebo `None` odebere odkaz zapomenuté heslo.|
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true`nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true`. |

Poznámky:
1. K dispozici pro typ [DataUri](contentdefinitions.md#datauri) definice obsahu `unifiedssp`nebo `unifiedssd`.
1. K dispozici pro typ [DataUri](contentdefinitions.md#datauri) definice obsahu `unifiedssp`nebo `unifiedssd`. [Rozložení stránky verze](page-layout.md) 1.1.0 a vyšší.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** se nepoužívá.
