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
ms.date: 03/10/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8d3343838216522abfc11ec3f202ae2da1c0e38f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611874"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu s vlastním uplatněním v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Všechny interakce v Azure Active Directory B2C (Azure AD B2C), kde se očekává, že uživatel zadává vstup, jsou technické profily s vlastním uplatněním. Například stránku pro registraci, přihlašovací stránku nebo stránku pro resetování hesla.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C, pro samoobslužné vyhodnocení: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje technický profil s vlastním uplatněním pro registraci e-mailu:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Vstupní deklarace identity

V technickém profilu s vlastním oceněním můžete použít prvky **InputClaims** a **InputClaimsTransformations** k předvyplnění hodnoty deklarací identity, které se zobrazí na stránce s vlastním kontrolním jménem (Zobrazit deklarace identity). Například v zásadě upravit zásadu profilace uživatele nejprve načte profil uživatele z adresářové služby Azure AD B2C, potom technický profil s vlastním uplatněním nastaví vstupní deklarace identity s uživatelskými daty uloženými v profilu uživatele. Tyto deklarace se shromažďují z profilu uživatele a pak se zobrazí uživateli, který pak může upravovat stávající data.

```xml
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

Pořadí deklarací v **DisplayClaims** určuje pořadí, ve kterém Azure AD B2C vykreslí deklarace na obrazovce. Chcete-li vynutit, aby uživatel poskytoval hodnotu pro konkrétní deklaraci identity, nastavte **požadovaný** atribut prvku **DisplayClaim** na hodnotu `true` .

Element **ClaimType** v kolekci **DisplayClaims** musí nastavit element **UserInputType** na libovolný typ vstupu uživatele podporovaný Azure AD B2C. Příkladem je `TextBox` nebo `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Přidat odkaz na ovládací prvek.

V části Zobrazit kolekci deklarací můžete zahrnout odkaz na [ovládací prvek](display-controls.md) zobrazení, který jste vytvořili. Ovládací prvek zobrazení je prvek uživatelského rozhraní, který má zvláštní funkce a komunikuje s Azure AD B2C back-end službou. Umožňuje uživateli provádět akce na stránce, která vyvolá technický profil ověření na back-endu. Například ověřte e-mailovou adresu, telefonní číslo nebo věrnostní číslo zákazníka.

Následující příklad `TechnicalProfile` znázorňuje použití deklarace zobrazení s ovládacími prvky zobrazení.

* První deklarace identity zobrazení vytvoří odkaz na `emailVerificationControl` ovládací prvek zobrazení, který shromažďuje a ověřuje e-mailovou adresu.
* Pátá deklarace zobrazení vytvoří odkaz na `phoneVerificationControl` ovládací prvek zobrazení, který shromáždí a ověří telefonní číslo.
* Ostatní zobrazované deklarace identity jsou ClaimTypes, které se mají shromáždit od uživatele.

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

Jak bylo zmíněno, zobrazení deklarace s odkazem na ovládací prvek zobrazení může spustit vlastní ověřování, například ověření e-mailové adresy. Kromě toho Stránka s vlastním kontrolním jménem podporuje použití technického profilu ověření k ověření celé stránky, včetně uživatelského vstupu (typy deklarací nebo ovládacích prvků zobrazení), před přechodem k dalšímu kroku orchestrace.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Kombinovat využití deklarací zobrazení a deklarací výstupních událostí pečlivě

Pokud zadáte jeden nebo více **DisplayClaim** prvků v technickém profilu s vlastním uplatněním, musíte použít DisplayClaim pro *každou* deklaraci identity, kterou chcete zobrazit na obrazovce a shromažďovat ji od uživatele. Technický profil, který obsahuje alespoň jednu deklaraci identity zobrazení, nezobrazuje žádné deklarace výstupu.

Vezměte v úvahu následující příklad, ve kterém `age` je deklarace identity definovaná jako **výstupní** deklarace identity v základní zásadě. Před přidáním všech zobrazených deklarací do technického profilu s vlastním uplatněním `age` se deklarace identity zobrazí na obrazovce pro shromažďování dat od uživatele:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Pokud koncová zásada, která dědí tuto základnu, následně určí `officeNumber` jako deklaraci **zobrazení** :

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

`age`Deklarace identity v základních zásadách už není na obrazovce k dispozici uživateli – je to efektivně "skryté". Pokud chcete zobrazit `age` deklaraci identity a shromažďovat věkovou hodnotu od uživatele, musíte přidat `age` **DisplayClaim**.

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity, které se mají vrátit do dalšího kroku orchestrace. Atribut **DefaultValue** se uplatní pouze v případě, že deklarace identity nebyla nikdy nastavena. Pokud byl nastaven v předchozím kroku orchestrace, výchozí hodnota se neprojeví i v případě, že uživatel opustí hodnotu prázdnou. Chcete-li vynutit použití výchozí hodnoty, nastavte atribut **AlwaysUseDefaultValue** na hodnotu `true` .

Z bezpečnostních důvodů je hodnota deklarace hesla ( `UserInputType` nastavená na `Password` ) dostupná jenom pro technické profily ověřování technického profilu s vlastním uplatněním. V dalších krocích orchestrace nelze použít deklaraci hesla. 

> [!NOTE]
> V předchozích verzích architektury IEF (identity Experience Framework) byly pro shromažďování dat od uživatele použity výstupní deklarace identity. K shromažďování dat od uživatele použijte místo toho kolekci **DisplayClaims** .

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="when-you-should-use-output-claims"></a>Pokud byste měli použít výstupní deklarace identity

V technickém profilu s vlastním výkonem vrátí kolekce deklarací identity následující krok orchestrace.

Použijte výstupní deklarace identity v těchto případech:

- **Deklarace identity jsou výstupní transformací deklarací výstupů**.
- **Nastavení výchozí hodnoty ve výstupní deklaraci** , aniž by bylo potřeba shromažďovat data od uživatele nebo vracet data z technického profilu ověření. `LocalAccountSignUpWithLogonEmail`Technický profil s vlastním uplatněním nastavuje deklaraci **spuštěnou na SelfAsserted-Input** na `true` .
- **Technický profil ověření vrací deklarace identity** – váš technický profil může zavolat technický profil ověření, který vrací některé deklarace identity. Je možné, že budete chtít deklarace identity a vrátit je k dalším krokům orchestrace v cestě uživatele. Například když se přihlašujete pomocí místního účtu, technický profil s vlastním uplatněním s názvem `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověření s názvem `login-NonInteractive` . Tento technický profil ověří přihlašovací údaje uživatele a také vrátí profil uživatele. Například "userPrincipalName", "DisplayName", "dodaný" a "příjmení".
- **Ovládací prvek zobrazení vrací výstupní deklarace** – váš technický profil může mít odkaz na [ovládací prvek zobrazení](display-controls.md). Ovládací prvek zobrazení vrátí některé deklarace identity, například ověřenou e-mailovou adresu. Je možné, že budete chtít deklarace identity a vrátit je k dalším krokům orchestrace v cestě uživatele. Funkce řízení zobrazení je nyní ve **verzi Preview**.

Následující příklad ukazuje použití technického profilu s vlastním uplatněním, který používá zobrazení deklarací identity a výstupní deklarace identity.

```xml
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

### <a name="output-claims-sign-up-or-sign-in-page"></a>Výstupní stránka pro registraci a přihlášení deklarací identity

Na stránce s kombinovaným registrací a přihlášením si všimněte následujícího při použití prvku definice obsahu [DataUri](contentdefinitions.md#datauri) , který určuje `unifiedssp` `unifiedssd` typ stránky nebo:

- Vykreslí se jenom deklarace identity uživatelského jména a hesla.
- První dvě výstupní deklarace identity musí být uživatelské jméno a heslo (v tomto pořadí). 
- Žádné další deklarace identity se nevykreslují. u těchto deklarací musíte nastavit `defaultValue` nebo vyvolat technický profil ověření formuláře deklarací identity. 

## <a name="persist-claims"></a>Zachovat deklarace identity

Element PersistedClaims se nepoužívá. Technický profil s vlastním uplatněním neuchovává data Azure AD B2C. Místo toho je provedeno volání na technický profil ověření, který je zodpovědný za uchování dat. Například zásada registrace používá `LocalAccountSignUpWithLogonEmail` ke shromáždění nového profilu uživatele technický profil s vlastním uplatněním. `LocalAccountSignUpWithLogonEmail`Technický profil volá technický profil ověření pro vytvoření účtu v Azure AD B2C.

## <a name="validation-technical-profiles"></a>Technické profily ověřování

Pro ověření některých nebo všech výstupních deklarací odkazujícího technického profilu se používá ověřovací technický profil. Vstupní deklarace technického profilu ověření se musí objevit ve výstupních deklaracích technického profilu s vlastním uplatněním. Technický profil ověření ověřuje vstup uživatele a může uživateli vrátit chybu.

Technický profil ověření může být jakýkoliv technický profil v zásadách, například [Azure Active Directory](active-directory-technical-profile.md) nebo [REST API](restful-technical-profile.md) technickými profily. V předchozím příkladu `LocalAccountSignUpWithLogonEmail` technický profil ověří, že signinName v adresáři neexistuje. Pokud ne, technický profil ověření vytvoří místní účet a vrátí objectId, authenticationSource, Nový_uživatel. `SelfAsserted-LocalAccountSignin-Email`Technický profil volá k ověření `login-NonInteractive` přihlašovacích údajů uživatele technický profil ověřování.

Pomocí obchodní logiky můžete také volat REST API technický profil, přepsat vstupní deklarace identity nebo rozšířit uživatelská data další integrací s podnikovou obchodní aplikací. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md) .

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| nastavení. operatingMode <sup>1</sup>| No | Pro přihlašovací stránku Tato vlastnost řídí chování pole username, jako je například ověřování vstupu a chybové zprávy. Očekávané hodnoty: `Username` nebo `Email` .  |
| AllowGenerationOfClaimsWithNullValues| No| Povoluje generování deklarace identity s hodnotou null. Například v případě, že uživatel nevybere zaškrtávací políčko.|
| ContentDefinitionReferenceId | Yes | Identifikátor [definice obsahu](contentdefinitions.md) přidruženého k tomuto technickému profilu. |
| EnforceEmailVerification | No | Pro registraci nebo úpravy profilu vynutilo ověřování e-mailů. Možné hodnoty: `true` (výchozí), nebo `false` . |
| nastavení. retryLimit | No | Určuje počet pokusů, kolikrát se uživatel může pokusit zadat data, která jsou zkontrolována na technický profil ověření. Uživatel se například pokusí zaregistrovat pomocí účtu, který už existuje, a pokračuje v tom, dokud nedosáhne limitu.
| SignUpTarget <sup>1</sup>| No | Identifikátor cílového Exchange registrace. Když uživatel klikne na tlačítko pro registraci, Azure AD B2C spustí zadaný identifikátor Exchange. |
| nastavení. showCancelButton | No | Zobrazí tlačítko Storno. Možné hodnoty: `true` (výchozí), nebo `false` |
| nastavení. showContinueButton | No | Zobrazí tlačítko pokračovat. Možné hodnoty: `true` (výchozí), nebo `false` |
| nastavení. showSignupLink <sup>2</sup>| No | Zobrazí tlačítko pro registraci. Možné hodnoty: `true` (výchozí), nebo `false` |
| nastavení. forgotPasswordLinkLocation <sup>2</sup>| No| Zobrazí odkaz zapomenuté heslo. Možné hodnoty: `AfterLabel` (výchozí) zobrazí odkaz přímo po popisku nebo po poli zadání hesla, když není k dispozici popisek, zobrazuje odkaz  `AfterInput` za polem zadání hesla, `AfterButtons` zobrazuje odkaz v dolní části formuláře za tlačítky nebo `None` odebere odkaz na Zapomenuté heslo.|
| nastavení. enableRememberMe <sup>2</sup>| No| Zobrazí zaškrtávací políčko [zůstat přihlášeni](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) . Možné hodnoty: `true` , nebo `false` (výchozí). |
| nastavení. inputVerificationDelayTimeInMilliseconds <sup>3</sup>| No| Zlepšuje činnost koncového uživatele čekáním, až uživatel přestane psát, a pak hodnotu ověří. Výchozí hodnota je 2000 milisekund. |
| IncludeClaimResolvingInClaimsHandling  | No | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |
|forgotPasswordLinkOverride <sup>4</sup>| No | Provede se výměna deklarací resetování hesla. Další informace najdete v tématu [Samoobslužné resetování hesla](add-password-reset-policy.md). |

Poznámky:
1. K dispozici pro [DataUri](contentdefinitions.md#datauri) typ definice obsahu `unifiedssp` nebo `unifiedssd` .
1. K dispozici pro [DataUri](contentdefinitions.md#datauri) typ definice obsahu `unifiedssp` nebo `unifiedssd` . [Rozložení stránky verze](page-layout.md) 1.1.0 a vyšší.
1. K dispozici pro [rozložení stránky verze](page-layout.md) 1.2.0 a vyšší.
1. K dispozici pro [DataUri](contentdefinitions.md#datauri) typ definice obsahu `unifiedssp` . [Rozložení stránky verze](page-layout.md) 2.1.2 a vyšší.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** se nepoužívá.
