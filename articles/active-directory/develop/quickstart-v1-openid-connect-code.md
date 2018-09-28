---
title: Vytvoření webové aplikace Node.js Express pro přihlašování a odhlašování pomocí Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak vytvořit webovou aplikaci Node.js Express se softwarovou architekturou MVC, která se pro přihlašování integruje s Azure AD.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19563f76c261fda1fca53babcb553f2dceeaa345
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990089"
---
# <a name="quickstart-build-a-nodejs-express-web-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Rychlý start: Vytvoření webové aplikace Node.js Express pro přihlašování a odhlašování pomocí Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Passport je ověřovací middleware pro Node.js. Passport je flexibilní a modulární a lze ho snadno nainstalovat v jakékoli webové aplikaci využívající Express nebo Restify. Komplexní sada strategií podporuje ověřování pomocí uživatelského jména a hesla, Facebooku, Twitteru a dalších možností. Pro Azure Active Directory (Azure AD) nainstalujeme tento modul a potom přidáme modul plug-in `passport-azure-ad` služby Azure AD.

V tomto rychlém startu se naučíte používat Passport k těmto činnostem:

* Přihlášení uživatele k aplikaci pomocí Azure AD
* Zobrazení informací o uživateli
* Odhlášení uživatele z aplikace

Pokud chcete sestavit úplnou funkční aplikaci, budete muset:

1. Zaregistrovat aplikaci
2. Nastavit aplikaci tak, aby používala strategii `passport-azure-ad`
3. Použít Passport pro zasílání požadavků na přihlášení a odhlášení do Azure AD
4. Vytisknout data o uživateli

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* [Stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)
  
    * Tuto kostru naklonujte:

        ```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

    Kód k tomuto rychlému startu je udržován [na GitHubu](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Hotová aplikace je také k dispozici na konci tohoto rychlého startu.

* Musíte mít tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ho ještě nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-app"></a>Krok 1: Zaregistrujte aplikaci

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V nabídce v horní části stránky vyberte svůj účet. Ze seznamu **Adresář** zvolte tenanta Active Directory, do kterého chcete aplikaci zaregistrovat.
1. V nabídce na levé straně obrazovky vyberte **Všechny služby** a pak vyberte **Azure Active Directory**.
1. Vyberte **Registrace aplikací** a potom vyberte **Přidat**.
1. Podle zobrazovaných výzev vytvořte **webovou aplikaci** nebo **WebAPI**.

    * **Název** aplikace vaši aplikaci popisuje uživatelům.
    * **Přihlašovací adresa URL** je základní adresou URL vaší aplikace. Výchozí hodnota kostry je `http://localhost:3000/auth/openid/return`.

1. Potom, co se zaregistrujete, přiřadí Azure AD aplikaci jedinečné ID aplikace. Tuto hodnotu budete potřebovat v následujících částech, takže si ji ze stránky aplikace zkopírujte.
1. Ze stránky **Nastavení > Vlastnosti** pro vaši aplikaci aktualizujte identifikátor URI ID aplikace. 
    
    **Identifikátor URI ID aplikace** je jedinečný identifikátor pro vaši aplikaci. Používá se pojmenovávací konvence v tomto formátu: `https://<tenant-domain>/<app-name>` (například: `https://contoso.onmicrosoft.com/my-first-aad-app`).

1. Ze stránky **Nastavení > Adresy URL pro odpověď** pro vaši aplikaci přidejte adresu URL přidanou v přihlašovací adrese URL z kroku 5 a vyberte **Uložit**.
1. Tajný kód vytvoříte podle kroku 4 v části tématu věnované [přidávání přihlašovacích údajů aplikací nebo oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Zkopírujte hodnotu klíče aplikace. Jedná se o hodnotu parametru `clientSecret`, kterou budete potřebovat pro **Krok 3** níže. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>Krok 2: Přidání požadovaných součástí do adresáře

1. Na příkazovém řádku přejděte do kořenové složky, pokud v ní ještě nejste, a potom spusťte následující příkazy:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

1. Také potřebujete `passport-azure-ad`, proto spusťte následující příkaz:

    * `npm install passport-azure-ad`

Tím se nainstalují knihovny potřebné pro `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3: Nastavení aplikace pro použití strategie passport-node-js

Tady nakonfigurujte middleware Express na používání ověřovacího protokolu OpenID Connect. Passport slouží k provádění nejrůznějších operací, včetně vydávání žádostí o přihlášení a odhlášení, správy relace uživatele a získávání informací o uživateli.

1. V kořenovém adresáři projektu otevřete soubor `config.js` a v oddílu `exports.creds` potom zadejte hodnoty konfigurace vaší aplikace.

    * `clientID` je **ID aplikace** přiřazené vaší aplikaci na portálu registrace.
    * `returnURL` je **adresa URL pro odpověď**, kterou jste zadali na portálu.
    * `clientSecret` je tajný kód, který jste vygenerovali na portálu.

1. Dále otevřete soubor `app.js` umístěný v kořenovém adresáři projektu. Potom přidejte následující volání pro vyvolání strategie `OIDCStrategy` s `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

1. Potom použijte právě zmíněnou strategii pro zpracování požadavků na přihlášení.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
        process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
            if (err) {
            return done(err);
            }
            if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
            }
            return done(null, user);
        });
        });
    }
    ));
    ```
   Passport používá podobný vzorec pro všechny svoje strategie (Twitter, Facebook atd.), kterým se řídí všichni autoři strategií. Když se na strategii podíváte, uvidíte, že jí předáváme funkci s parametry token a done. Po dokončení veškeré práce se k nám strategie vrátí. Potom chceme provést uložení uživatele a skrytí tokenu, abychom je nemuseli požadovat znovu.

   > [!IMPORTANT]
   > Předchozí kód přijímá jakéhokoli uživatele, který se na našem serveru ověří. To se označuje jako automatická registrace. Doporučujeme vám neumožnit ověření žádného uživatele na produkčním serveru bez toho, aby se nejdříve zaregistroval pomocí vámi určeného procesu. To je obvykle vzor, který můžete vidět u uživatelských aplikací, které vám umožňují zaregistrovat se pomocí Facebooku, ale potom vás požádají o poskytnutí dodatečných informací. Pokud by se nejednalo o ukázkovou aplikaci, mohli jsme vyextrahovat e-mailovou adresu uživatele z vráceného objektu tokenu a potom uživatele vyzvat k vyplnění dodatečných informací. Vzhledem k tomu, že se jedná o testovací server, přidáme uživatele do databáze v paměti.

1. Přidejte metody, které nám umožňují sledovat přihlášené uživatele podle požadavků služby Passport. Tyto metody zahrnují serializaci a deserializaci informací o uživateli.

    ```JavaScript
    // Passport session setup. (Section 2)

    //   To support persistent sign-in sessions, Passport needs to be able to
    //   serialize users into the session and deserialize them out of the session. Typically,
    //   this is done simply by storing the user ID when serializing and finding
    //   the user by ID when deserializing.
    passport.serializeUser(function(user, done) {
        done(null, user.email);
    });

    passport.deserializeUser(function(id, done) {
        findByEmail(id, function (err, user) {
            done(err, user);
        });
    });

    // array to hold signed-in users
    var users = [];

    var findByEmail = function(email, fn) {
        for (var i = 0, len = users.length; i < len; i++) {
            var user = users[i];
            log.info('we are using user: ', user);
            if (user.email === email) {
                return fn(null, user);
            }
        }
        return fn(null, null);
    };
    ```

1. Přidejte kód pro načtení modulu Express. Tady používáme výchozí vzorec /views a /routes, který poskytuje middleware Express.

    ```JavaScript
    // configure Express (section 2)

      var app = express();
      app.configure(function() {
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.logger());
      app.use(express.methodOverride());
      app.use(cookieParser());
      app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
      app.use(bodyParser.urlencoded({ extended : true }));
      // Initialize Passport!  Also use passport.session() middleware, to support
      // persistent login sessions (recommended).
      app.use(passport.initialize());
      app.use(passport.session());
      app.use(app.router);
      app.use(express.static(__dirname + '/../../public'));
    });
    ```

1. Nakonec přidejte trasy, které modulu `passport-azure-ad` předávají samotné požadavky na přihlášení:

    ```JavaScript
    // Our Auth routes (section 3)

    // GET /auth/openid
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. The first step in OpenID authentication involves redirecting
    //   the user to their OpenID provider. After authenticating, the OpenID
    //   provider redirects the user back to this application at
    //   /auth/openid/return.
    app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
        log.info('Authentication was called in the Sample');
        res.redirect('/');
    });

    // GET /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.get('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });

    // POST /auth/openid/return
    //   Use passport.authenticate() as route middleware to authenticate the
    //   request. If authentication fails, the user is redirected back to the
    //   sign-in page. Otherwise, the primary route function is called,
    //   which, in this example, redirects the user to the home page.
    app.post('/auth/openid/return',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('We received a return from AzureAD.');
        res.redirect('/');
      });
    ```

## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Krok 4: Použití Passportu pro zasílání požadavků na přihlášení a odhlášení do Azure AD

Vaše aplikace je nyní správně nastavená pro komunikaci s koncovým bodem pomocí ověřovacího protokolu OpenID Connect. `passport-azure-ad` se už postaral o všechny podrobnosti ohledně vytváření ověřovacích zpráv, ověřování tokenů z Azure AD a udržování uživatelských relací. Už jen zbývá umožňovat uživatelům přihlášení a odhlášení a sbírat dodatečné informace o přihlášených uživatelích.

1. Do souboru `app.js` přidejte metody výchozí, přihlášení, účet a odhlášení:

    ```JavaScript
    //Routes (section 4)

    app.get('/', function(req, res){
      res.render('index', { user: req.user });
    });

    app.get('/account', ensureAuthenticated, function(req, res){
      res.render('account', { user: req.user });
    });

    app.get('/login',
      passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
      function(req, res) {
        log.info('Login was called in the Sample');
        res.redirect('/');
    });

    app.get('/logout', function(req, res){
      req.logout();
      res.redirect('/');
    });
    ```

1. Projděte si podrobně:

    * Trasa `/` se přesměruje na zobrazení index.ejs předáním uživatele v požadavku (pokud existuje).
    * Cesta `/account` nejprve *zajistí, že jsme ověření* (implementujeme to v následujícím příkladu), a potom předá uživatele v požadavku, abychom o uživateli mohli získat další informace.
    * Trasa `/login` volá azuread-openidconnect authenticator z `passport-azuread`. Pokud dojde k neúspěchu, trasa přesměruje uživatele zpět na /login.
    * Trasa `/logout` jednoduše volá logout.ejs (a trasu), což vymaže soubory cookie a pak vrátí uživatele zpět na index.ejs.

1. Pro poslední část `app.js` přidejte metodu **EnsureAuthenticated**, která se používá v `/account`, jak je uvedeno výše.

    ```JavaScript
    // Simple route middleware to ensure user is authenticated. (section 4)

    //   Use this route middleware on any resource that needs to be protected. If
    //   the request is authenticated (typically via a persistent sign-in session),
    //   the request proceeds. Otherwise, the user is redirected to the
    //   sign-in page.
    function ensureAuthenticated(req, res, next) {
      if (req.isAuthenticated()) { return next(); }
      res.redirect('/login')
    }
    ```

1. Nakonec v `app.js` vytvořte samotný server:

    ```JavaScript
    app.listen(3000);
    ```

## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5: Vytvořte zobrazení a trasy v Expressu pro zobrazení našeho uživatele na webu

Dokončili jsme `app.js`. Jednoduše musíte přidat trasy a zobrazení, které zobrazují informace získané o uživateli, a zároveň musíte zpracovat trasy `/logout` a `/login`, které jsme vytvořili.

1. V kořenovém adresáři vytvořte trasu `/routes/index.js`.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

1. V kořenovém adresáři vytvořte trasu `/routes/user.js`.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

    Ty předají požadavek našim zobrazením, včetně uživatele, pokud je přítomen.

1. V kořenovém adresáři vytvořte zobrazení `/views/index.ejs`. Toto je jednoduchá stránka, která volá naše metody přihlášení a odhlášení a umožňuje nám shromažďovat informace o účtu. Všimněte si, že můžeme využít podmínku `if (!user)`, protože uživatel, který je předáván v požadavku, je důkazem toho, že máme přihlášeného uživatele.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

1. V kořenovém adresáři vytvořte zobrazení `/views/account.ejs` pro zobrazení dodatečných informací, které `passport-azure-ad` vložil do uživatelského požadavku.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
    <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

1. Přidejte rozložení pro vylepšení vzhledu stránky. V kořenovém adresáři vytvořte zobrazení `/views/layout.ejs`.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="step-6-build-and-run-your-app"></a>Krok 6: Sestavení a spuštění aplikace

1. Spusťte `node app.js` a pak přejděte na `http://localhost:3000`.
1. Přihlaste se pomocí osobního nebo pracovního/školního účtu Microsoft.

    Všimněte si, jak se identita uživatele projevila v seznamu /account. Teď máte webovou aplikaci, která je zabezpečená pomocí standardních oborových protokolů, které mohou ověřovat uživatele pomocí jejich osobních a pracovních/školních účtů.

    Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [k dispozici jako soubor .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/master.zip). Alternativní možností je naklonování z GitHubu:

    ```git clone --branch master https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

## <a name="next-steps"></a>Další kroky

Můžete teď pokračovat dál a vyzkoušet další scénáře:

> [!div class="nextstepaction"]
> [Zabezpečení webového rozhraní API pomocí Azure AD](quickstart-v1-nodejs-webapi.md)