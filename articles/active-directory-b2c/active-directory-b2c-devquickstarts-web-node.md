---
title: Přidání přihlašování do webové aplikace Node.js v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak vytvořit webové aplikace v Node.js s přihlašováním uživatelů pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ee8dab24d53c7f3563d741ff74e49b0b87cda6da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442490"
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Přidání přihlašování do webové aplikace Node.js

**Passport** je ověřovací middleware pro Node.js. Passport je flexibilní a modulární a lze ho snadno nainstalovat v jakékoli webové aplikaci využívající Express nebo Restify. Komplexní sada strategií podporuje ověřování pomocí uživatelského jména a hesla, Facebooku, Twitteru a dalších.

Pro Azure Active Directory (Azure AD), nainstalujete tento modul a poté přidejte služby Azure AD `passport-azure-ad` modulu plug-in.

Budete muset:

1. Zaregistrovat aplikaci pomocí Azure AD.
2. Nastavit aplikaci tak pro používání modulu plug-in `passport-azure-ad`.
3. Použít Passport pro zasílání požadavků na přihlášení a odhlášení do Azure AD.
4. Tisknout data o uživatelích.

Kód k tomuto kurzu [je udržovaný na GitHubu](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Chcete-li kód sledovat, můžete si [stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/master.zip). Kostru můžete také klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Hotová aplikace je k dispozici na konci tohoto kurzu.

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta.  Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

Dále musíte vytvořit aplikaci v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Klientská aplikace i webové rozhraní API budou mít stejné **ID aplikace**, protože společně tvoří jednu logickou aplikaci. Chcete-li vytvořit aplikaci, postupujte podle [těchto pokynů](active-directory-b2c-app-registration.md). Ujistěte se, že:

- Jste do aplikace zahrnuli **webovou aplikaci** / **webové rozhraní API**.
- Jste do pole **Adresa URL odpovědi** vyplnili `http://localhost:3000/auth/openid/return`. To je výchozí URL pro tento příklad.
- Vytvořte pro aplikaci **tajný klíč aplikace** a poznamenejte si ho. Budete ho potřebovat později. Před tím, než tuto hodnotu použijete, musí být [uvozena v XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. To také budete potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady

V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato aplikace obsahuje tři činnosti koncového uživatele: registrace, přihlášení a přihlášení pomocí Facebooku. Tuto zásadu je třeba vytvořit pro každý typ činnosti, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Když vytváříte tyto tři zásady, nezapomeňte:

- Zvolit **Zobrazovaný název** a další atributy registrace ve svojí registrační zásadě.
- Zvolit deklarace identity aplikace **Zobrazovaný název** a **ID objektu** v každé zásadě. Můžete zvolit i další deklarace identity.
- Po vytvoření každé zásady si poznamenejte její **Název**. Měl by mít předponu `b2c_1_`.  Tyto názvy zásad budete potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Jakmile vytvoříte tyto tři zásady, budete připraveni k sestavení aplikace.

Tento článek nepopisuje používání právě vytvořených zásad. Chcete-li se dozvědět, jak fungují zásady v Azure AD B2C, začněte [kurzem Začínáme s webovými aplikacemi .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Přidání požadovaných součástí do adresáře

V příkazovém řádku přejděte do kořenové složky, pokud v ní ještě nejste. Spusťte následující příkazy:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Kromě toho jsme pro náhled v naší kostře Rychlého startu použili `passport-azure-ad`.

- `npm install passport-azure-ad`

To nainstaluje knihovny potřebné pro `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Nastavení aplikace pro použití strategie Passport-Node.js
Nakonfigurujte middleware Express pro použití ověřovacího protokolu OpenID Connect. Passport bude mimo jiné sloužit k zasílání požadavků na přihlášení a odhlášení, ke správě uživatelských relací a k získávání informací o uživatelích.

V kořenovém adresáři projektu otevřete soubor `config.js` a v oddílu `exports.creds` zadejte hodnoty konfigurace.
- `clientID`: **ID aplikace** přiřazené vaší aplikaci v portálu registrace.
- `returnURL`: **Identifikátor URI přesměrování**, který jste zadali v portálu.
- `tenantName`: Klientský název vaší aplikace, například **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Otevřete soubor `app.js` umístěný v kořenovém adresáři projektu. Přidejte následující volání pro vyvolání strategie `OIDCStrategy`, která přichází s `passport-azure-ad`.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Použijte právě přidanou strategii pro zpracování požadavků na přihlášení.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Passport používá podobný princip pro všechny strategie (včetně Twitteru a Facebooku). Tímto principem se řídí všichni tvůrci strategií. Při pohledu na strategii uvidíte, že jí předáváte `function()` s parametry token a `done`. Po dokončení veškeré práce se k vám strategie vrátí. Uložení uživatele a skrytí tokenu, takže je nebudete muset požadovat znovu.

> [!IMPORTANT]
Předchozí kód přijme všechny uživatele, ověřené serverem. To je automatická registrace. Pokud používáte produkční servery, nechcete vpustit uživatele, kteří neprošli vámi nastaveným procesem registrace. Tento princip je často k vidění u uživatelských aplikací. Ty umožňují registraci pomocí Facebooku, ale poté vás vyzvou k vyplnění dodatečných informací. Kdyby naše aplikace nebyla pouze příklad, mohli bychom extrahovat emailovou adresu z vráceného objektu tokenu a poté vyzvat uživatele k vyplnění dodatečných informací. Protože se jedná o testovací server, jednoduše přidáme uživatele do databáze v paměti.

Přidejte metody, které vám umožní sledovat přihlášené uživatele, jak vyžaduje Passport. To zahrnuje serializaci a deserializaci informací o uživateli:

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Přidejte kód pro načtení modulu Express. V následujícím příkladu vidíte, že používáme výchozí `/views` a vzor `/routes` poskytované modulem Express.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Přidání tras `POST`, které přebírají vlastní požadavky na přihlášení do modulu `passport-azure-ad`:

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Použití Passportu pro zasílání požadavků na přihlášení a odhlášení do Azure AD

Vaše aplikace je nyní správně nastavená pro komunikaci s koncovým bodem v2.0 pomocí ověřovacího protokolu OpenID Connect. `passport-azure-ad` se už postaral o podrobnosti ohledně vytváření ověřovacích zpráv, ověřování tokenů z Azure AD a udržování uživatelských relací. Už jen zbývá umožnit uživatelům přihlášení a odhlášení a sbírat dodatečné informace o přihlášených uživatelích.

Nejprve do souboru `app.js` přidejte metody výchozí, přihlášení, účet a odhlášení:

```javascript

//Routes (Section 4)

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

Chcete-li si tyto metody prohlédnout podrobně:
- Trasa `/` se přesměruje na zobrazení `index.ejs` předáním uživatele v požadavku (pokud existuje).
- Trasa `/account` nejprve ověří, zda jste přihlášeni (příklad implementace je níže). Poté předá uživatele v požadavku, abyste o něm mohli získat dodatečné informace.
- Trasa `/login` zavolá `azuread-openidconnect` Authenticator z `passport-azure-ad`. Pokud dojde k neúspěchu, trasa přesměruje uživatele zpět na `/login`.
- `/logout` jednoduše volá `logout.ejs` (a jeho trasu). Zruší soubory cookies a poté vrátí uživatele zpět na `index.ejs`.


V poslední části `app.js` přidejte metodu `EnsureAuthenticated`, která je použitá v trase `/account`.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Nakonec v `app.js` vytvořte samotný server.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Vytvoření zobrazení a tras v Expressu pro volání zásad

Vaše `app.js` je nyní hotová. Potřebujete pouze přidat trasy a zobrazení, které vám umožní volat zásady pro přihlášení a odhlášení. Ty zpracovávají také dříve vytvořené trasy `/logout` a `/login`.

V kořenovém adresáři vytvořte trasu `/routes/index.js`.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

V kořenovém adresáři vytvořte trasu `/routes/user.js`.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Tyto jednoduché trasy předávají požadavky zobrazením. Obsahují i uživatele, pokud je přítomen.

V kořenovém adresáři vytvořte zobrazení `/views/index.ejs`. Toto je jednoduchá stránka, která volá zásady pro přihlášení a odhlášení. Můžete ji také použít ke sběru informací o účtu. Všimněte si, že můžete využít podmínku `if (!user)` při předávání uživatele v požadavku pro prokázání, že je přihlášen.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

V kořenovém adresáři vytvořte zobrazení `/views/account.ejs` pro zobrazení dodatečných informací, které `passport-azure-ad` vložil do uživatelského požadavku.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Nyní můžete sestavit a spustit svoji aplikaci.

Spusťte `node app.js` a přejděte na `http://localhost:3000`.


Pomocí emailu nebo Facebooku se zaregistrujte nebo přihlaste k aplikaci. Odhlaste se a přihlaste se jako jiný uživatel.

## <a name="next-steps"></a>Další postup

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [k dispozici jako soubor .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Můžete ho také klonovat z GitHubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Nyní se můžete přesunout k pokročilejším tématům. Můžete vyzkoušet:

[Zabezpečení webového rozhraní API pomocí modelu B2C v Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
