---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5f7cbdd98d25855e9b8bb102413bd71148193318
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133871"
---
### <a name="server-auth"></a>Postup: Ověřování pomocí zprostředkovatele (tok na straně serveru)
Pokud chcete, aby funkce Mobile Apps spravovala proces ověřování ve vaší aplikaci, je třeba aplikaci zaregistrovat u vašeho zprostředkovatele identity. Potom je nutné ve službě Azure App Service nakonfigurovat ID aplikace a tajný klíč, který vám poskytne zprostředkovatel.
Další informace najdete v kurzu [Přidání ověřování do aplikace](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Jakmile budete zaregistrováni u zprostředkovatele identity, zavolejte metodu `.login()` s názvem vašeho zprostředkovatele. Například, přihlásit se přes Facebook použijte následující kód:

```
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Platné hodnoty pro zprostředkovatele jsou „aad“, „facebook“, „google“, „microsoftaccount“ a „twitter“.

> [!NOTE]
> Ověřování Google přes tok na straně serveru aktuálně nefunguje.  K ověřování Google je třeba použít [metodu toku na straně klienta](#client-auth).

V tomto případě služba Azure App Service spravuje tok ověřování OAuth 2.0.  Zobrazí přihlašovací stránku vybraného zprostředkovatele a po úspěšném přihlášení pomocí zprostředkovatele identity vygeneruje ověřovací token služby App Service. Funkce login po dokončení vrátí objekt JSON, který vystaví ID uživatele a ověřovací token služby App Service v polích userId a authenticationToken. Tento token se může uložit do mezipaměti a znovu požívat do vypršení platnosti.

### <a name="client-auth"></a>Postup: Ověřování pomocí zprostředkovatele (tok na straně klienta)

Vaše aplikace také může nezávisle kontaktovat zprostředkovatele identity a následně poskytnout navrácený token službě App Service k ověření. Tento tok na straně klienta vám umožní poskytnout uživatelům jednotné přihlašování nebo od zprostředkovatele identity načíst další uživatelská data.

#### <a name="social-authentication-basic-example"></a>Základní příklad sociálního ověřování

Tento příklad používá k ověřování klientskou sadu SDK Facebooku:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
Tento příklad předpokládá, že token poskytnutý příslušnou sadou SDK zprostředkovatele je uložený v proměnné token.

#### <a name="microsoft-account-example"></a>Příklad s účtem Microsoft

Následující příklad používá sadu Live SDK, která podporuje jednotné přihlašování v aplikacích pro Windows Store pomocí účtu Microsoft:

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now signed in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

Tento příklad získá token ze služby Live Connect a zavoláním funkce login jej předá vaší službě App Service.

### <a name="auth-getinfo"></a>Postup: Získání informací o ověřeném uživateli

Ověřovací informace můžete načíst z koncového bodu `/.auth/me` voláním HTTP pomocí libovolné knihovny AJAX.  Ujistěte se, že jste hlavičku `X-ZUMO-AUTH` nastavili na váš ověřovací token.  Ověřovací token je uložen v `client.currentUser.mobileServiceAuthenticationToken`.  Například pokud chcete použít rozhraní Fetch API:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Fetch je k dispozici jako [balíček npm](https://www.npmjs.com/package/whatwg-fetch) nebo ke stažení přes prohlížeč na webu [CDNJS](https://cdnjs.com/libraries/fetch). K načtení informací můžete použít také jQuery nebo jiné rozhraní AJAX API.  Přijatá data jsou ve formátu objektu JSON.
