---
title: ContentDefinitions – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte element ContentDefinitions vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f51b058b14525cc5a4af312696330a3a39ea44e1
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381229"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Můžete přizpůsobit vzhled a chování žádné [držitelem s prohlašovanou technický profil](self-asserted-technical-profile.md). Azure Active Directory (Azure AD) B2C kód v prohlížeči vašeho zákazníka a využívá moderní přístup a volá sdílení prostředků mezi zdroji (CORS). 

Přizpůsobení uživatelského rozhraní, zadejte adresu URL v **ContentDefinition** element s vlastní obsah ve formátu HTML. V profilu s vlastním potvrzením technické nebo **OrchestrationStep**, přejděte na tento identifikátor definici obsahu. Může obsahovat definice obsahu **LocalizedResourcesReferences** prvek, který určuje seznam lokalizované prostředky pro načtení. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML, který je načten z vaší adresy URL a pak zobrazí na stránce pro uživatele.

**ContentDefinitions** prvek obsahuje adresy URL do šablon HTML5, které lze použít v cestě uživatele. Identifikátor URI stránky HTML5 se používá pro krok zadaného uživatelského rozhraní. Pro příklad, resetování hesla přihlášení nebo registraci nebo chybové stránky. Vzhled a chování můžete upravit tak, že přepíšete LoadUri pro soubor HTML5. Můžete vytvořit nové definice obsahu podle vašich potřeb. Tento element může obsahovat odkaz na lokalizované prostředky, na zadaný identifikátor lokalizace [lokalizace](localization.md) elementu.

Následující příklad ukazuje definici obsahu identifikátor a definice lokalizované prostředky:

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LoalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadata **LocalAccountSignUpWithLogonEmail** držitelem s prohlašovanou technický profil obsahuje identifikátor obsahu definice **ContentDefinitionReferenceId** nastavena na `api.localaccountsignup`

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


## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** prvek obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ID | Ano | Identifikátor pro definici obsahu. Hodnotu zadanou v **obsahu definice ID** dále v této stránce. |

**ContentDefinition** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | Řetězec, který obsahuje adresu URL stránky HTML5 pro definici obsahu. |
| RecoveryUri | 0:1 | Řetězec, který obsahuje adresu URL stránky HTML k chybě týkající se obsahu definice zobrazení. | 
| Parametr | 1:1 | Řetězec, který obsahuje relativní adresu URL souboru HTML, který poskytuje uživatelské prostředí, která se má vyvolat pro krok. |  
| Metadata | 1:1 | Kolekce dvojic klíč/hodnota, která obsahuje metadata využívaných definici obsahu. | 
| LocalizedResourcesReferences | 0:1 | Kolekce odkazů na lokalizované prostředky. Tento element slouží k přizpůsobení lokalizace uživatelského rozhraní a deklarace atributu. |

### <a name="datauri"></a>Parametr

**Parametr** element slouží k určení identifikátoru stránky. Azure AD B2C používá identifikátor stránky pro načtení a zahájit prvky uživatelského rozhraní a JavaScript na straně klienta. Formát hodnoty je `urn:com:microsoft:aad:b2c:elements:page-name:version`.  V následující tabulce jsou uvedeny identifikátorů stránky, která vám pomůže.

| Hodnota |   Popis |
| ----- | ----------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | Zobrazí chybovou stránku, když došlo k výjimce nebo došlo k chybě. |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | Zobrazí seznam zprostředkovatelů identity, které uživatelé můžou vybírat při přihlašování. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | Zobrazí formulář pro přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Také poskytuje tuto hodnotu "zůstat přihlášení funkce" a "Zapomněli jste heslo?" odkaz. | 
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | Zobrazí formulář pro přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | Ověří telefonní čísla pomocí textových nebo hlasových během registrace nebo přihlášení. |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | Zobrazí formulář, který umožňuje uživatelům vytvořit nebo aktualizovat svůj profil. | 


### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | Seznam odkazy na lokalizované prostředky pro definici obsahu. | 

**LocalizedResourcesReferences** prvek obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Jazyk | Ano | Řetězec, který obsahuje podporovaný jazyk pro zásady souladu s RFC 5646 – značky pro identifikaci jazyků. |
| LocalizedResourcesReferenceId | Ano | Identifikátor **LocalizedResources** elementu. |

Následující příklad ukazuje definici obsahu. registrace nebo přihlášení:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
</ContentDefinition>
```

Následující příklad ukazuje definici obsahu registrace nebo přihlášení s odkazem na lokalizaci pro angličtinu, francouzštinu a španělština:

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

Zjistěte, jak přidat podporu lokalizace obsahu definic, najdete v článku [lokalizace](localization.md).

## <a name="content-definition-ids"></a>Definici ID obsahu

Atribut ID **ContentDefinition** prvek určuje typ stránky, která má vztah k definici obsahu. Element definuje kontext, na který budete použít vlastní šablonu HTML5 a CSS. Následující tabulka popisuje sadu obsahu definice ID, který je rozpoznán architekturu rozhraní identit a typy stránek, které se vztahují k nim. Vytvoříte vlastní definice obsahu s libovolný identifikátor.

| ID | Výchozí šablona | Popis | 
| -- | ---------------- | ----------- |
| **api.error** | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Chybová stránka** – zobrazí chybu stránku při výjimce nebo dojde k chybě. |
| **API.idpselections** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Stránka výběru zprostředkovatele identit** -obsahuje seznam zprostředkovatelů identity, které uživatelé můžou vybírat při přihlašování. Možnosti jsou obvykle enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook a Google + nebo místním účtům. |
| **api.idpselections.signup** | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Výběr zprostředkovatele identity pro registraci** -obsahuje seznam zprostředkovatelů identity, které uživatelé můžou vybírat při registraci. Možnosti jsou obvykle enterprise zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook a Google + nebo místním účtům. |
| **api.localaccountpasswordreset** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka zapomenuté heslo** – zobrazí formulář, který uživatelé musí dokončit k zahájení resetování hesla. |
| **API.localaccountsignin** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Místní účet přihlašovací stránku** – zobrazí formulář pro přihlašování pomocí místní účet, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat pole textového zadání a pole pro zadání hesla. |
| **API.localaccountsignup** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránku pro přihlášení místním účtem** – zobrazí formulář pro registraci místního účtu, který je založen na e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako například: textový vstup pole, pole pro zadání hesla, přepínací tlačítko, rozevírací seznamy výběru jednoho a víc zaškrtněte políčka. |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Stránka služby Multi-Factor authentication** -ověří telefonní čísla pomocí textových nebo hlasových během registrace nebo přihlášení. |
| **api.selfasserted** | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Stránka registrace sociálního účtu** – zobrazí formulář, který uživatelé musí dokončit při registraci pomocí existující účet od poskytovatele identity v sociálních sítích. Tato stránka je podobný jako předchozí účtu na sociální síti registrační stránku, s výjimkou pole pro zadání hesla. |
| **api.selfasserted.profileupdate** | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Stránka pro aktualizaci profilu** – zobrazí formulář, který mohou uživatelé aktualizovat svůj profil. Tato stránka je podobný na přihlašovací stránku, s výjimkou pole pro zadání hesla účtu na sociální síti. |
| **api.signuporsignin** | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Jednotná stránka registrace nebo přihlašování** – zpracovává proces registrace a přihlášení uživatele. Uživatelé můžou používat podnikové zprostředkovatelů identity, zprostředkovatelé sociálních identit, jako je Facebook nebo Google + nebo místním účtům. |
 
