---
title: Vložení Azure Active Directory B2C uživatelského rozhraní do aplikace s vlastními zásadami
titleSuffix: Azure AD B2C
description: Naučte se, jak do aplikace vložit Azure Active Directory B2C uživatelské rozhraní s vlastními zásadami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4fbb09a2687673ef96252d8bdb0523d0c5f235ae
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772021"
---
# <a name="embedded-sign-in-experience"></a>Vložené prostředí pro přihlašování

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Pro jednodušší prostředí přihlašování se můžete vyhnout přesměrování uživatelů na samostatnou přihlašovací stránku nebo při generování automaticky otevíraného okna. Pomocí vloženého prvku rámce `<iframe>` můžete Azure AD B2C uživatelské rozhraní pro přihlašování do webové aplikace vložit přímo.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Přihlášení vložené webové aplikace

Element vloženého rámce `<iframe>` se používá k vložení dokumentu na webovou stránku HTML5. Element IFrame lze použít k vložení Azure AD B2C uživatelského rozhraní pro přihlašování přímo do webové aplikace, jak je znázorněno v následujícím příkladu:

![Přihlášení pomocí možnosti DIV najetí myší](media/embedded-login/login-hovering.png)

Při použití prvku IFRAME zvažte následující:

- Vložené přihlášení podporuje pouze místní účty. Většina poskytovatelů sociálních identit (například Google a Facebook) blokuje jejich přihlašovací stránky při jejich vykreslování ve vložených rámečcích.
- Vzhledem k tomu, že soubory cookie relací Azure AD B2C v rámci prvku IFRAME jsou považovány za soubory cookie třetích stran, některé prohlížeče (například Safari nebo Chrome v režimu anonymním) zablokují nebo vymažou tyto soubory cookie, což vede k nežádoucímu uživatelskému prostředí. Chcete-li tomuto problému zabránit, ujistěte se, že název domény aplikace a vaše Azure AD B2C doména mají *stejný původ*. Pokud chcete použít stejný původ, povolte pro klienta Azure AD B2C [vlastní domény](custom-domain.md) a pak nakonfigurujte svou webovou aplikaci se stejným zdrojem. Například aplikace hostovaná na https://app.contoso.com má stejný původ jako Azure AD B2C běžící na https://login.contoso.com .

## <a name="prerequisites"></a>Předpoklady

* Dokončete kroky v části [Začínáme s vlastními zásadami v Active Directory B2C](custom-policy-get-started.md).
* [Povolte vlastní domény](custom-domain.md) pro vaše zásady.

## <a name="configure-your-policy"></a>Konfigurace zásad

Aby bylo možné Azure AD B2C uživatelské rozhraní vkládat do prvku IFRAME, `Content-Security-Policy` `X-Frame-Options` musí být v hlavičkách HTTP odpovědi Azure AD B2C zahrnuty zásady zabezpečení obsahu a možnosti rámce. Tato záhlaví umožňují, aby Azure AD B2C uživatelské rozhraní běželo pod názvem domény aplikace.

Přidejte element **JourneyFraming** uvnitř elementu [RelyingParty](relyingparty.md) .  Element **UserJourneyBehaviors** musí následovat po **DefaultUserJourney**. Váš element **UserJourneyBehaviors** by měl vypadat jako v tomto příkladu:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

Atribut **sources** obsahuje identifikátor URI vaší webové aplikace. Přidejte mezeru mezi identifikátory URI. Každý identifikátor URI musí splňovat následující požadavky:

- Identifikátor URI musí být důvěryhodný a vlastnit vaši aplikaci.
- Identifikátor URI musí používat schéma https.  
- Musí být zadaný úplný identifikátor URI webové aplikace. Zástupné znaky se nepodporují.

Kromě toho doporučujeme, abyste před vložením do prvku IFRAME zablokovali vlastní název domény tak, že nastavíte záhlaví Content-Security-Policy a X-frame-Options v uvedeném pořadí na stránkách aplikace. Tím se sníží dopad na zabezpečení pro starší prohlížeče související s vnořenými vkládáním prvků IFRAME.

## <a name="adjust-policy-user-interface"></a>Upravit uživatelské rozhraní zásad

Díky Azure AD B2C [přizpůsobení uživatelského rozhraní](customize-ui.md)máte téměř plnou kontrolu nad obsahem HTML a CSS prezentovaným uživatelům. Postupujte podle kroků pro přizpůsobení stránky HTML pomocí definic obsahu. Chcete-li přizpůsobit Azure AD B2C uživatelskému rozhraní do velikosti prvku IFRAME, poskytněte čistou stránku HTML bez pozadí a mezer navíc.  

Následující kód CSS skryje Azure AD B2C prvky HTML a upraví velikost panelu tak, aby byl prvek IFRAME vyplněn.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

V některých případech můžete chtít sdělit aplikaci, na které Azure AD B2C stránka aktuálně prezentuje. Když třeba uživatel vybere možnost registrace, možná budete chtít, aby aplikace reagovala skrytím odkazů pro přihlášení pomocí účtu sociální sítě nebo úpravou velikosti prvku IFRAME.

Pro upozornění aplikace na aktuální Azure AD B2C stránku, [povolte zásady pro JavaScript](./javascript-and-page-layout.md)a pak použijte následné zprávy HTML5. Následující kód jazyka JavaScript pošle zprávu odeslání do aplikace pomocí `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Konfigurace webové aplikace

Když uživatel vybere tlačítko pro přihlášení, [Webová aplikace](code-samples.md#web-apps-and-apis) vygeneruje požadavek na autorizaci, který uživateli převezme Azure AD B2C přihlašovací prostředí. Po dokončení přihlášení Azure AD B2C vrátí token ID nebo autorizační kód k nakonfigurovanému identifikátoru URI přesměrování v rámci aplikace.

Aby bylo možné podporovat vložené přihlašovací údaje, ukazuje vlastnost **Src** elementu IFRAME na řadič přihlášení, například `/account/SignUpSignIn` , který vygeneruje žádost o autorizaci a přesměruje uživatele na Azure AD B2C zásady.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Po přijetí a ověření tokenu ID aplikací se tok autorizace dokončí a aplikace rozpoznává a důvěřuje uživateli. Vzhledem k tomu, že tok autorizace probíhá uvnitř prvku IFRAME, je nutné znovu načíst hlavní stránku. Po opětovném načtení stránky se tlačítko pro přihlášení změní na Odhlásit se a uživatelské jméno se zobrazí v uživatelském rozhraní.  

Následuje příklad ukazující, jak může identifikátor URI pro přesměrování přihlášení aktualizovat hlavní stránku:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Přidání přihlašování pomocí sociálních účtů do webové aplikace

Zprostředkovatelé sociální identity blokují jejich přihlašovací stránky od vykreslování v vložených rámečcích. Můžete použít samostatnou zásadu pro účty sociálních sítí, nebo můžete použít jednu zásadu pro přihlášení a registraci pomocí místních i sociálních účtů. Pak můžete použít `domain_hint` parametr řetězce dotazu. Parametr parametru doména přebírá uživatele přímo na přihlašovací stránku zprostředkovatele sociální identity.

Do aplikace přidejte přihlášení pomocí tlačítek účtu pro sociální sítě. Když uživatel klikne na tlačítko pro sociální účet, ovládací prvek musí změnit název zásady nebo nastavit parametr pro doménu.

<!-- TBD: add a diagram -->

Identifikátor URI přesměrování může být stejný identifikátor URI přesměrování, který používá IFRAME. Opětovné načtení stránky můžete přeskočit.

## <a name="configure-a-single-page-application"></a>Konfigurace jednostránkové aplikace

U jednostránkové aplikace budete také potřebovat druhou stránku HTML přihlášení, která se načte do prvku IFRAME. Tato přihlašovací stránka hostuje kód knihovny ověřování, který generuje autorizační kód a vrací token.

Když aplikace s jednou stránkou potřebuje přístupový token, použijte kód jazyka JavaScript k získání přístupového tokenu z prvku IFRAME a objektu, který jej obsahuje.

> [!NOTE]
> Spuštění MSAL 2,0 v prvku IFRAME není aktuálně podporováno.

Následující kód je příklad, který běží na hlavní stránce a volá JavaScriptový kód prvku IFRAME:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Další kroky

Podívejte se na následující související články:

- [Přizpůsobení uživatelského rozhraní](customize-ui.md)
- Odkaz na element [RelyingParty](relyingparty.md)
- [Povolit zásady pro JavaScript](./javascript-and-page-layout.md)
- [Ukázky kódu](code-samples.md)

::: zone-end
