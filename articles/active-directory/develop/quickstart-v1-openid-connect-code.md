---
title: Azure AD webová aplikace v Node.js Začínáme | Dokumentace Microsoftu
description: Zjistěte, jak sestavit webovou aplikaci Node.js Express MVC, která se integruje s Azure AD pro přihlášení.
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
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 91cb7df9d38432d660930c21f6a9d0d64215d6e6
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577435"
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Azure AD webová aplikace v Node.js Začínáme
Tady můžeme použít Passport:

* Přihlášení uživatele k aplikaci pomocí Azure Active Directory (Azure AD).
* Zobrazení informací o uživateli.
* Přihlášení uživatele z aplikace.

Passport je ověřovací middleware pro Node.js. Flexibilní a modulární a Passport dá snadno přetáhnout na každém využívající Express nebo restify webové aplikace. Komplexní sada strategií podporují ověřování pomocí uživatelského jména a hesla, Facebook, Twitter a další. Vyvinuli jsme strategii pro Microsoft Azure Active Directory. Jsme nainstalujete tento modul a poté přidáte Microsoft Azure Active Directory `passport-azure-ad` modulu plug-in.

Chcete-li to provést, postupujte následovně:

1. Registrace aplikace.
2. Nastavení aplikace pro použití `passport-azure-ad` strategie.
3. Použít Passport pro zasílání požadavků na přihlášení a odhlášení do Azure AD.
4. Tisknout data o uživateli.

Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Chcete-li postupovat s námi, můžete [stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) nebo tuto kostru klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Dokončená aplikace je k dispozici na konci tohoto kurzu také.

## <a name="step-1-register-an-app"></a>Krok 1: Registrace aplikace
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce v horní části stránky vyberte svůj účet. V části **Directory** , zvolte tenanta Active Directory, ve které chcete zaregistrovat aplikaci.

3. Vyberte **všechny služby** v nabídce na levé straně obrazovky a pak vyberte **Azure Active Directory**.

4. Vyberte **registrace aplikací**a pak vyberte **přidat**.

5. Postupujte podle výzev a vytvořte **webovou aplikaci** a/nebo **WebAPI**.
  * **Název** aplikace popíše aplikaci uživatelům.

  * **Přihlašovací adresa URL** základní adresu URL vaší aplikace. Výchozí hodnota typu skeleton je `http://localhost:3000/auth/openid/return`.

6. Až dokončíte registraci, Azure AD přiřadí vaší aplikaci jedinečné ID. Tuto hodnotu budete potřebovat v dalších částech, tedy zkopírujte ho ze stránky aplikace.
7. Z **nastavení** -> **vlastnosti** stránce pro vaši aplikaci, aktualizujte identifikátor URI ID aplikace. **Identifikátor ID URI aplikace** je jedinečný identifikátor pro vaši aplikaci. Tato konvence je určený formát `https://<tenant-domain>/<app-name>`, například: `https://contoso.onmicrosoft.com/my-first-aad-app`.

8. Z **nastavení** -> **adresy URL odpovědí** stránce pro vaši aplikaci, přidejte adresu URL do přihlašovací adresu URL z kroku 5 a klikněte na Uložit.

9. Vytvoření tajného klíče, postupujte podle kroku 4 v [přidat přihlašovací údaje aplikací nebo oprávnění pro přístup k webovým rozhraním API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-application-credentials-or-permissions-to-access-web-apis).

   > [!IMPORTANT]
   > Zkopírujte hodnotu klíče aplikace. Jedná se o hodnotu pro `clientSecret`, které budete potřebovat pro **kroku 3** níže. 

## <a name="step-2-add-prerequisites-to-your-directory"></a>Krok 2: Přidání požadovaných součástí do adresáře
1. Z příkazového řádku změňte adresáře na vaše kořenové složky a pokud si nejste již existuje, a pak spusťte následující příkazy:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Kromě toho potřebujete `passport-azure-ad`:
    * `npm install passport-azure-ad`

Tím se nainstaluje knihovny, který `passport-azure-ad` závisí.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3: Nastavení aplikace pro použití strategie passport-node js
Jsme zde, nakonfigurujte Express pro použití ověřovacího protokolu OpenID Connect. Passport slouží k provádění nejrůznějších operací, včetně žádostí o přihlášení a odhlášení problém, spravovat relace uživatele a získat informace o uživateli.

1. Pokud chcete začít, otevřete `config.js` soubor v kořenové složce projektu a zadejte hodnoty konfigurace vaší aplikace `exports.creds` části.

  * `clientID` Je **Id aplikace** přiřazené vaší aplikaci v portálu registrace.

  * `returnURL` Je **adresy URL odpovědi** , kterou jste zadali v portálu.

  * `clientSecret` Je tajný kód, který jste vygenerovali na portálu.

2. Dále otevřete `app.js` souboru v kořenovém adresáři projektu. Pak přidejte následující volání, který má být vyvolán `OIDCStrategy` strategii, která se dodává s `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Potom použijte strategii jsme právě přidanou pro zpracování našich požadavků přihlášení.

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
   Passport používá podobný Princip pro všechny svoje strategie (Twitter, Facebook atd.), které se řídí všichni autoři strategií. Hledání na strategii uvidíte, že jsme předat funkci, která má token a Hotovo jako parametry. Strategie vrátí nám po provede svou práci. Potom chceme uložit uživatele a skrytí tokenu, proto nepotřebujeme požadovat znovu.

   > [!IMPORTANT]
   > Předchozí kód přijímá jakéhokoli uživatele, ke které dochází k ověření na našem serveru. To se označuje jako automatické registrace serveru. Doporučujeme vám, že jste Nenechte všem uživatelům bez toho registraci prostřednictvím procesu, který se rozhodnete ověřovat na produkční server. Toto je obvykle vzor, který se zobrazí u uživatelských aplikací, které umožňují zaregistrovat se pomocí Facebooku, ale poté vás požádají o poskytují další informace. Pokud to nebyly ukázkovou aplikaci, mohli bychom může extrahovat e-mailovou adresu uživatele z token objektu, který je vrácen a poté požádat uživatele k vyplnění dodatečných informací. Protože se jedná o testovací server, přidáme je do databáze v paměti.


4. V dalším kroku přidejme metody, které pomáhají sledovat přihlášených uživatelů podle požadavků služby Passport. Tyto metody zahrnují serializaci a deserializaci informací o uživateli.

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

5. V dalším kroku přidáte kód pro načtení modulu Express. Tady používáme výchozí /views a poskytuje /routes vzor, který Express.

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

6. Nakonec přidejte trasy, které přebírají skutečné přihlašovací žádosti, které chcete `passport-azure-ad` modul:

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
Vaše aplikace je teď správně nakonfigurované pro komunikaci s koncovým bodem pomocí ověřovacího protokolu OpenID Connect. `passport-azure-ad` už se postaral o všechny podrobnosti ohledně vytváření ověřovacích zpráv, ověřování tokenů z Azure AD a udržování uživatelských relací. Vše, co zůstává poskytuje uživatelům způsob, jak přihlášení a odhlášení a shromažďování dalších informací o přihlášených uživatelů.

1. Nejprve přidáme výchozí, přihlášení, účet a odhlášení metody pro naše `app.js` souboru:

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

2. Pojďme se podívat na podrobněji:

  * `/`Trasy přesměruje na zobrazení index.ejs předávání uživatele v požadavku (pokud existuje).
  * `/account` Trasy nejprve *zajišťuje jsme se ověřují* (Implementujeme, který v následujícím příkladu) a poté předá uživatele v požadavku tak, aby nám můžete získat další informace o uživateli.
  * `/login` Trasy volá naše openidconnect Azure AD authenticator z `passport-azuread`. Pokud neproběhne úspěšně, která, přesměruje uživatele zpět na Publikace1.
  * `/logout` Jednoduše volání logout.ejs (a trasu), které vymaže soubory cookie a poté vrátí uživatele zpět na index.ejs trasy.

3. Pro poslední část `app.js`, přidáme **EnsureAuthenticated** metodu, která se používá v `/account`, jak je uvedeno výše.

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

4. Nakonec vytvoříme samotný server v `app.js`:

```JavaScript
app.listen(3000);
```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5: Naší uživatelské web zobrazíte vytváření zobrazení a tras v Expressu
Nyní `app.js` je dokončena. Jednoduše je potřeba přidat trasy a zobrazení, které obsahují informace můžeme získat uživateli, stejně jako zpracování `/logout` a `/login` trasy, které jsme vytvořili.

1. V kořenovém adresáři vytvořte trasu `/routes/index.js`.

    ```JavaScript
    /*
     * GET home page.
     */

    exports.index = function(req, res){
      res.render('index', { title: 'Express' });
    };
    ```

2. V kořenovém adresáři vytvořte trasu `/routes/user.js`.

    ```JavaScript
    /*
     * GET users listing.
     */

    exports.list = function(req, res){
      res.send("respond with a resource");
    };
    ```

 Tyto máte předat požadavek na naše zobrazení, včetně uživatele, pokud jsou k dispozici.

3. V kořenovém adresáři vytvořte zobrazení `/views/index.ejs`. Toto je jednoduchá stránka, která volá metody naše přihlášení a odhlášení a umožňuje nám to sběru informací o účtu. Všimněte si, že můžeme využít podmínku `if (!user)` jako uživatele se předává v požadavku je důkazy máme přihlášeného uživatele.

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

4. Vytvořte `/views/account.ejs` zobrazení pod kořenovým adresářem, takže můžeme zobrazit další informace, které `passport-azure-ad` má vložit do uživatelského požadavku.

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
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Přidáním rozložení vytvoříme dobré tento vzhled. V kořenovém adresáři vytvořte zobrazení `/views/layout.ejs`.

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

## <a name="next-steps"></a>Další postup
Nakonec sestavte a spusťte aplikaci. Spustit `node app.js`a pak přejděte na `http://localhost:3000`.

Přihlaste se pomocí osobního účtu Microsoft nebo pracovní nebo školní účet a Všimněte si, jak identitu uživatele se projeví v seznamu/Account. Teď máte webovou aplikaci, která je zabezpečena pomocí standardních oborových protokolů, které můžete ověřovat uživatele pomocí jejich osobní, tak i pracovní nebo školní účty.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [k dispozici jako soubor .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Alternativně můžete klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Nyní se můžete přesunout na pokročilejší témata. Můžete vyzkoušet:

[Zabezpečení webového rozhraní API s využitím Azure AD](quickstart-v1-nodejs-webapi.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
