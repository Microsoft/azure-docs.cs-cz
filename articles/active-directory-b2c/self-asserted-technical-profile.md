---
title: Definování profilu s vlastním potvrzením technické vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definování profilu s vlastním potvrzením technické vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7a783e496b7bb6fcdf2c80247baad2bfc901d857
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850990"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování profilu s vlastním potvrzením technické ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Všechny interakce v Azure Active Directory (Azure AD) B2C, kde se očekává zadání uživatele se svým s prohlašovanou technické profily. Stránku pro přihlášení, přihlašovací stránka nebo heslo obnovit stránku.

## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut **protokol** elementu musí být nastavena na `Proprietary`. **Obslužná rutina** atribut musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C pro samoobslužné uplatněna: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje registrace s vlastním potvrzením technický profil e-mailu:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Vstupní deklarace identity

V technickém profilu s vlastním potvrzením, můžete použít **InputClaims** a **InputClaimsTransformations** prvků, které se předvyplní hodnota deklarace identity, které se zobrazují na stránce s vlastním potvrzením (výstup deklarace identity). Například v zásady úprav profilu cesty uživatele nejprve čte profilu uživatele z adresáře služby Azure AD B2C a potom s vlastním potvrzením technický profil nastaví mezi vstupními deklaracemi identity s uživatelskými daty uložených v profilu uživatele. Tyto deklarace jsou shromážděná z profilu uživatele a pak budou zobrazena uživateli, který potom můžete upravit existující data.

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


## <a name="output-claims"></a>Výstupní deklarace identit.

**OutputClaims** prvek obsahuje seznam deklarací identity, které se budou zobrazovat ke shromažďování dat od uživatele. Předem výstupní deklarace identit se některé hodnoty, použijte mezi vstupními deklaracemi identity, které bylo popsáno dříve. Element může také obsahovat výchozí hodnotu. Pořadí deklarace identity v **OutputClaims** určuje pořadí, že Azure AD B2C vykreslí deklarace identity na obrazovce. **DefaultValue** atribut se projeví pouze v případě, že deklarace identity nikdy byl nastaven před. Ale pokud byla nastavena než v předchozím kroku Orchestrace, i když uživatel opustí prázdná hodnota, výchozí hodnota se neprojeví. Chcete-li vynutit použití výchozí hodnoty, nastavte **AlwaysUseDefaultValue** atribut `true`. Přinutit uživatele k zadání hodnoty pro konkrétní výstupní deklaraci identity, nastavte **vyžaduje** atribut **OutputClaims** elementu `true`.

**Typu deklarace identity** prvek **OutputClaims** kolekce je potřeba nastavit **UserInputType** element s žádným uživatelem vstupní typ podporovaný službou Azure AD B2C, jako je například `TextBox`nebo `DropdownSingleSelect`. Nebo **OutputClaim** elementu musí nastavit **DefaultValue**.  

**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.

Následující výstupní deklarací je vždycky nastavený na `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Případ použití

Existují čtyři scénáře pro výstupní deklarace identit:

- **Shromažďování výstupu deklarace identity od uživatele** – když je potřeba shromáždit informace od uživatele, jako je například datum narození, měli byste přidat deklaraci do **OutputClaims** kolekce. Musíte zadat deklarace, které se budou zobrazovat uživateli **UserInputType**, jako například `TextBox` nebo `DropdownSingleSelect`. Obsahuje-li s vlastním potvrzením technický profil technický profil na ověřování, jejichž výstupem jsou stejnou deklaraci identity, nepředstavuje deklaraci identity pro uživatele Azure AD B2C. Pokud není žádná žádné výstupní deklarace pro konkrétního uživatele, Azure AD B2C přeskočí technický profil.
- **Nastavení výchozí hodnoty v deklaraci výstupu** – bez shromažďování dat od uživatele nebo vrácení dat z technického profilu ověření. `LocalAccountSignUpWithLogonEmail` Držitelem s prohlašovanou sady technický profil **provést zadání SelfAsserted** deklaraci identity pro `true`.
- **Technický profil ověření vrátí výstupní deklarace identit** – technický profil může volat ověření technický profil, který vrátí některé deklarace identity. Můžete vyvolat deklarace identity a vrátíte se k dalším krokům Orchestrace v cestě uživatele. Například při přihlašování pomocí místní účet, s vlastním potvrzením technický profil s názvem `SelfAsserted-LocalAccountSignin-Email` volá technický profil ověřování s názvem `login-NonInteractive`. Tento technický profil ověří přihlašovací údaje uživatele a také vrátí hodnotu profilu uživatele. Například "userPrincipalName", "displayName", "jméno" a "Příjmení".
- **Výstupní deklarace identity pomocí transformace deklarací identity výstupu**

V následujícím příkladu `LocalAccountSignUpWithLogonEmail` držitelem s prohlašovanou technický profil demonstruje použití výstupní deklarace identit a sadách **provést zadání SelfAsserted** k `true`. `objectId`, `authenticationSource`, `newUser` Deklarace identity jsou výstup `AAD-UserWriteUsingLogonEmail` ověření technické profilu a uživateli se nezobrazují.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Zachovat deklarací identity

Pokud **PersistedClaims** chybí element, s vlastním potvrzením technický profil neuchovává data do Azure AD B2C. Ověření technický profil, který je zodpovědné za trvalost dat místo toho je provedeno volání. Například používá zásady registrace `LocalAccountSignUpWithLogonEmail` držitelem s prohlašovanou technický profil ke shromažďování nový uživatelský profil. `LocalAccountSignUpWithLogonEmail` Technický profil volá ověření technický profil k vytvoření účtu v Azure AD B2C.

## <a name="validation-technical-profiles"></a>Ověření technické profily

Technický profil ověření se používá pro některé nebo všechny výstupní deklarace identit odkazující technického profilu. Ve výstupu deklarace identity s vlastním potvrzením technického profilu musí být uvedena mezi vstupními deklaracemi identity technického profilu ověření. Technický profil ověření ověřuje vstup uživatele a může vrátit chybu uživateli. 

Technický profil ověření může být jakékoli technický profil v zásadách, například [Azure Active Directory](active-directory-technical-profile.md) nebo [rozhraní REST API](restful-technical-profile.md) technické profily. V předchozím příkladu `LocalAccountSignUpWithLogonEmail` technický profil ověří, signinName neexistuje v adresáři. Pokud ne, technický profil ověření vytvoří místní účet a vrátí ID objektu, authenticationSource, newUser. `SelfAsserted-LocalAccountSignin-Email` Technický profil volání `login-NonInteractive` technický profil ověření pro ověření pověření uživatele.

Můžete také volat rozhraní REST API technický profil s obchodní logikou, přepsat vstupních deklarací identity nebo další integrace pomocí podnikové aplikace – obchodní Obohaťte uživatelská data. Další informace najdete v tématu [technický profil ověření](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| setting.showContinueButton | Ne | Zobrazí tlačítko Pokračovat. Možné hodnoty: `true` (výchozí), nebo `false` |
| setting.showCancelButton | Ne | Zobrazí tlačítko Storno. Možné hodnoty: `true` (výchozí), nebo `false` |
| setting.operatingMode | Ne | Pro přihlašovací stránku tato vlastnost řídí chování pole uživatelské jméno, například ověření vstupu a chybové zprávy. Očekávané hodnoty: `Username` nebo `Email`. |
| ContentDefinitionReferenceId | Ano | Identifikátor [obsahu definice](contentdefinitions.md) přidružené k této technický profil. |
| EnforceEmailVerification | Ne | Pro zápis nebo úpravy profilu, vynucuje ověření e-mailu. Možné hodnoty: `true` (výchozí), nebo `false`. | 
| setting.showSignupLink | Ne | Zobrazí tlačítko pro zápis. Možné hodnoty: `true` (výchozí), nebo `false` |
| setting.retryLimit | Ne | Určuje, kolikrát uživatel mohli zkusit poskytnout data, která bude zkontrolován, technický profil ověření. Například uživatel pokusí zaregistrovat pomocí účtu, který již existuje a udržuje zkusit až do dosažení limitu.
| SignUpTarget | Ne | Identifikátor cílové exchange registrace. Když uživatel klikne na tlačítko zaregistrovat, Azure AD B2C spustí zadaný exchange identifikátor. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** není použit element.













