---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Zadejte element ContentDefinitions vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 018d90db06948f3fd6a34b56c65088641a9ca874
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108973"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete přizpůsobit vzhled a chování každého [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) spouští kód v prohlížeči zákazníka a používá moderní přístup nazvaný sdílení prostředků mezi zdroji (CORS).

Chcete-li přizpůsobit uživatelské rozhraní, zadejte adresu URL v elementu **ContentDefinition** s přizpůsobeným obsahem HTML. V technickém profilu nebo **OrchestrationStep** s vlastním uplatněním odkazujete na tento identifikátor definice obsahu. Definice obsahu může obsahovat element **LocalizedResourcesReferences** , který určuje seznam lokalizovaných prostředků, které se mají načíst. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML načteným z vaší adresy URL a pak zobrazí stránku uživateli.

Element **ContentDefinitions** obsahuje adresy URL pro šablony HTML5, které lze použít při cestě uživatele. Identifikátor URI stránky HTML5 se používá pro zadaný krok uživatelského rozhraní. Například přihlášení nebo registrace, resetování hesla nebo chybové stránky. Vzhled a chování můžete upravit přepsáním LoadUri pro soubor HTML5. Nové definice obsahu můžete vytvořit podle svých potřeb. Tento element může obsahovat odkaz na lokalizované prostředky na identifikátor lokalizace zadaný v elementu [Localization](localization.md) .

Následující příklad ukazuje identifikátor definice obsahu a definici lokalizovaných prostředků:

```xml
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

Metadata **LocalAccountSignUpWithLogonEmailho** technického profilu s vlastním uplatněním obsahují identifikátor definice obsahu **ContentDefinitionReferenceId** nastaven na `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

Element **ContentDefinition** obsahuje následující atribut:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor definice obsahu. Hodnota je jedna zadaná v oddílu **ID definice obsahu** dále na této stránce. |

Element **ContentDefinition** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Řetězec, který obsahuje adresu URL stránky HTML5 pro definici obsahu. |
| RecoveryUri | 1:1 | Řetězec, který obsahuje adresu URL stránky HTML pro zobrazení chyby související s definicí obsahu. Aktuálně se nepoužívá, hodnota musí být `~/common/default_page_error.html` . |
| DataUri | 1:1 | Řetězec, který obsahuje relativní adresu URL souboru HTML, který poskytuje činnost koncového uživatele, která se má vyvolat pro krok. |
| Metadata | 0:1 | Kolekce párů klíč/hodnota, které obsahují metadata využitá definicí obsahu. |
| LocalizedResourcesReferences | 0:1 | Kolekce lokalizovaných odkazů na prostředky Tento prvek použijte k přizpůsobení lokalizace uživatelského rozhraní a atributu deklarace identity. |

### <a name="datauri"></a>DataUri

Element **DataUri** slouží k určení identifikátoru stránky. Azure AD B2C používá identifikátor stránky k načtení a spuštění prvků uživatelského rozhraní a JavaScriptu na straně klienta. Formát hodnoty je `urn:com:microsoft:aad:b2c:elements:page-name:version` . Následující tabulka obsahuje seznam identifikátorů stránek, které můžete použít.

| Identifikátor stránky | Popis |
| ----- | ----------- |
| `globalexception` | Zobrazí chybovou stránku, pokud dojde k výjimce nebo chybě. |
| `providerselection`, `idpselection` | Zobrazuje seznam zprostředkovatelů identity, ze kterých si uživatelé můžou vybrat během přihlašování.  |
| `unifiedssp` | Zobrazí formulář pro přihlášení pomocí místního účtu, který je založený na e-mailové adrese nebo uživatelském jménu. Tato hodnota také poskytuje možnost "zachovat funkce přihlašování" a zapomněli jste heslo? " . |
| `unifiedssd` | Zobrazí formulář pro přihlášení pomocí místního účtu, který je založený na e-mailové adrese nebo uživatelském jménu. |
| `multifactor` | Ověřuje telefonní čísla pomocí textu nebo hlasu během registrace nebo přihlašování. |
| `selfasserted` | Zobrazí formulář pro shromažďování dat od uživatele. Například umožňuje uživatelům vytvořit nebo aktualizovat svůj profil. |

### <a name="select-a-page-layout"></a>Vybrat rozložení stránky

Můžete povolit [javascriptový kód na straně klienta](javascript-and-page-layout.md) vložením `contract` mezi `elements` a a typem stránky. Například `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Část [verze](page-layout.md) `DataUri` Určuje balíček obsahu obsahujícího jazyky HTML, CSS a JavaScript pro prvky uživatelského rozhraní v zásadách. Pokud máte v úmyslu povolit kód na straně klienta JavaScript, prvky, na kterých založíte JavaScript, musí být neměnné. Pokud nejsou neměnné, můžou jakékoli změny způsobit neočekávané chování na stránkách uživatele. Chcete-li zabránit těmto problémům, vynutili použití rozložení stránky a určení verze rozložení stránky. Tím zajistíte, že všechny definice obsahu, na kterých jste na svém JavaScriptu vycházíte, jsou neměnné. I v případě, že nechcete povolit JavaScript, je stále nutné zadat verzi rozložení stránky pro vaše stránky.

Následující příklad ukazuje **DataUri** `selfasserted` verze `1.2.0` :

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

#### <a name="migrating-to-page-layout"></a>Migrace na rozložení stránky

Formát hodnoty musí obsahovat slovo `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p věk-Name: Version_. Chcete-li určit rozložení stránky ve vlastních zásadách, které používají starou hodnotu **DataUri** , proveďte migraci do nového formátu pomocí následující tabulky.

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

Následující příklad ukazuje identifikátory definice obsahu a odpovídající **DataUri** s kontraktem stránky: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

### <a name="metadata"></a>Metadata

Element **metadata** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Položka | 0: n | Metadata vztahující se k definici obsahu. |

Element **Item** elementu **metadata** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Klíč | Ano | Klíč metadat.  |

#### <a name="metadata-keys"></a>Klíče metadat

Definice obsahu podporuje následující položky metadat:

| Klíč | Povinné | Popis |
| --------- | -------- | ----------- |
| DisplayName | Ne | Řetězec, který obsahuje název definice obsahu. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

Element **LocalizedResourcesReferences** obsahuje následující prvky:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Seznam lokalizovaných odkazů na prostředky pro definici obsahu. |

Element **LocalizedResourcesReference** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Jazyk | Ano | Řetězec, který obsahuje podporovaný jazyk pro zásady na značku RFC 5646-Tags pro identifikaci jazyků. |
| LocalizedResourcesReferenceId | Ano | Identifikátor elementu **LocalizedResources** |

Následující příklad ukazuje definici obsahu pro registraci nebo přihlašování s odkazem na lokalizaci pro angličtinu, francouzštinu a španělštinu:

```xml
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

Další informace o tom, jak přidat podporu lokalizace do definic obsahu, naleznete v tématu [lokalizace](localization.md).

## <a name="content-definition-ids"></a>ID definice obsahu

Atribut ID elementu **ContentDefinition** určuje typ stránky, která se vztahuje k definici obsahu. Prvek definuje kontext, který bude použita vlastní šablona HTML5/CSS. V následující tabulce jsou popsány sady ID definic obsahu rozpoznávané architekturou prostředí identity a typy stránek, které se na ně vztahují. Můžete vytvořit vlastní definice obsahu s libovolným ID.

| ID | Výchozí šablona | Popis |
| -- | ---------------- | ----------- |
| **rozhraní API. Chyba** | [výjimka. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka** – zobrazí chybovou stránku, když dojde k výjimce nebo chybě. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka Výběr zprostředkovatele identity** – zobrazí seznam zprostředkovatelů identity, ze kterých si uživatelé můžou během přihlašování vybírat. Tyto možnosti jsou obvykle poskytovatelé podnikových identit, poskytovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| **API. idpselections. signup** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr poskytovatele identity pro registraci** – zobrazí seznam zprostředkovatelů identity, ze kterých si uživatelé můžou vybírat během registrace. Tyto možnosti jsou obvykle poskytovatelé podnikových identit, poskytovatelé sociálních identit, jako je Facebook, Google + nebo místní účty. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka zapomenuté heslo** – zobrazí formulář, který uživatelé musí dokončit pro zahájení resetování hesla. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místního účtu** – zobrazí formulář pro přihlášení pomocí místního účtu, který je založený na e-mailové adrese nebo uživatelském jménu. Formulář může obsahovat textové pole pro zadání textu a heslo. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Přihlašovací stránka místního účtu** – zobrazí formulář pro registraci místního účtu, který vychází z e-mailové adresy nebo uživatelského jména. Formulář může obsahovat různé vstupní ovládací prvky, jako je například textové pole pro zadání hesla, pole pro zadávání hesla, přepínač, rozevírací seznamy s jedním výběrem a zaškrtávací políčka vícenásobného výběru. |
| **API. PhoneFactor** | [vícefaktorového – 1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránka Multi-Factor Authentication** – při registraci nebo přihlášení ověřuje telefonní čísla pomocí textu nebo hlasu. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka pro registraci účtu sociální** sítě – zobrazí formulář, který uživatelé musí dokončit při registraci pomocí existujícího účtu od poskytovatele sociálních identit. Tato stránka se podobá na předchozí přihlašovací stránce účtu sociální sítě, s výjimkou polí zadání hesla. |
| **API. selfasserted. profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka aktualizace profilu** – zobrazí formulář, ke kterému mají uživatelé přístup, aby mohli aktualizovat svůj profil. Tato stránka se podobá stránce pro registraci účtu sociální sítě s výjimkou polí zadání hesla. |
| **API. signuporsignin** | [sjednocení. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná registrace nebo přihlašovací stránka** – zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatele identity, poskytovatele sociálních identit, jako je Facebook nebo Google +, nebo místní účty. |

## <a name="next-steps"></a>Další kroky

Příklad přizpůsobení uživatelského rozhraní pomocí definic obsahu najdete v těchto tématech:

[Přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady](customize-ui-with-html.md)
