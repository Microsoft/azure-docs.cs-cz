---
title: Webové aplikace Azure AD Node.js Začínáme | Microsoft Docs
description: Naučte se vytvářet webové aplikace Node.js Express MVC, která se integruje se službou Azure AD pro přihlášení.
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
ms.openlocfilehash: 8068e193ac267ef23bdaab1fc23bc7bf9b0d330b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Začínáme se službou Azure AD Node.js webové aplikace
Tady používáme Passport:

* Uživatele přihlaste k aplikaci pomocí Azure Active Directory (Azure AD).
* Zobrazí informace o uživateli.
* Přihlášení uživatele mimo aplikaci.

Passport je ověřovací middleware pro Node.js. Flexibilní a modulární, Passport lze snadno vyřadit k žádnému využívající Express nebo restify webové aplikace. Komplexní sada strategií podporují ověřování pomocí uživatelského jména a hesla, Facebook, Twitter a další. Vyvinuli jsme strategii pro Microsoft Azure Active Directory. Jsme nainstalujete tento modul a poté přidejte Microsoft Azure Active Directory `passport-azure-ad` modulu plug-in.

Chcete-li to provést, proveďte následující kroky:

1. Zaregistrujte aplikaci.
2. Nastavení aplikace pro použití `passport-azure-ad` strategie.
3. Použít Passport pro zasílání požadavků na přihlášení a odhlášení do Azure AD.
4. Tisknout data o uživateli.

Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Chcete-li sledovat, můžete [stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) nebo tuto kostru klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Dokončená aplikace je k dispozici na konci tohoto kurzu také.

## <a name="step-1-register-an-app"></a>Krok 1: Registrace aplikace
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce v horní části stránky vyberte svůj účet. V části **Directory** vyberte klienta služby Active Directory, kde chcete registrace vaší aplikace.

3. Vyberte **všechny služby** v nabídce na levé straně obrazovky a pak vyberte **Azure Active Directory**.

4. Vyberte **registrace aplikace**a potom vyberte **přidat**.

5. Postupujte podle výzev a vytvořte **webové aplikace** nebo **WebAPI**.
  * **Název** aplikace popisuje vaší aplikace pro uživatele.

  * **Přihlašovací adresa URL** je základní adresu URL aplikace. Výchozí hodnota kostru je `http://localhost:3000/auth/openid/return`.

6. Po registraci, Azure AD přiřadí vaší aplikace ID jedinečný aplikace. Je třeba tuto hodnotu v následujících částech, zkopírujte jej ze stránky aplikace.
7. Z **nastavení** -> **vlastnosti** stránky pro aplikace, aktualizujte identifikátor ID URI aplikace. **Identifikátor ID URI aplikace** je jedinečný identifikátor pro vaši aplikaci. Konvence, je použít formát `https://<tenant-domain>/<app-name>`, například: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Krok 2: Přidání požadovaných součástí do adresáře
1. Z příkazového řádku, změňte adresáře na kořenové složky a pokud si nejste již existuje, a poté spusťte následující příkazy:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Kromě toho musíte `passport-azure-ad`:
    * `npm install passport-azure-ad`

Tím se nainstaluje do knihoven, `passport-azure-ad` závisí na.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Krok 3: Nastavení aplikace k použití strategie passport uzlu js
Zde jsme nakonfigurovat Express pro použití ověřovacího protokolu OpenID Connect. Passport umožňuje provádět různé akce, včetně požadavků na přihlášení a odhlášení problém, spravovat relace uživatele a získat informace o uživateli.

1. Chcete-li začít, otevřete `config.js` souboru v kořenovém adresáři projektu a potom zadejte hodnoty konfigurace vaší aplikace v `exports.creds` oddílu.

  * `clientID` Je **Id aplikace** přiřazené vaší aplikaci v portálu pro registraci.

  * `returnURL` Je **identifikátor Uri pro přesměrování** kterou jste zadali v portálu.

  * `clientSecret` Je tajný klíč, který jste vygenerovali na portálu.

2. Dále otevřete `app.js` soubor v kořenovém adresáři projektu. Pak přidejte následující volání pro vyvolání `OIDCStrategy` strategie, která se dodává s `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Potom použijte strategii jsme právě přidanou pro zpracování naše žádostí o přihlášení.

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
Passport používá podobný Princip pro všechny svoje strategie (Twitteru, Facebooku a tak dále), které řídí všichni autoři strategií k. Prohlížení strategie, uvidíte, že jsme předáváme funkci, která má token a done jako parametry. Strategie vrátí do us po jeho činnosti provede. Potom chceme uložení uživatele a skrytí tokenu, takže jsme nemusíte požadovat znovu.

> [!IMPORTANT]
Předchozí kód přijímá jakéhokoli uživatele, které dochází k ověřování na našem server. To se označuje jako Automatická registrace. Doporučujeme vám, že nedošlo každý, kdo ověření na provozním serveru, aniž by bylo nejdříve je registrovat prostřednictvím procesu, který se rozhodnete. To je obvykle vzor, který můžete vidět u uživatelských aplikací, které umožňují registraci pomocí Facebooku, ale poté vás požádají o poskytují další informace. Pokud to nebyly ukázkovou aplikaci, mohli bychom extrahovat e-mailovou adresu uživatele z tokenu objektu, který se vrátí a poté požádat uživatele k vyplnění dodatečných informací. Protože se jedná o testovací server, jsme je přidat k databázi v paměti.


4. V dalším kroku přidejme metody, které umožní, abychom mohli sledovat přihlášených uživatelů podle požadavků Passport. Tyto metody zahrnují serializaci a deserializaci informací o uživateli.

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

5. Dále umožňuje přidat kód pro načtení modulu Express. Tady používáme výchozí /views a poskytuje /routes vzor, který Express.

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

6. Nakonec přidejme tras, které přebírají skutečné přihlášení žádosti, které chcete `passport-azure-ad` modul:

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


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Krok 4: Použití služby Passport pro zasílání požadavků na přihlášení a odhlášení do Azure AD
Aplikace je nyní správně nakonfigurován pro komunikaci s koncovým bodem pomocí ověřovacího protokolu OpenID Connect. `passport-azure-ad` má postaráno všechny podrobnosti o věnujte zpráv ověřování, ověřování tokenů z Azure AD a údržbě uživatelských relací. Všechny, které zůstává je udělení uživatelům způsob, jak přihlášení a odhlášení a shromažďování Další informace o přihlášených uživatelů.

1. Nejprve přidejme výchozí, přihlášení, účet a odhlášení metody pro naše `app.js` souboru:

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

2. Pojďme si podrobněji:

  * `/`Trasa přesměruje na zobrazení index.ejs předávání uživatele v požadavku (pokud existuje).
  * `/account` Směrovat nejprve *zajistí jsme se ověří* (jsme implementovat, v následujícím příkladu) a poté předá uživatele v požadavku tak, aby se nám můžete získat další informace o uživateli.
  * `/login` Trasy volá naše azuread openidconnect authenticator z `passport-azuread`. Pokud není úspěšné, přesměruje uživatele zpět na Publikace1.
  * `/logout` Směrování jednoduše volání logout.ejs (a trasy), které vymaže soubory cookie a poté vrátí uživatele zpět na index.ejs.

3. Pro poslední část `app.js`, přidejme **EnsureAuthenticated** metoda, která se používá v `/account`, jako je uvedené výše.

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

4. Nakonec vytvořte samotný server v `app.js`:

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Krok 5: Pokud chcete zobrazit naše uživatele na webu, zobrazení a vytvořte tras ve Express
Nyní `app.js` dokončení. Musíme jednoduše přidat trasy a zobrazení, které zobrazují informace jsme získat uživateli, stejně jako zpracování `/logout` a `/login` tras, které jsme vytvořili.

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

 Tyto předají požadavek na našem zobrazení, včetně uživatele, pokud je k dispozici.

3. V kořenovém adresáři vytvořte zobrazení `/views/index.ejs`. Toto je jednoduchá stránka, která volá metody, naše přihlášení a odhlášení a umožňuje nám se získat informace o účtu. Všimněte si, že budeme moci použít podmínku `if (!user)` jako uživatel předávány prostřednictvím v požadavku je důkaz máme přihlášeného uživatele.

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

4. Vytvořte `/views/account.ejs` zobrazení pod kořenovým adresářem, takže jsme můžete zobrazit další informace, `passport-azure-ad` pozastavil v požadavku uživatele.

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

5. Umožňuje, aby tento vzhled dobrý přidání rozložení. Vytvořte ' / zobrazení se views/layout.ejs v kořenovém adresáři.

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

Přihlaste se pomocí osobního účtu Microsoft nebo pracovní nebo školní účet a Všimněte si, jak se v seznamu /account projeví identitu uživatele. Nyní máte webovou aplikaci, která je zabezpečen pomocí standardních oborových protokolech, které může ověřit uživatele s svoje osobní, tak i pracovní nebo školní účty.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [k dispozici jako soubor .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Alternativně můžete klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Nyní se můžete přesunout na pokročilejší témata. Můžete se pokusit:

[Zabezpečení webového rozhraní API pomocí Azure AD](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
