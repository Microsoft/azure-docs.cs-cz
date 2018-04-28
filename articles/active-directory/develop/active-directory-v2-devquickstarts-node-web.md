---
title: Azure Active Directory v2.0 Node.js webové aplikace přihlášení | Microsoft Docs
description: Naučte se vytvářet webové aplikace Node.js, který se přihlásí uživatel pomocí účtu Microsoft osobní i pracovní nebo školní účet.
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb16502ca255bf99c3780ff3975b6ca7f5a79993
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Přidání přihlašování do webové aplikace Node.js

> [!NOTE]
> Ne všechny funkce a scénáře Azure Active Directory fungovat s koncovým bodem v2.0. Pokud chcete zjistit, zda by měl používat koncového bodu v2.0 nebo koncový bod verze 1.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>

V tomto kurzu používáme [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) provádění následujících úloh:

* Ve webové aplikaci přihlaste uživatele pomocí služby Azure Active Directory (Azure AD) a koncový bod v2.0.
* Zobrazí informace o uživateli.
* Přihlášení uživatele mimo aplikaci.

[Passport](http://passportjs.org/) je ověřovací middleware pro Node.js. Flexibilní a modulární, můžete do jakéhokoli nenápadně vyřadit Passport využívající Express nebo restify webové aplikace. Komplexní sada strategií Passport, podporují ověřování pomocí uživatelského jména a hesla, Facebook, Twitter a další možnosti. Vyvinuli jsme strategii pro Azure AD. V tomto článku jsme ukazují, jak nainstalovat modul a poté přidejte Azure AD **passport-azure-ad** modulu plug-in.

## <a name="download"></a>Ke stažení
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Chcete-li postupovat v kurzu, můžete [stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) nebo tuto kostru klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Také můžete získat hotová aplikace na konci tohoto kurzu.

## <a name="1-register-an-app"></a>1: registrace aplikace
Vytvoření nové aplikace v [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle [tyto podrobné kroky](active-directory-v2-app-registration.md) k registraci aplikace. Ověřte, že je:

* Kopírování **Id aplikace** přiřazené vaší aplikaci. Budete ho potřebovat pro účely tohoto kurzu.
* Přidat **webové** platformu pro vaši aplikaci.
* Přidejte následující adresa URL konfigurovaná v ukázce `http://localhost:3000/auth/openid/return` jako **identifikátor URI pro přesměrování**.

## <a name="2-prerequisities-to-run-the-sample"></a>2: Prerequisities ke spuštění ukázky

Musíte mít nainstalován sestavení a spuštění této ukázce Node.js. Můžete nainstalovat Node.js z http://nodejs.org/.

Na příkazovém řádku změňte adresáře na přejděte do kořenové složky, pokud si nejste již existuje.
Spusťte následující příkaz pro instalaci požadovaných součástí uzlu modulů uvedených v `package.json` souboru:

`npm install`

Tím se nainstaluje taky v knihovnách, `passport-azure-ad` používá.

## <a name="3-set-up-your-app-to-use-passport-azure-ad"></a>3: nastavení aplikace pro použití služby passport-azure-ad
Nastavte Express middleware pro použití ověřovacího protokolu OpenID Connect. Používání služby Passport pro zasílání požadavků na přihlášení a odhlášení, správu relace uživatele a získat informace o uživateli, mimo jiné.

1.  V kořenovém adresáři projektu otevřete souboru config.js. V `exports.creds` zadejte hodnoty konfigurace vaší aplikace.

  * `clientID`: **Id aplikace** přiřazené vaší aplikaci na portálu.
  * `returnURL`: **Identifikátor URI pro přesměrování** kterou jste zadali v portálu.
  * `clientSecret`: Tajný klíč, který jste vygenerovali na portálu.

2.  V kořenovém adresáři projektu otevřete soubor app.js. K vyvolání OIDCStrategy, který se dodává s `passport-azure-ad`, přidejte následující volání:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Pro zpracování vaší žádosti o přihlášení, použijte strategie, kterou jste právě odkazuje:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
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

Passport používá podobný Princip pro všechny svoje strategie (Twitteru, Facebooku a tak dále). Řídí všichni autoři strategií se vzorem. Předat strategie `function()` token, který používá a `done` jako parametry. Strategie se vrátí po dělá svou práci. Uložení uživatele a skrytí tokenu, takže je nebudete muset požadovat znovu.

  > [!IMPORTANT]
  > Předchozí kód přijme všechny uživatele, který může ověřit na váš server. To se označuje jako Automatická registrace. Na provozním serveru byste neměli chtít vpouštět každý, kdo bez toho, aby předtím prošli registračním procesem, který zvolíte. To je obvykle vzor, který můžete vidět u uživatelských aplikací. Aplikace může umožňují registraci pomocí Facebooku, ale následně požádá, můžete k zadání dalších informací. Pokud v tomto kurzu nebyly pomocí příkazového řádku programu, může extrahovat e-mail z vráceného objektu tokenu. Potom může požádat uživatele k zadání dalších informací. Protože se jedná o testovací server, je třeba přidat uživatele přímo k databázi v paměti.
  >
  >

4.  Přidejte metody, které můžete použít ke sledování uživatelů, kteří se přihlásili, jak vyžaduje Passport. To zahrnuje serializaci a deserializaci informací o uživateli:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
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

5.  Přidejte kód, který načte modulu Express. Použijte výchozí /views a /routes vzor, který Express poskytuje:

  ```JavaScript

  // Set up Express (section 2)

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

6.  Přidání tras této můžete předat skutečné přihlášení žádosti, které chcete v příspěvku `passport-azure-ad` modul:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-add-sign-in-and-sign-out-requests-to-azure-ad"></a>4: přidejte požadavků na přihlášení a odhlášení do Azure AD
Aplikace je teď nastavený pro komunikaci s koncovým bodem v2.0 pomocí ověřovacího protokolu OpenID Connect. `passport-azure-ad` Strategie postará všechny podrobnosti o věnujte zpráv ověřování, ověřování tokenů z Azure AD a údržbě uživatelské relace. Je již zbývá chcete umožnit uživatelům způsob přihlášení a odhlášení a získat další informace o uživateli, který je přihlášený.

1.  Přidat **výchozí**, **přihlášení**, **účet**, a **odhlášení** metody do souboru app.js:

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
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Zde jsou uvedeny podrobnosti:

    * `/` Trasa přesměruje na zobrazení index.ejs. Pak předá uživatele v požadavku (pokud existuje).
    * `/account` Směrovat nejprve *zajistí, že jsou ověří* (implementujete, v následujícím kódu). Poté předá uživatele v požadavku. Toto je, abyste získali další informace o uživateli.
    * `/login` Směrování volání vaše `azuread-openidconnect` authenticator z `passport-azuread`. Pokud není úspěšné, ho přesměruje uživatele zpět na `/login`.
    * `/logout` Trasy volá logout.ejs zobrazení (a směrování). Vymaže soubory cookie a poté vrátí uživatele zpět na index.ejs.

2.  Přidat **EnsureAuthenticated** metoda, která jste použili výše v `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  V app.js vytvořte serveru:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express"></a>5: vytvoření zobrazení a trasy v Express
Přidáte trasy a zobrazení, které se zobrazí informace o uživateli. Trasy a zobrazení, zpracovávají také dříve `/logout` a `/login` tras, které jste vytvořili.

1. V kořenovém adresáři vytvořte `/routes/index.js` trasy.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  V kořenovém adresáři vytvořte `/routes/user.js` trasy.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` a `/routes/user.js` jsou jednoduché trasy, které předávají žádosti zobrazením, včetně uživatele, pokud je k dispozici.

3.  V kořenovém adresáři vytvořte `/views/index.ejs` zobrazení. Tato stránka volá vaše **přihlášení** a **odhlášení** metody. Můžete také použít `/views/index.ejs` zobrazení k zaznamenání informací o účtu. Můžete využít podmínku `if (!user)` jako uživatel předávány prostřednictvím v požadavku. Je důkaz, že máte přihlášení uživatele.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  V kořenovém adresáři vytvořte `/views/account.ejs` zobrazení. `/views/account.ejs` Zobrazení umožňuje zobrazit další informace, `passport-azuread` převádí na žádost uživatele.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Přidáte rozložení. V kořenovém adresáři vytvořte `/views/layout.ejs` zobrazení.

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
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```


  ## <a name="6-build-and-run-your-app"></a>6: vytvoření a spuštění aplikace
  1. Sestavení a spuštění aplikace, spusťte následující příkaz z kořenového adresáře aplikace.

  `node app.js`

   Potom přejděte na `http://localhost:3000` v prohlížeči.

  2. Přihlaste se pomocí osobního účtu Microsoft nebo pracovní nebo školní účet. Všimněte si, že identita uživatele se odrazí v `/account` trasy.

Nyní máte webovou aplikaci, která je zabezpečená službou pomocí standardních protokolů. Uživatelé ve vaší aplikaci, můžete ověřovat pomocí svoje osobní i pracovní nebo školní účty.

## <a name="next-steps"></a>Další postup
Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici jako [soubor .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Vám může ho také klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Potom můžete přesunout pokročilejší témata. Můžete se pokusit:

[Zabezpečit webové rozhraní API Node.js pomocí koncového bodu v2.0](active-directory-v2-devquickstarts-node-api.md)

Zde jsou některé další zdroje informací:

* [Příručka vývojáře v2.0 služby Azure AD](active-directory-appmodel-v2-overview.md)
* [Značka "azure-active-directory" přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme registrace oznámení o bezpečnostních incidentech. Na [technické oznámení zabezpečení Microsoft](https://technet.microsoft.com/security/dd252948) stránky, odběr výstrah zpravodaje zabezpečení.
