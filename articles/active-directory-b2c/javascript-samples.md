---
title: Ukázky JavaScriptu
titleSuffix: Azure AD B2C
description: Přečtěte si o používání JavaScriptu v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.custom: devx-track-js
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9faca55f8440a28a845e892ee38df2de3489f97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259488"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Ukázky JavaScriptu pro použití v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Do aplikací Azure Active Directory B2C (Azure AD B2C) můžete přidat vlastní kód na straně klienta JavaScript.

Povolení JavaScriptu pro vaše aplikace:

* Přidání elementu do [vlastních zásad](custom-policy-overview.md)
* Vybrat [rozložení stránky](page-layout.md)
* Použití [b2clogin.com](b2clogin.md) v žádostech

Tento článek popisuje, jak můžete změnit vlastní zásady a povolit spouštění skriptů.

> [!NOTE]
> Pokud chcete povolit JavaScript pro toky uživatelů, přečtěte si téma [verze JavaScriptu a rozložení stránky v Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Požadavky

### <a name="select-a-page-layout"></a>Vybrat rozložení stránky

* Vyberte [rozložení stránky](contentdefinitions.md#select-a-page-layout) pro prvky uživatelského rozhraní vaší aplikace.

    Pokud máte v úmyslu použít JavaScript, je nutné [definovat verzi rozložení stránky](contentdefinitions.md#migrating-to-page-layout) s verzí stránky `contract` pro *všechny* definice obsahu ve vlastních zásadách.

## <a name="add-the-scriptexecution-element"></a>Přidat element ScriptExecution

Spuštění skriptu můžete povolit přidáním elementu **ScriptExecution** do elementu [RelyingParty](relyingparty.md) .

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
2. Přidejte element **ScriptExecution** do **UserJourneyBehaviors** elementu **RelyingParty**:

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Uložte a nahrajte soubor.

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

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

Další informace o tom, jak můžete přizpůsobit uživatelské rozhraní svých aplikací, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C](custom-policy-ui-customization.md).
