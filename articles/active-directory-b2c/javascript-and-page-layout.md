---
title: Verze JavaScriptu a rozložení stránky
titleSuffix: Azure AD B2C
description: Naučte se, jak povolit JavaScript a používat verze rozložení stránky v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e54aac6317b21a490627022b110bb5927e6986a6
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111179"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Verze JavaScriptu a rozložení stránky v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C poskytuje sadu sbaleného obsahu obsahujícího jazyky HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve vašich uživatelských tocích a vlastních zásadách. Povolení JavaScriptu pro vaše aplikace:

::: zone pivot="b2c-user-flow"

* Vybrat [rozložení stránky](page-layout.md)
* Povolte ji v uživatelském toku pomocí Azure Portal
* Použití [b2clogin.com](b2clogin.md) v žádostech

::: zone-end

::: zone pivot="b2c-custom-policy"

* Vybrat [rozložení stránky](page-layout.md)
* Přidání elementu do [vlastních zásad](custom-policy-overview.md)
* Použití [b2clogin.com](b2clogin.md) v žádostech

::: zone-end

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Vybrat verzi rozložení stránky

Pokud máte v úmyslu povolit kód na straně klienta JavaScript, prvky, na kterých založíte JavaScript, musí být neměnné. Pokud nejsou neměnné, můžou jakékoli změny způsobit neočekávané chování na stránkách uživatele. Chcete-li zabránit těmto problémům, vynutili použití rozložení stránky a určení verze rozložení stránky, aby bylo zajištěno, že definice obsahu, na kterých jste na svém JavaScriptu zakládáte, jsou neměnné. I v případě, že nechcete povolit JavaScript, můžete zadat verzi rozložení stránky pro stránky.

::: zone pivot="b2c-user-flow"

Určení verze rozložení stránky pro stránky toku uživatele: 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Vyberte zásadu (například "B2C_1_SignupSignin") a otevřete ji.
1. Vyberte **rozložení stránky**. V části **název rozložení** vyberte stránku tok uživatele a zvolte **verzi rozložení stránky (Preview)**.

Informace o různých verzích rozložení stránky naleznete v [protokolu změn verze rozložení stránky](page-layout.md).

![Nastavení rozložení stránky na portálu s rozevírací nabídkou verze rozložení stránky](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Vyberte [rozložení stránky](contentdefinitions.md#select-a-page-layout) pro prvky uživatelského rozhraní vaší aplikace.

Definujte [verzi rozložení stránky](contentdefinitions.md#migrating-to-page-layout) s verzí stránky `contract` pro *všechny* definice obsahu ve vlastních zásadách. Formát hodnoty musí obsahovat slovo `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p věk-Name: Version_. Naučte se [migrovat na rozložení stránky](contentdefinitions.md#migrating-to-page-layout) pomocí verze stránky.

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
::: zone-end

## <a name="enable-javascript"></a>Povolení JavaScriptu

::: zone pivot="b2c-user-flow"

Ve **vlastnostech** toku uživatele můžete povolit JavaScript. Povolení JavaScriptu taky vynutilo použití rozložení stránky. Pak můžete nastavit verzi rozložení stránky pro tok uživatele, jak je popsáno v následující části.

![Stránka vlastností toku uživatele s zvýrazněným nastavením povolit JavaScript](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Spuštění skriptu můžete povolit přidáním elementu **ScriptExecution** do elementu [RelyingParty](relyingparty.md) .

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
1. Přidejte element **ScriptExecution** do elementu **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Uložte a nahrajte soubor.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Pokyny pro používání JavaScriptu

Pokud přizpůsobíte rozhraní aplikace pomocí JavaScriptu, postupujte podle těchto pokynů:

- Nevážete událost kliknutí na `<a>` prvky jazyka HTML.
- Nepoužívejte závislost na Azure AD B2C kód nebo komentáře.
- Neměňte pořadí ani hierarchii Azure AD B2C prvků HTML. Použijte zásady Azure AD B2C k řízení pořadí prvků uživatelského rozhraní.
- Můžete volat libovolnou službu RESTful s těmito informacemi:
    - Možná budete muset nastavit CORS služby RESTful tak, aby umožňovala volání HTTP na straně klienta.
    - Ujistěte se, že je služba RESTful zabezpečená a používá jenom protokol HTTPS.
    - Nepoužívejte JavaScript přímo pro volání Azure AD B2Cch koncových bodů.
- Můžete vložit JavaScript nebo můžete propojit s externími soubory JavaScriptu. Při použití externího souboru JavaScriptu je nutné použít absolutní adresu URL, nikoli relativní adresu URL.
- Rozhraní JavaScript:
    - Azure AD B2C používá specifickou verzi jQuery. Nezahrnujte jinou verzi jQuery. Používání více než jedné verze na stejné stránce způsobuje problémy.
    - Použití RequireJS se nepodporuje.
    - Azure AD B2C nepodporuje většinu platforem JavaScript.
- Nastavení Azure AD B2C lze číst voláním `window.SETTINGS` , `window.CONTENT` objektů, jako je aktuální jazyk uživatelského rozhraní. Neměňte hodnotu těchto objektů.
- Pokud chcete přizpůsobit chybovou zprávu Azure AD B2C, použijte lokalizaci v zásadě.
- Pokud se dá cokoli dosáhnout pomocí zásad, obvykle se jedná o doporučený způsob.

## <a name="javascript-samples"></a>Ukázky JavaScriptu

### <a name="show-or-hide-a-password"></a>Zobrazit nebo skrýt heslo

Běžný způsob, jak zákazníkům pomáhat při jejich registraci, je jim dovolit, aby viděli, co zadali jako heslo. Tato možnost pomůže uživatelům zaregistrovat se tak, že jim umožní v případě potřeby snadno zobrazit a opravit jejich heslo. Každé pole typu heslo má zaškrtávací políčko s popiskem **Zobrazit heslo** .  To uživateli umožňuje zobrazit heslo v prostém textu. Tento fragment kódu vložte do šablony pro registraci nebo přihlášení pro stránku s vlastním kontrolním kódem:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Přidat podmínek použití

Do stránky vložte následující kód, na který chcete zahrnout zaškrtávací políčko s **podmínkami použití** . Toto zaškrtávací políčko je obvykle potřeba na přihlašovacích stránkách místního účtu pro registraci a účet sociální sítě.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

V kódu nahraďte `termsOfUseUrl` odkazem na svůj souhlas s podmínkami použití. Pro váš adresář vytvořte nový atribut uživatele s názvem **termsOfUse** a pak jako atribut uživatele přidejte **termsOfUse** .

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak můžete přizpůsobit uživatelské rozhraní svých aplikací, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace v Azure Active Directory B2C](customize-ui-with-html.md).