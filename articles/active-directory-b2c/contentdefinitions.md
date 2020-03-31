---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Zadejte prvek ContentDefinitions vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074a0a39090e22a29f778fc1c99060848c6bfd99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051497"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete přizpůsobit vzhled a dojem z jakéhokoli [self-tvrdil technický profil](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) spouští kód v prohlížeči zákazníka a používá moderní přístup s názvem Sdílení prostředků mezi zdroji (CORS).

Chcete-li přizpůsobit uživatelské rozhraní, zadejte adresu URL v elementu **ContentDefinition** s přizpůsobeným obsahem HTML. V samoobslužné technický profil nebo **OrchestrationStep**, můžete přejděte na tento identifikátor definice obsahu. Definice obsahu může obsahovat element **LocalizedResourcesReferences,** který určuje seznam lokalizovaných prostředků, které mají být načteny. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML, který je načten z adresy URL a pak zobrazí stránku uživateli.

Element **ContentDefinitions** obsahuje adresy URL šablon HTML5, které lze použít v cestě uživatele. Identifikátor URI stránky HTML5 se používá pro zadaný krok uživatelského rozhraní. Například přihlášení nebo přihlášení, resetování hesla nebo chybové stránky. Vzhled a chování můžete upravit přepsáním identifikátoru LoadUri pro soubor HTML5. Můžete vytvářet nové definice obsahu podle svých potřeb. Tento prvek může obsahovat odkaz na lokalizované prostředky na identifikátor lokalizace zadaný v [lokalizačním](localization.md) prvku.

Následující příklad ukazuje identifikátor definice obsahu a definici lokalizovaných prostředků:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadata **místního účtuSignUpUpWithLogonEmail** vlastní uplatněný technický profil obsahuje identifikátor definice obsahu **ContentDefinitionReferenceId** nastavena na`api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>Definice obsahu

Prvek **ContentDefinition** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor pro definici obsahu. Hodnota je zadána v části **ID definice obsahu** dále na této stránce. |

Prvek **ContentDefinition** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Řetězec, který obsahuje adresu URL stránky HTML5 pro definici obsahu. |
| RecoveryUri | 1:1 | Řetězec, který obsahuje adresu URL stránky HTML pro zobrazení chyby týkající se definice obsahu. Není aktuálně používán, hodnota `~/common/default_page_error.html`musí být . |
| DataUri | 1:1 | Řetězec, který obsahuje relativní adresu URL souboru HTML, který poskytuje uživatelské prostředí vyvolat krok. |
| Metadata | 0:1 | Kolekce párů klíč/hodnota, která obsahuje metadata využívaná definicí obsahu. |
| LocalizedResourcesReferences | 0:1 | Kolekce lokalizovaných prostředků odkazy. Tento prvek slouží k přizpůsobení lokalizace uživatelského rozhraní a atributu deklarací identity. |

### <a name="datauri"></a>DataUri

Prvek **DataUri** se používá k určení identifikátoru stránky. Azure AD B2C používá identifikátor stránky k načtení a zahájení prvků uživatelského rozhraní a JavaScriptu na straně klienta. Formát hodnoty je `urn:com:microsoft:aad:b2c:elements:page-name:version`. V následující tabulce jsou uvedeny identifikátory stránek, které můžete použít.

| Identifikátor stránky | Popis |
| ----- | ----------- |
| `globalexception` | Zobrazí chybovou stránku, když dojde k výjimce nebo chybě. |
| `providerselection`, `idpselection` | Uvádí zprostředkovatele identit, ze kterých si uživatelé mohou vybrat během přihlašování.  |
| `unifiedssp` | Zobrazí formulář pro přihlášení pomocí místního účtu, který je založen na e-mailové adrese nebo uživatelském jménu. Tato hodnota také poskytuje funkci "keep me sign-in" a "Zapomněli jste heslo?" Odkaz. |
| `unifiedssd` | Zobrazí formulář pro přihlášení pomocí místního účtu, který je založen na e-mailové adrese nebo uživatelském jménu. |
| `multifactor` | Ověřuje telefonní čísla pomocí textu nebo hlasu během registrace nebo přihlášení. |
| `selfasserted` | Zobrazí formulář pro sběr dat od uživatele. Umožňuje například uživatelům vytvářet nebo aktualizovat svůj profil. |

### <a name="select-a-page-layout"></a>Výběr rozložení stránky

[Kód klienta javascriptu](javascript-samples.md) můžete povolit vložením `contract` mezi `elements` a typem stránky. Například, `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Část [verze](page-layout.md) této `DataUri` verze určuje balíček obsahu obsahujícího HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve vaší zásadě. Pokud máte v úmyslu povolit kód javascriptu na straně klienta, musí být prvky, na kterých je javascript zakládán, neměnné. Pokud nejsou neměnné, jakékoli změny mohou způsobit neočekávané chování na uživatelských stránkách. Chcete-li těmto problémům zabránit, vynuťte použití rozložení stránky a určete verzi rozložení stránky. Tím zajistíte, že všechny definice obsahu, na kterých jste javascript založili, budou neměnné. I v případě, že nemáte v úmyslu povolit JavaScript, stále je třeba zadat verzi rozložení stránky pro vaše stránky.

Následující příklad ukazuje **DataUri** `selfasserted` `1.2.0`verze :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrace do rozložení stránky

Formát hodnoty musí obsahovat `contract`slovo : _urn:com:microsoft:aad:b2c:elements:**contract**:page-name:version_. Chcete-li zadat rozložení stránky ve vlastních zásadách, které používají starou hodnotu **DataUri,** migrujte do nového formátu pomocí následující tabulky.

| Stará hodnota DataUri | Nová hodnota DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>Metadata

Prvek **metadat** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Položka | 0:n | Metadata, která se vztahuje k definici obsahu. |

Prvek **Item** prvku **Metadata** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Klíč | Ano | Klíč metadat.  |

#### <a name="metadata-keys"></a>Klíče metadat

Definice obsahu podporuje následující položky metadat:

| Klíč | Požaduje se | Popis |
| --------- | -------- | ----------- |
| DisplayName | Ne | Řetězec, který obsahuje název definice obsahu. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Prvek **LocalizedResourcesReferences** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Odkaz na lokalizované zdroje | 1:n | Seznam lokalizovaných odkazů na prostředky pro definici obsahu. |

Element **LocalizedResourcesReference** obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Jazyk | Ano | Řetězec, který obsahuje podporovaný jazyk pro zásady podle RFC 5646 - značky pro identifikaci jazyků. |
| Lokalizované zdrojeReferenceId | Ano | Identifikátor prvku **LocalizedResources.** |

Následující příklad ukazuje definici obsahu registrace nebo přihlášení s odkazem na lokalizaci pro angličtinu, francouzštinu a španělštinu:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Informace o tom, jak přidat podporu lokalizace k definicím obsahu, naleznete v [tématu Lokalizace](localization.md).

## <a name="content-definition-ids"></a>ID definice obsahu

Atribut ID prvku **ContentDefinition** určuje typ stránky, která se vztahuje k definici obsahu. Prvek definuje kontext, který bude použít vlastní šablona HTML5/CSS. Následující tabulka popisuje sadu ID definice obsahu, která je rozpoznána rozhraním Identity Experience Framework, a typy stránek, které se jich týkají. Můžete vytvořit vlastní definice obsahu s libovolným ID.

| ID | Výchozí šablona | Popis |
| -- | ---------------- | ----------- |
| **api.chyba** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka** - Zobrazí chybovou stránku, když dojde k výjimce nebo chybě. |
| **api.idpsvolby** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka výběru zprostředkovatele identity** – zobrazí seznam zprostředkovatelů identit, ze kterých si uživatelé mohou vybrat během přihlášení. Možnosti jsou obvykle poskytovatelé podnikových identit, poskytovatelé sociální identity, jako je Facebook a Google+, nebo místní účty. |
| **api.idpselections.registrace** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr zprostředkovatele identity pro registraci** – zobrazí seznam poskytovatelů identit, ze kterých si uživatelé mohou vybrat během registrace. Možnosti jsou obvykle poskytovatelé podnikových identit, poskytovatelé sociální identity, jako je Facebook a Google+, nebo místní účty. |
| **api.localaccountpasswordreset** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka Zapomenuté heslo** – Zobrazí formulář, který musí uživatelé vyplnit, aby zahájili resetování hesla. |
| **api.localaccountsignin** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místního účtu** – Zobrazí formulář pro přihlášení pomocí místního účtu, který je založen na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat textové vstupní pole a pole pro zadání hesla. |
| **api.localaccountsignup** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka registrace místního účtu** – Zobrazí formulář pro registraci místního účtu, který je založen na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat různé vstupní ovládací prvky, například: textové vstupní pole, pole pro zadání hesla, přepínací tlačítko, rozevírací políčka s jedním výběrem a vícenásobná zaškrtávací políčka. |
| **api.phonefactor** | [multifaktor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránka vícefaktorového ověřování** – Ověřuje telefonní čísla pomocí textu nebo hlasu při registraci nebo přihlášení. |
| **api.selfasserted** | [selfasserted.cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka registrace účtu na sociální chod –** Zobrazí formulář, který musí uživatelé vyplnit, když se zaregistrují pomocí existujícího účtu od poskytovatele sociální identity. Tato stránka je podobná předchozí stránce registrace účtu na sociální síti, s výjimkou polí pro zadání hesla. |
| **api.selfasserted.profileupdate** | [updateprofile.cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu** – Zobrazí formulář, ke kterému mají uživatelé přístup k aktualizaci svého profilu. Tato stránka je podobná stránce registrace účtu na sociální síti, s výjimkou polí pro zadání hesla. |
| **api.signuporsignin** | [unified.cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Sjednocená přihlašovací nebo přihlašovací stránka** – zpracovává proces registrace a přihlášení uživatele. Uživatelé mohou používat poskytovatele podnikových identit, poskytovatele sociálníidentity, jako je Facebook nebo Google+, nebo místní účty. |

## <a name="next-steps"></a>Další kroky

Příklad přizpůsobení uživatelského rozhraní pomocí definic obsahu naleznete v následujících tématech:

[Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad](custom-policy-ui-customization.md)
