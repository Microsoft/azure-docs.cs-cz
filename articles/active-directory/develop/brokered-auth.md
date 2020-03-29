---
title: Zprostředkované ověřování v systému Android | Azure
titlesuffix: Microsoft identity platform
description: Přehled zprostředkovaného ověřování & autorizaci pro Android v platformě microsoft identity
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697893"
---
# <a name="brokered-authentication-in-android"></a>Zprostředkované ověřování v systému Android

K účasti v jednotném přihlašování (SSO) pro celé zařízení a ke splnění zásad podmíněného přístupu organizace je nutné použít jednoho z zprostředkovatelů ověřování společnosti Microsoft. Integrace s makléřem poskytuje následující výhody:

- Jednotné přihlašování zařízení
- Podmíněný přístup pro:
  - Intune App Protection
  - Registrace zařízení (připojení k pracovišti)
  - Správa mobilních zařízení
- Správa účtů pro celé zařízení
  -  přes Android AccountManager & nastavení účtu
  - "Pracovní účet" - vlastní typ účtu

V systému Android je Zprostředkovatel ověřování Microsoftu součástí, která je součástí [aplikace Microsoft Authenticator App](https://play.google.com/store/apps/details?id=com.azure.authenticator) a [portálu společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Pouze jedna aplikace, která hostuje makléře, bude aktivní jako makléř najednou. Která aplikace je aktivní jako zprostředkovatel je určena pořadí instalace na zařízení. První, který má být nainstalován, nebo poslední dárek v zařízení, se stane aktivním zprostředkovatelem.

Následující diagram znázorňuje vztah mezi vaší aplikací, Knihovnou ověřování Microsoftu (MSAL) a zprostředkovateli ověřování společnosti Microsoft.

![Diagram nasazení zprostředkovatele](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalace aplikací, které jsou hostitelem brokera

Aplikace pro hostování makléřů může vlastník zařízení kdykoli nainstalovat z obchodu s aplikacemi (obvykle Obchod Google Play). Některá pravidla api (prostředky) jsou však chráněny zásadami podmíněného přístupu, které vyžadují, aby zařízení byla:

- Registrovaní (připojeni k pracovišti) a/nebo
- Zaregistrováno ve správě zařízení nebo
- Registrace v ochraně aplikací Intune

Pokud zařízení ještě nemá nainstalovanou aplikaci broker, MSAL pokyn uživateli k instalaci, jakmile se aplikace pokusí získat token interaktivně. Aplikace pak bude muset vést uživatele prostřednictvím kroků, aby zařízení kompatibilní s požadovanou zásadou.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Účinky instalace a odinstalování makléře

### <a name="when-a-broker-is-installed"></a>Při instalaci makléře

Když je zprostředkovatel nainstalován na zařízení, všechny následné požadavky `acquireToken()`na interaktivní tokeny (volání) jsou zpracovány zprostředkovatelem spíše než místně MSAL. Jakýkoli stav služby SSO dříve k dispozici MSAL není k dispozici zprostředkovatele. V důsledku toho bude uživatel muset znovu ověřit nebo vybrat účet z existujícího seznamu účtů známých zařízení.

Instalace zprostředkovatele nevyžaduje, aby se uživatel znovu přihlásil. Pouze v případě, že `MsalUiRequiredException` uživatel potřebuje vyřešit bude další požadavek přejít na zprostředkovatele. `MsalUiRequiredException`je vyvolána z mnoha důvodů a je třeba ji vyřešit interaktivně. To to jsou některé běžné důvody:

- Uživatel změnil heslo přidružené k jejich účtu.
- Uživatelský účet již nesplňuje zásady podmíněného přístupu.
- Uživatel odvolal svůj souhlas s tím, aby byla aplikace přidružena k jeho účtu.

### <a name="when-a-broker-is-uninstalled"></a>Když je makléř odinstalován

Pokud je nainstalována pouze jedna hostitelská aplikace brokera a je odebrána, uživatel se bude muset znovu přihlásit. Odinstalování aktivního zprostředkovatele odebere účet a přidružené tokeny ze zařízení.

Pokud je portál společnosti Intune nainstalovaný a funguje jako aktivní zprostředkovatel a nainstaluje se také Microsoft Authenticator, pak pokud je odinstalován portál společnosti Intune (aktivní zprostředkovatel), uživatel se bude muset znovu přihlásit. Jakmile se znovu přihlásí, aplikace Microsoft Authenticator se stane aktivním zprostředkovatelem.

## <a name="integrating-with-a-broker"></a>Integrace s makléřem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generování identifikátoru URI přesměrování pro zprostředkovatele

Je nutné zaregistrovat identifikátor URI přesměrování, který je kompatibilní s zprostředkovatelem. Identifikátor URI přesměrování pro zprostředkovatele musí obsahovat název balíčku vaší aplikace a také reprezentaci podpisu aplikace zakódovanou 64.

Formát identifikátoru URI přesměrování je:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Vygenerujte svůj podpis s kódovaným iurlovou adresou Base64 pomocí podpisových klíčů aplikace. Zde je několik příkladů příkazů, které používají ladicí podpisové klíče:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Informace o podepisování aplikace najdete v tématu [Podepisování](https://developer.android.com/studio/publish/app-signing) aplikace.

> [!IMPORTANT]
> Použijte produkční podpisový klíč pro produkční verzi aplikace.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurace služby MSAL pro použití zprostředkovatele

Chcete-li použít zprostředkovatele ve vaší aplikaci, musíte dosvědčit, že jste nakonfigurovali přesměrování zprostředkovatele. Zahrňte například identifikátor URI s povoleným přesměrováním zprostředkovatele a označte, že jste jej zaregistrovali, a to zahrnutím následujících položek do konfiguračního souboru Služby MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Nové rozhraní registrace aplikace portálu Azure vám pomůže generovat identifikátor URI přesměrování zprostředkovatele. Pokud jste aplikaci zaregistrovali pomocí staršího prostředí nebo jste tak učinili pomocí portálu pro registraci aplikací Microsoftu, možná budete muset vygenerovat identifikátor URI přesměrování a aktualizovat seznam identifikátorů URI přesměrování na portálu ručně.

### <a name="broker-related-exceptions"></a>Výjimky související s makléřem

MSAL komunikuje s makléřem dvěma způsoby:

- Služba vázaná na zprostředkovatele
- Správce účtů Android

MSAL nejprve používá službu vázanou na zprostředkovatele, protože volání této služby nevyžaduje žádná oprávnění Android. Pokud vazba na vázanou službu selže, msal bude používat rozhraní API Android AccountManager. MSAL to pouze v případě, že `"READ_CONTACTS"` vaše aplikace již bylo uděleno oprávnění.

Pokud se `MsalClientException` zobrazí kód `"BROKER_BIND_FAILURE"`chyby , pak existují dvě možnosti:

- Požádejte uživatele, aby zakázal optimalizaci napájení pro aplikaci Microsoft Authenticator a portál společnosti Intune.
- Požádejte uživatele o `"READ_CONTACTS"` udělení oprávnění.
