---
title: Ukázky JavaScriptu – Azure Active Directory B2C | Microsoft Docs
description: Další informace o použití jazyka JavaScript v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5dfc1ef732c8456356de82f7fe026476fdfc075b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227130"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Ukázky jazyka JavaScript pro použití v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Pro vaše aplikace Azure Active Directory (Azure AD) B2C můžete přidat vlastní kód jazyka JavaScript na straně klienta. Pro povolení JavaScriptu pro vaše aplikace musíte přidat element do [vlastních zásad](active-directory-b2c-overview-custom.md), vybrat [rozložení stránky](page-layout.md)a ve svých žádostech použít [b2clogin.com](b2clogin.md) . Tento článek popisuje, jak můžete změnit vlastní zásady a povolit spouštění skriptů.

> [!NOTE]
> Pokud chcete povolit JavaScript pro toky uživatelů, přečtěte si téma [verze JavaScriptu a rozložení stránky v Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Požadavky

Vyberte rozložení stránky pro prvky uživatelského rozhraní vaší aplikace. Pokud máte v úmyslu používat jazyk JavaScript, je nutné ve vlastních zásadách definovat verzi rozložení stránky pro všechny definice obsahu.

## <a name="add-the-scriptexecution-element"></a>Přidejte prvek ScriptExecution

Povolit spuštění skriptu přidáním **ScriptExecution** elementu [RelyingParty](relyingparty.md) elementu.

1. Otevřete soubor vlastní zásady. Například *SignUpOrSignin.xml*.
2. Přidat **ScriptExecution** elementu **UserJourneyBehaviors** prvek **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Uložte a odešlete soubor.

## <a name="guidelines-for-using-javascript"></a>Pokyny k používání jazyka JavaScript

Při přizpůsobování rozhraní vaší aplikace pomocí jazyka JavaScript, postupujte podle následujících pokynů:

- Událost click není vázán `<a>` elementů HTML.
- Nevyřídí závislost na Azure AD B2C kódu a v komentářích.
- Neměnit pořadí nebo hierarchie elementů HTML v Azure AD B2C. Pomocí zásady služby Azure AD B2C můžete řídit pořadí prvků uživatelského rozhraní.
- Můžete volat jakékoli služby RESTful těchto aspektů:
    - Budete muset nastavit služby RESTful CORS povolit volání HTTP na straně klienta.
    - Ujistěte se, že vaše služba RESTful je zabezpečené a využívá protokol HTTPS.
    - Nepoužívejte JavaScriptu přímo k volání koncových bodů Azure AD B2C.
- Můžete vložit JavaScript nebo můžete propojit na externí soubory jazyka JavaScript. Pokud používáte externí soubor jazyka JavaScript, nezapomeňte použít absolutní adresu URL a ne relativní adresu URL.
- Rozhraní jazyka JavaScript:
    - Azure AD B2C používá specifickou verzi jQuery. Nezahrnují jinou verzi jQuery. Použití více než jedna verze na stejné stránce způsobí problémy.
    - Použití RequireJS není podporováno.
    - Většina architektury JavaScriptu nejsou podporovány službou Azure AD B2C.
- Nastavení služby Azure AD B2C můžete číst voláním `window.SETTINGS`, `window.CONTENT` objekty, jako je aktuální jazyk uživatelského rozhraní. Neměňte hodnotu z těchto objektů.
- Pokud chcete přizpůsobit chybovou zprávu Azure AD B2C, použijte lokalizaci zásad.
- Pokud nic jde dosáhnout s použitím zásad, obecně je doporučený postup.

## <a name="javascript-samples"></a>Ukázky jazyka JavaScript

### <a name="show-or-hide-a-password"></a>Umožňuje zobrazit nebo skrýt heslo

Běžným způsobem, které vašim zákazníkům pomůžou s jejich registrace úspěchu je mohly zobrazit, co se jste zadali jako své heslo. Tato možnost pomáhá uživatelům zaregistrovat tak, že umožňuje snadno zobrazit a provádět opravy hesla v případě potřeby. Jakékoli pole zadejte heslo má zaškrtávací políčko s **zobrazit heslo** popisek.  To umožňuje uživatelům zobrazit heslo jako prostý text. Zahrnout tento fragment kódu do šablony registrace nebo přihlášení s vlastním potvrzením stránky:

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

Začleňte následující kód na vaši stránku, ve které chcete zahrnout **Terms of Use** zaškrtávací políčko. Toto políčko je obvykle potřeba na stránkách registrace registrace a sociální účtu místní účet.

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

V kódu, nahraďte `termsOfUseUrl` s odkazem na vaše smlouva o podmínkách použití. Pro váš adresář vytvořte nový atribut uživatele s názvem **termsOfUse** a pak jako atribut uživatele přidejte **termsOfUse** .

## <a name="next-steps"></a>Další postup

Další informace o tom, jak můžete přizpůsobit uživatelského rozhraní aplikací v [přizpůsobit uživatelské rozhraní vaší aplikace pomocí vlastních zásad v Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
