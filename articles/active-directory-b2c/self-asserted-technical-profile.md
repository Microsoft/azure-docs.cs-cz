---
title: Definování vlastního technického profilu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte vlastní uplatněný technický profil ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332513"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování vlastního technického profilu ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Všechny interakce ve službě Azure Active Directory B2C (Azure AD B2C), kde se očekává, že uživatel poskytne vstup, jsou vlastní technické profily. Například registrační stránka, přihlašovací stránka nebo stránka pro obnovení hesla.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `Proprietary`nastaven na . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, který používá Azure AD B2C, pro vlastní asserted:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Následující příklad ukazuje vlastní uplatněný technický profil pro registraci e-mailu:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Vstupní deklarace

V samoobslužném technickém profilu můžete pomocí elementů **InputClaims** a **InputClaimsTransformations** předem naplnit hodnotu deklarací, které se zobrazují na stránce s vlastním uplatněním (zobrazení deklarací). Například v zásadách upravit profil uživatele cesta nejprve přečte profil uživatele z adresářové služby Azure AD B2C, pak se samoobslužné technický profil nastaví vstupní deklarace s uživatelská data uložená v profilu uživatele. Tyto deklarace identity jsou shromažďovány z profilu uživatele a poté prezentovány uživateli, který pak může upravit existující data.

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

## <a name="display-claims"></a>Zobrazit nároky

Funkce deklarací zobrazení je aktuálně ve **verzi Preview**.

**DisplayClaims** Element obsahuje seznam deklarací, které mají být prezentovány na obrazovce pro sběr dat od uživatele. Chcete-li předem vyplnit hodnoty deklarací zobrazení, použijte vstupní deklarace, které byly dříve popsány. Prvek může také obsahovat výchozí hodnotu.

Pořadí deklarací identity v **DisplayClaims** určuje pořadí, ve kterém Azure AD B2C vykreslí deklarace identity na obrazovce. Chcete-li vynutit, aby uživatel zadával hodnotu pro konkrétní `true`deklaraci, nastavte atribut **Required** prvku **DisplayClaim** na .

**ClaimType** element v **displayclaims** kolekce musí nastavit **UserInputType** element na libovolný typ vstupu uživatele podporované Azure AD B2C. Příkladem je `TextBox` nebo `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Přidání odkazu na ovládací prvek DisplayControl

Do kolekce deklarací zobrazení můžete zahrnout odkaz na [ovládací prvek DisplayControl,](display-controls.md) který jste vytvořili. Ovládací prvek zobrazení je prvek uživatelského rozhraní, který má speciální funkce a spolupracuje s back-endovou službou Azure AD B2C. Umožňuje uživateli provádět akce na stránce, které vyvolávají ověření technický profil na back-end. Například ověření e-mailové adresy, telefonního čísla nebo věrnostního čísla zákazníka.

Následující příklad `TechnicalProfile` ilustruje použití deklarací zobrazení s ovládacími prvky zobrazení.

* První nárok na zobrazení odkazuje `emailVerificationControl` na ovládací prvek zobrazení, který shromažďuje a ověřuje e-mailovou adresu.
* Pátý displej tvrzení odkazuje na `phoneVerificationControl` ovládací prvek zobrazení, který shromažďuje a ověřuje telefonní číslo.
* Ostatní deklarace zobrazení jsou ClaimTypes, které mají být shromažďovány od uživatele.

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

Jak již bylo zmíněno, deklarace zobrazení s odkazem na ovládací prvek zobrazení může spustit vlastní ověření, například ověření e-mailové adresy. Kromě toho stránka s vlastním uplatněním podporuje použití technického profilu ověření k ověření celé stránky, včetně všech uživatelských vstupů (typů deklarací nebo ovládacích prvků zobrazení), než přejde k dalšímu kroku orchestrace.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Pečlivě kombinujte použití deklarací zobrazení a výstupních deklarací

Pokud zadáte jeden nebo více prvků **DisplayClaim** v samoobslužném technickém profilu, musíte použít DisplayClaim pro *každou* deklaraci, kterou chcete zobrazit na obrazovce a shromažďovat od uživatele. Žádné výstupní deklarace jsou zobrazeny vlastním technickým profilem, který obsahuje alespoň jednu deklaraci zobrazení.

Vezměme si následující `age` příklad, ve kterém je deklarace definována jako **deklarace výstupu** v základní zásadě. Před přidáním jakýchkoli nároků na zobrazení do `age` samoobslužného technického profilu se reklamace zobrazí na obrazovce pro sběr dat od uživatele:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Pokud list zásady, které dědí, `officeNumber` že základ následně určuje jako **deklarace zobrazení:**

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

Deklarace `age` v zásadách základní již není na obrazovce prezentována uživateli - je účinně "skrytá". Chcete-li `age` zobrazit deklaraci pohledávky a shromáždit věkovou hodnotu od uživatele, musíte přidat `age` **displayclaim**.

## <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací, které mají být vráceny do dalšího kroku orchestrace. Atribut **DefaultValue** se projeví pouze v případě, že deklarace deklarace byla nikdy nastavena. Pokud byla nastavena v předchozím kroku orchestrace, výchozí hodnota se neprojeví i v případě, že uživatel ponechá hodnotu prázdnou. Chcete-li vynutit použití výchozí hodnoty, nastavte `true`atribut **AlwaysUseDefaultValue** na .

Z bezpečnostních důvodů je hodnota`UserInputType` deklarace hesla (nastavená na) `Password`k dispozici pouze pro technické profily ověření technického profilu, který se sám uplatňuje. V dalších krocích orchestrace nelze použít deklaraci hesla. 

> [!NOTE]
> V předchozích verzích rozhraní IEF (Identity Experience Framework) byly výstupní deklarace použity ke shromažďování dat od uživatele. Chcete-li shromažďovat data od uživatele, použijte místo toho **kolekci DisplayClaims.**

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

### <a name="when-you-should-use-output-claims"></a>Kdy byste měli použít výstupní deklarace

V samoobslužném technickém profilu vrátí kolekce deklarací výstupu deklarace identity dalšímu kroku orchestrace.

Výstupní deklarace použijte v:

- **Deklarace jsou výstupem transformace výstupních deklarací**.
- **Nastavení výchozí hodnoty ve výstupní deklaraci** bez shromažďování dat od uživatele nebo vrácení dat z technického profilu ověření. Vlastní `LocalAccountSignUpWithLogonEmail` uplatněný technický profil nastaví **nárok na spuštění SelfAsserted-Input** `true`.
- **Technický profil ověření vrátí výstupní deklarace –** váš technický profil může volat technický profil ověření, který vrací některé deklarace identity. Můžete chtít bubliny do deklarací identity a vrátit je do dalšíkroky orchestrace v cestě uživatele. Například při přihlášení pomocí místního účtu, self-tvrdil `SelfAsserted-LocalAccountSignin-Email` technický profil s `login-NonInteractive`názvem volá ověření technický profil s názvem . Tento technický profil ověří pověření uživatele a také vrátí profil uživatele. Například "userPrincipalName", 'displayName', 'givenName' a 'surName'.
- **Ovládací prvek zobrazení vrátí výstupní deklarace -** Váš technický profil může mít odkaz na [ovládací prvek zobrazení](display-controls.md). Ovládací prvek zobrazení vrátí některé deklarace identity, například ověřenou e-mailovou adresu. Můžete chtít bubliny do deklarací identity a vrátit je do dalšíkroky orchestrace v cestě uživatele. Funkce ovládacího prvku zobrazení je aktuálně ve **verzi preview**.

Následující příklad ukazuje použití samoobslužného technického profilu, který používá deklarace identity zobrazení i výstupní deklarace identity.

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

## <a name="persist-claims"></a>Trvalé nároky

Prvek PersistedClaims se nepoužívá. Vlastní uplatněný technický profil nezachová data do Azure AD B2C. Místo toho je provedeno volání technického profilu ověření, který je zodpovědný za zachování dat. Zásady registrace například používají `LocalAccountSignUpWithLogonEmail` vlastní uplatněný technický profil ke shromažďování nového profilu uživatele. Technický `LocalAccountSignUpWithLogonEmail` profil volá technický profil ověření k vytvoření účtu v Azure AD B2C.

## <a name="validation-technical-profiles"></a>Validační technické profily

Technický profil ověření se používá pro ověření některých nebo všech výstupních deklarací referenčního technického profilu. Vstupní tvrzení technického profilu validace musí být uvedena ve výstupních tvrzeních samoobslužného technického profilu. Technický profil ověření ověří vstup uživatele a může uživateli vrátit chybu.

Technický profil ověření může být libovolný technický profil v zásadách, jako je [například Azure Active Directory](active-directory-technical-profile.md) nebo technické profily [rozhraní REST API.](restful-technical-profile.md) V předchozím příkladu `LocalAccountSignUpWithLogonEmail` technický profil ověří, že signinName neexistuje v adresáři. Pokud tomu tak není, technický profil ověření vytvoří místní účet a vrátí objectId, authenticationSource, newUser. Technický `SelfAsserted-LocalAccountSignin-Email` profil volá `login-NonInteractive` technický profil ověření k ověření pověření uživatele.

Můžete také volat technický profil rozhraní REST API s obchodní logikou, přepsat vstupní deklarace nebo obohatit uživatelská data další integrací s podnikovou obchodní aplikací. Další informace naleznete v [tématu Ověření technického profilu](validation-technical-profile.md)

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Ne | Pro přihlašovací stránku tato vlastnost řídí chování pole uživatelského jména, jako je například ověření vstupu a chybové zprávy. Očekávané `Username` hodnoty: `Email`nebo .  |
| AllowGenerationOfClaimsWithNullValues| Ne| Povolit generování deklarace s nulovou hodnotou. Například v případě, že uživatel nezaškrtne políčko.|
| ContentDefinitionReferenceId | Ano | Identifikátor definice [obsahu](contentdefinitions.md) přidružené k tomuto technickému profilu. |
| EnforceEmailVerification | Ne | Pro registraci nebo úpravy profilu vynucuje ověření e-mailu. Možné hodnoty: `true` (výchozí) nebo `false`. |
| setting.retryLimit | Ne | Určuje, kolikrát se uživatel může pokusit poskytnout data, která jsou kontrolována podle technického profilu ověření. Uživatel se například pokusí zaregistrovat pomocí účtu, který již existuje a stále se snaží, dokud není dosaženo limitu.
| Cíl registrace <sup>1</sup>| Ne | Identifikátor výměny cíle registrace. Když uživatel klikne na tlačítko registrace, Azure AD B2C provede zadaný identifikátor výměny. |
| setting.showCancelButton | Ne | Zobrazí tlačítko storno. Možné hodnoty: `true` (výchozí), nebo`false` |
| setting.showContinueButton | Ne | Zobrazí tlačítko Pokračovat. Možné hodnoty: `true` (výchozí), nebo`false` |
| setting.showSignupLink <sup>2</sup>| Ne | Zobrazí tlačítko registrace. Možné hodnoty: `true` (výchozí), nebo`false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| Ne| Zobrazí odkaz na zapomenuté heslo. Možné hodnoty: `AfterInput` (výchozí) odkaz se zobrazí v dolní `None` části stránky nebo odebere odkaz na zapomenuté heslo.|
| setting.enableRememberMe <sup>2</sup>| Ne| Zobrazí zaškrtávací políčko [Ponechat přihlášen.](custom-policy-keep-me-signed-in.md) Možné hodnoty: `true` `false` , nebo (výchozí). |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |

Poznámky:
1. K dispozici pro definici `unifiedssp`obsahu `unifiedssd` [Typ DataUri](contentdefinitions.md#datauri) , nebo .
1. K dispozici pro definici `unifiedssp`obsahu `unifiedssd` [Typ DataUri](contentdefinitions.md#datauri) , nebo . [Rozložení stránky verze](page-layout.md) 1.1.0 a vyšší.

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** se nepoužívá.
