---
title: Přizpůsobení uživatelského rozhraní (UI) v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma na funkce přizpůsobení uživatelského rozhraní (UI) v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 385c13194063761d6449fafa49714d8627f6c6fc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37447049"
---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: Přizpůsobení uživatelského rozhraní (UI) Azure AD B2C

Činnost koncového uživatele je prvořadá v aplikací určených pro zákazníky.  Rozšiřte vaši zákaznickou základnu tím, že vytvoří uživatelské prostředí s vzhledu a chování vaší značky. Azure Active Directory B2C (Azure AD B2C) umožňuje přizpůsobit profil registrace, přihlašování, úpravy a stránky s ovládacím prvkem perfektním vzhledem resetování hesla.

> [!NOTE]
> Funkci přizpůsobení uživatelského rozhraní stránky popsaných v tomto článku se nevztahují na pouze zásady přihlašování, jeho související stránce pro resetování hesla a ověření e-mailů.  Tyto funkce používají [firemní branding funkce](../active-directory/fundamentals/customize-branding.md) místo.
>
> Podobně pokud uživatel intiates zásady úprav profilu *před* přihlášení uživatele budete přesměrováni na stránku, kterou lze přizpůsobit pomocí [firemní branding funkce](../active-directory/fundamentals/customize-branding.md).

Tento článek obsahuje následující témata:

* Funkce pro přizpůsobení uživatelského rozhraní stránky.
* Nástroj pro nahrávání obsahu HTML do Azure Blob Storage pro použití s funkcí přizpůsobení uživatelského rozhraní stránky.
* Prvky uživatelského rozhraní, používá Azure AD B2C, můžete přizpůsobit pomocí stylů CSS (Cascading Style).
* Osvědčené postupy při práv vyplývajících z této funkce.

## <a name="the-page-ui-customization-feature"></a>Funkce přizpůsobení uživatelského rozhraní stránky

Můžete přizpůsobit vzhled a chování zákazníků registrace, přihlášení (viz výše Poznámka pro výjimky související s branding), resetování hesla a úpravy profilu stránky (tím, že nakonfigurujete [zásady](active-directory-b2c-reference-policies.md)). Vašim zákazníkům získat bezproblémové prostředí při navigaci mezi vaší aplikací a stránky, které obsluhuje Azure AD B2C.

Na rozdíl od jiných služeb, kde možnosti uživatelského rozhraní, Azure AD B2C používá jednoduché a moderní přístup k přizpůsobení uživatelského rozhraní.

Zde je, jak to funguje: Azure AD B2C kód v prohlížeči vašeho zákazníka a využívá moderní přístup a volá [sdílení prostředků mezi zdroji (CORS)](http://www.w3.org/TR/cors/).  V době běhu obsah je načten z adresy URL, který zadáte v zásadách. Můžete určit různé adresy URL pro různé stránky. Po načtení z adresy URL pro váš obsah je sloučen s fragment HTML z Azure AD B2C vložili, stránka se zobrazí na vaše zákazníky. Vše, co je třeba provést je:

1. Vytvořit ve správném formátu HTML5 obsahu s prázdnou `<div id="api"></div>` element nachází někde v `<body>`. Tento element značky vloženy obsahu Azure AD B2C.
1. Hostování obsahu na koncový bod HTTPS (s CORS povolené). Všimněte si, jak získat a možnosti požadavek metody musí být povolené při konfiguraci CORS.
1. Pomocí šablon stylů CSS stylu prvků uživatelského rozhraní, které vloží Azure AD B2C.

### <a name="a-basic-example-of-customized-html"></a>Základní příklad přizpůsobené HTML

Následující příklad je základní obsah HTML, který můžete použít k testování této funkce. Použití [pomocným nástrojem pro](active-directory-b2c-reference-ui-customization-helper-tool.md) nahrání a konfigurace tohoto obsahu ve službě Azure Blob storage. Můžete si ověřit, že basic, stylizované tlačítka a pole formuláře na každé stránce jsou zobrazené a funkční.

```HTML
<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
    </body>
</html>
```

## <a name="test-out-the-ui-customization-feature"></a>Otestování funkce přizpůsobení uživatelského rozhraní

Chcete si vyzkoušet si funkce přizpůsobení uživatelského rozhraní pomocí našich ukázkový kód HTML a CSS obsah?  Nabízíme vám [pomocným nástrojem](active-directory-b2c-reference-ui-customization-helper-tool.md) , která nahrává a nakonfiguruje ukázkový obsah v úložišti objektů Blob v Azure.

> [!NOTE]
> Můžete hostovat obsah uživatelského rozhraní kdekoli: na webové servery, sítě CDN, AWS S3, sdílení systémy souborů, atd. Za předpokladu, obsah je hostovaný na veřejně dostupný koncový bod HTTPS s povolením CORS, jste připravení. Azure Blob storage se používá pouze pro ilustraci.
>

## <a name="the-ui-fragments-embedded-by-azure-ad-b2c"></a>Uživatelské rozhraní fragmenty vložené pomocí Azure AD B2C

V následujících částech fragmenty HTML5, které Azure AD B2C sloučí `<div id="api"></div>` prvek umístěný v obsahu. **Nevkládejte tyto fragmenty do vlastního obsahu HTML 5.** Ve službě Azure AD B2C je v vloží v době běhu. Použijte tyto fragmenty jako při navrhování vlastních stylů CSS (Cascading Style).

### <a name="fragment-inserted-into-the-identity-provider-selection-page"></a>Fragment vloží do "Stránka výběru zprostředkovatele identit"

Tato stránka obsahuje seznam zprostředkovatelů identity, které může uživatel vybrat během registrace nebo přihlášení. Tato tlačítka zahrnout zprostředkovatelů sociálních identit, jako je Facebook a Google + nebo místní účty (podle e-mailové adresy nebo uživatelského jména).

```HTML
<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-local-account-sign-up-page"></a>Fragment vloží do "místní účet pro zápis stránky"

Tato stránka obsahuje formulář pro místní účet pro zápis na základě e-mailovou adresu nebo jméno uživatele. Formulář může obsahovat různé vstupní ovládací prvky, jako je například pole textového zadání, pole pro zadání hesla, přepínač, jedním výběrem rozevírací seznamy a vyberte zaškrtávací políčka.

```HTML
<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-social-account-sign-up-page"></a>Fragment vloží do "stránka registrace sociálního účtu"

Tato stránka se můžou objevit při registraci pomocí existujícího účtu ze zprostředkovatele sociální identity, jako je Facebook nebo Google +.  Používá se při další informace musí být shromažďovány z koncového uživatele pomocí registračního formuláře. Tato stránka je podobně jako na místní účet stránku pro přihlášení (uvedené v předchozí části) s výjimkou pole pro zadání hesla.

### <a name="fragment-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Fragment vloží do "Unified registrace nebo přihlášení stránky"

Tato stránka zpracovává jak registrace a přihlášení zákazníky, kteří můžou využívat zprostředkovatelů sociálních identit, jako je Facebook nebo Google + nebo místním účtům.

```HTML
<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>
```

### <a name="fragment-inserted-into-the-multi-factor-authentication-page"></a>Fragment vloží do "stránka služby Multi-Factor authentication"

Na této stránce si uživatelé mohli ověřit jejich telefonních čísel (pomocí textových nebo hlasových) během registrace nebo přihlášení.

```HTML
<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>
```

### <a name="fragment-inserted-into-the-error-page"></a>Fragment vloží do "chybová stránka"

```HTML
<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>
```

## <a name="localizing-your-html-content"></a>Lokalizace obsahu HTML

Existují dva způsoby, jak lokalizovat váš obsah ve formátu HTML. Jedním ze způsobů je zapnout [přizpůsobení jazyka](active-directory-b2c-reference-language-customization.md). Když tuto funkci povolí, Azure AD B2C k předávání parametru Open ID Connect `ui-locales`, do koncového bodu.  Vaše servery obsahu můžete použít tento parametr k poskytování přizpůsobených stránky HTML, které jsou specifické pro konkrétní jazyk.

Alternativně si můžete vyžádat obsah z různých míst na základě národního prostředí, který se používá. Váš koncový bod s povoleným CORS můžete nastavit strukturu složek jako hostitele obsahu pro konkrétní jazyky. Ten správný budete volat, pokud použijete zástupné hodnoty `{Culture:RFC5646}`.  Předpokládejme například, že se jedná o identifikátor URI pro vlastní stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Můžete načíst stránku v `fr`. Pokud na stránce si vyžádá obsah HTML a CSS, je přebírání z:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="things-to-remember-when-building-your-own-content"></a>Co je potřeba pamatovat i při vytváření vlastního obsahu

Pokud máte v úmyslu používat funkci přizpůsobení uživatelského rozhraní stránky, projděte si následující osvědčené postupy:

* Není zkopírujte výchozí obsah Azure AD B2C a pokuste se jej upravit. Doporučujeme vytvářet obsah HTML5 úplně od začátku a použít výchozí obsah jako odkaz.
* Z bezpečnostních důvodů jsme není umožňují zahrnout všechny jazyka JavaScript v obsahu. Většinu toho, co potřebovat by měla být k dispozici ihned. Pokud ne, použijte [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) požádat o nové funkce.
* Podporované verze prohlížeče:
  * Internet Explorer 11, 10, Edge
  * Omezená podpora pro aplikaci Internet Explorer 9, 8
  * Google Chrome 42.0 a vyšší
  * Mozilla Firefox 38.0 a vyšší
* Ujistěte se, že nechcete zahrnout `<form>` značky v kódu HTML, jak to bude v konfliktu s operací POST generovaný vložený kód HTML z Azure AD B2C.
