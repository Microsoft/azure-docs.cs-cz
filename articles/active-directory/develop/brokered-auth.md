---
title: Zprostředkované ověřování v Androidu | Azure
titlesuffix: Microsoft identity platform
description: Přehled zprostředkovaných ověření & autorizaci pro Android na platformě Microsoft Identity Platform
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76697893"
---
# <a name="brokered-authentication-in-android"></a>Zprostředkované ověřování v Androidu

Abyste se mohli zapojit do jednotného přihlašování (SSO) v jednotném zařízení, musíte použít jednoho z zprostředkovatelů ověřování od Microsoftu a vyhovět zásadám podmíněného přístupu na úrovni organizace. Integrace se zprostředkovatelem přináší následující výhody:

- Jednotné přihlašování zařízení
- Podmíněný přístup pro:
  - Intune App Protection
  - Registrace zařízení (Workplace Join)
  - Správa mobilních zařízení
- Správa účtů na úrovni zařízení
  -  přes Android ke správci účtů & nastavení účtu
  - Pracovní účet – vlastní typ účtu

V Androidu je zprostředkovatel ověřování Microsoft součástí, která je součástí [Microsoft Authenticator aplikace](https://play.google.com/store/apps/details?id=com.azure.authenticator) a [portál společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Pouze jedna aplikace, která je hostitelem zprostředkovatele, bude v jednom okamžiku aktivní jako zprostředkovatel. Která aplikace je aktivní jako zprostředkovatel, je určena podle pořadí instalace na zařízení. První instalace nebo poslední přítomná v zařízení se stal aktivním zprostředkovatelem.

Následující diagram znázorňuje vztah mezi vaší aplikací, Microsoft Authentication Library (MSAL) a zprostředkovateli ověřování od Microsoftu.

![Diagram nasazení zprostředkovatele](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalace aplikací, které hostují zprostředkovatele

Služby pro hostování aplikací můžou instalovat vlastník zařízení z jejich App Storu (obvykle Obchod Google Play). Některá rozhraní API (prostředky) jsou ale chráněná zásadami podmíněného přístupu, které vyžadují, aby byla zařízení:

- Registrováno (připojeno k pracovišti) a/nebo
- Zaregistrováno ve správě zařízení nebo
- Zaregistrováno v Intune App Protection

Pokud na zařízení ještě není nainstalovaná aplikace zprostředkovatele, MSAL uživateli pokyn, aby ho nainstaloval hned, jakmile se aplikace pokusí o interaktivní získání tokenu. V takovém případě bude muset uživatel provést kroky, aby zařízení dodržovalo požadované zásady.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Účinky instalace a odinstalace zprostředkovatele

### <a name="when-a-broker-is-installed"></a>Při instalaci zprostředkovatele

Když je v zařízení nainstalován zprostředkovatel, budou všechny následné požadavky na interaktivní tokeny (volání `acquireToken()` ) zpracovávány zprostředkovatelem namísto místně pomocí MSAL. Pro zprostředkovatele není k dispozici jakýkoli stav jednotného přihlašování, který je dřív dostupný pro MSAL. V důsledku toho se uživatel bude muset znovu ověřit nebo vybrat účet ze stávajícího seznamu účtů, které zařízení zná.

Instalace zprostředkovatele nepožaduje, aby se uživatel znovu přihlásil. Pouze v případě, že uživatel potřebuje vyřešit, `MsalUiRequiredException` bude další požadavek přejít do služby Broker. `MsalUiRequiredException`je vyvolána z několika důvodů a je třeba je přeložit interaktivně. Jedná se o některé běžné důvody:

- Uživatel změnil heslo přidružené k účtu.
- Uživatelský účet už nesplňuje zásady podmíněného přístupu.
- Uživatel odvolal svůj souhlas s tím, že aplikace bude přidružená ke svému účtu.

### <a name="when-a-broker-is-uninstalled"></a>Při odinstalaci zprostředkovatele

Pokud je nainstalovaná jenom jedna hostující aplikace zprostředkovatele a odebere se, bude se muset uživatel znovu přihlásit. Odinstalace aktivního zprostředkovatele odebere účet a přidružené tokeny ze zařízení.

Pokud je nainstalovaná služba Portál společnosti Intune a pracuje jako aktivní zprostředkovatel a Microsoft Authenticator je taky nainstalovaná, pak se při odinstalaci Portál společnosti Intune (aktivní zprostředkovatel) uživatel bude muset znovu přihlásit. Po opětovném přihlášení se aplikace Microsoft Authenticator stala aktivním zprostředkovatelem.

## <a name="integrating-with-a-broker"></a>Integrace s zprostředkovatelem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generování identifikátoru URI přesměrování pro zprostředkovatele

Je nutné zaregistrovat identifikátor URI pro přesměrování, který je kompatibilní se zprostředkovatelem. Identifikátor URI přesměrování pro zprostředkovatele musí zahrnovat název balíčku vaší aplikace, stejně jako reprezentace signatury vaší aplikace v kódování Base64.

Formát identifikátoru URI pro přesměrování je:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Vygenerujte podpis kódovaný v adrese URL Base64 pomocí podpisových klíčů vaší aplikace. Tady je několik příkladů příkazů, které používají vaše podpisové klíče pro ladění:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Informace o podepsání aplikace najdete v tématu o [podepsání aplikace](https://developer.android.com/studio/publish/app-signing) .

> [!IMPORTANT]
> Pro produkční verzi vaší aplikace použijte svůj produkční podpisový klíč.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurace MSAL pro použití zprostředkovatele

Pokud chcete ve své aplikaci použít zprostředkovatele, musíte ověřit, že jste nakonfigurovali přesměrování zprostředkovatele. Můžete například zahrnout jak identifikátor URI přesměrování s povoleným zprostředkovatelem, tak i to, že jste ho zaregistrovali – zahrnutím následujících do konfiguračního souboru MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Nové uživatelské rozhraní pro registraci aplikace Azure Portal vám pomůže vygenerovat identifikátor URI přesměrování zprostředkovatele. Pokud jste vaši aplikaci zaregistrovali pomocí staršího prostředí nebo jste používali portál pro registraci aplikací Microsoftu, možná budete muset vygenerovat identifikátor URI pro přesměrování a aktualizovat seznam identifikátorů URI pro přesměrování na portálu ručně.

### <a name="broker-related-exceptions"></a>Výjimky související se zprostředkovatelem

MSAL komunikuje se zprostředkovatelem dvěma způsoby:

- Služba vázaná na zprostředkovatele
- Android ke správci účtů

MSAL nejprve používá službu přivázané na zprostředkovatele, protože volání této služby nevyžaduje žádná oprávnění Androidu. Pokud se vazba na vázanou službu nezdaří, MSAL použije rozhraní Android ke správci účtů API. MSAL to dělá jenom v případě, že vaše aplikace už má udělené `"READ_CONTACTS"` oprávnění.

Pokud `MsalClientException` se zobrazí kód chyby `"BROKER_BIND_FAILURE"` , jsou k dispozici dvě možnosti:

- Požádejte uživatele, aby zakázal optimalizaci výkonu pro aplikaci Microsoft Authenticator a Portál společnosti Intune.
- Požádat uživatele o udělení `"READ_CONTACTS"` oprávnění
