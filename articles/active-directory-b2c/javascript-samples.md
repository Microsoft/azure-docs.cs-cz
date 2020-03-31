---
title: Ukázky JavaScriptu
titleSuffix: Azure AD B2C
description: Přečtěte si o používání JavaScriptu ve Službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a26f6c5e69ca083335580a0368459e062de3941e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187657"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Ukázky JavaScriptu pro použití ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Do aplikací Azure Active Directory B2C (Azure AD B2C) můžete přidat vlastní kód javascriptu na straně klienta.

Povolení JavaScriptu pro vaše aplikace:

* Přidání prvku do [vlastní chodníče](custom-policy-overview.md)
* Výběr [rozložení stránky](page-layout.md)
* Používejte [b2clogin.com](b2clogin.md) ve svých požadavcích

Tento článek popisuje, jak můžete změnit vlastní zásady povolit spuštění skriptu.

> [!NOTE]
> Pokud chcete povolit JavaScript pro toky uživatelů, přečtěte si informace o [javascriptu a verzích rozložení stránky ve službě Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Požadavky

### <a name="select-a-page-layout"></a>Výběr rozložení stránky

* Vyberte [rozložení stránky](contentdefinitions.md#select-a-page-layout) pro prvky uživatelského rozhraní aplikace.

    Pokud máte v úmyslu používat JavaScript, musíte definovat `contract` verzi rozložení [stránky](contentdefinitions.md#migrating-to-page-layout) s verzí stránky pro *všechny* definice obsahu ve vlastních zásadách.

## <a name="add-the-scriptexecution-element"></a>Přidání elementu Execution skriptu

Spuštění skriptu povolíte přidáním elementu **ScriptExecution** do elementu [RelyingParty.](relyingparty.md)

1. Otevřete vlastní soubor zásad. Například *SignUpOrSignin.xml*.
2. Přidejte element **Execution scriptdo** elementu **UserJourneyBehaviors** **relyingparty**:

    ```XML
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

### <a name="show-or-hide-a-password"></a>Zobrazení nebo skrytí hesla

Běžným způsobem, jak pomoci svým zákazníkům s jejich úspěchem v registraci, je umožnit jim vidět, co zadali jako své heslo. Tato možnost pomáhá uživatelům zaregistrovat tím, že jim umožní snadno vidět a provádět opravy jejich heslo v případě potřeby. Každé pole typu heslo má zaškrtávací políčko s popiskem **Zobrazit heslo.**  To umožňuje uživateli zobrazit heslo ve formátu prostého textu. Zahrňte tento fragment kódu do šablony registrace nebo přihlášení pro stránku s vlastním uplatněním:

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

### <a name="add-terms-of-use"></a>Přidání podmínek použití

Zahrňte na stránku následující kód, do kterého chcete zahrnout **podmínky použití.** Toto zaškrtávací políčko je obvykle potřeba na stránkách registrace místního účtu a registrace na sociálních účtech.

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

V kódu nahraďte `termsOfUseUrl` odkazem na vaši smlouvu o podmínkách použití. Pro váš adresář vytvořte nový uživatelský atribut s názvem **termsOfUse** a pak zahrňte **termsOfUse** jako atribut uživatele.

## <a name="next-steps"></a>Další kroky

Další informace o přizpůsobení uživatelského rozhraní aplikací [najdete v části Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad ve službě Azure Active Directory B2C](custom-policy-ui-customization.md).
