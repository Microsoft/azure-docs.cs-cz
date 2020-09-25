---
title: Zprostředkované ověřování v Androidu | Azure
titleSuffix: Microsoft identity platform
description: Přehled zprostředkovaných ověření & autorizaci pro Android na platformě Microsoft Identity Platform
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 2bb48971e86c2b61742735020469865fa969bee3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258402"
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

V Androidu je zprostředkovatel ověřování Microsoft součástí, která je součástí [Microsoft Authenticator aplikace](https://play.google.com/store/apps/details?id=com.azure.authenticator) a [portál společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

Následující diagram znázorňuje vztah mezi vaší aplikací, Microsoft Authentication Library (MSAL) a zprostředkovateli ověřování od Microsoftu.

![Diagram znázorňující, jak se aplikace vztahuje k MSAL, zprostředkovatelům, aplikacím a správci účtů Android.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalace aplikací, které hostují zprostředkovatele

Služby pro hostování aplikací můžou instalovat vlastník zařízení z jejich App Storu (obvykle Obchod Google Play). Některá rozhraní API (prostředky) jsou ale chráněná zásadami podmíněného přístupu, které vyžadují, aby byla zařízení:

- Registrováno (připojeno k pracovišti) a/nebo
- Zaregistrováno ve správě zařízení nebo
- Zaregistrováno v Intune App Protection

Pokud na zařízení ještě není nainstalovaná aplikace zprostředkovatele, MSAL uživateli pokyn, aby ho nainstaloval hned, jakmile se aplikace pokusí o interaktivní získání tokenu. V takovém případě bude muset uživatel provést kroky, aby zařízení dodržovalo požadované zásady.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Účinky instalace a odinstalace zprostředkovatele

### <a name="when-a-broker-is-installed"></a>Při instalaci zprostředkovatele

Když je v zařízení nainstalován zprostředkovatel, budou všechny následné požadavky na interaktivní tokeny (volání `acquireToken()` ) zpracovávány zprostředkovatelem namísto místně pomocí MSAL. Pro zprostředkovatele není k dispozici jakýkoli stav jednotného přihlašování, který je dřív dostupný pro MSAL. V důsledku toho se uživatel bude muset znovu ověřit nebo vybrat účet ze stávajícího seznamu účtů, které zařízení zná.

Instalace zprostředkovatele nevyžaduje, aby se uživatel znovu přihlásil. Pouze v případě, že uživatel potřebuje vyřešit, `MsalUiRequiredException` bude další požadavek přejít do služby Broker. `MsalUiRequiredException` může být vyvolána z několika důvodů a je třeba je přeložit interaktivně. Příklad:

- Uživatel změnil heslo přidružené k účtu.
- Uživatelský účet už nesplňuje zásady podmíněného přístupu.
- Uživatel odvolal svůj souhlas s tím, že aplikace bude přidružená ke svému účtu.

#### <a name="multiple-brokers"></a>Více zprostředkovatelů

Pokud je v zařízení nainstalováno více zprostředkovatelů, je zprostředkovatel, který byl nainstalován jako první, vždy aktivním zprostředkovatelem. V zařízení může být aktivní jenom jeden zprostředkovatel.

### <a name="when-a-broker-is-uninstalled"></a>Při odinstalaci zprostředkovatele

Pokud je nainstalovaná jenom jedna hostující aplikace zprostředkovatele a odebere se, bude se muset uživatel znovu přihlásit. Odinstalace aktivního zprostředkovatele odebere účet a přidružené tokeny ze zařízení.

Pokud je nainstalovaná služba Portál společnosti Intune a pracuje jako aktivní zprostředkovatel a Microsoft Authenticator je taky nainstalovaná, pak se při odinstalaci Portál společnosti Intune (aktivní zprostředkovatel) uživatel bude muset znovu přihlásit. Po opětovném přihlášení se aplikace Microsoft Authenticator stala aktivním zprostředkovatelem.

## <a name="integrating-with-a-broker"></a>Integrace s zprostředkovatelem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generování identifikátoru URI přesměrování pro zprostředkovatele

Je nutné zaregistrovat identifikátor URI pro přesměrování, který je kompatibilní se zprostředkovatelem. Identifikátor URI přesměrování pro zprostředkovatele by měl zahrnovat název balíčku vaší aplikace a reprezentace signatury vaší aplikace v kódování Base64.

Formát identifikátoru URI pro přesměrování je: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Pomocí [nástroje](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) Key můžete vygenerovat hodnotu hash signatury s kódováním base64 pomocí podpisových klíčů vaší aplikace a pak pomocí Azure Portal vygenerovat identifikátor URI pro přesměrování pomocí této hodnoty hash.

Linux a macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Jakmile vygenerujete hodnotu hash podpisu pomocí *nástroje*, použijte Azure Portal k VYgenerování identifikátoru URI přesměrování:

1. Přihlaste se k [Azure Portal](https://protal.azure.com) a v **Registrace aplikací**vyberte svou aplikaci pro Android.
1. Vyberte **ověřování**  >  **Přidat platformu**  >  **Android**.
1. V podokně **Konfigurovat aplikaci pro Android** , které se otevře, zadejte **hodnotu hash podpisu** , kterou jste dříve vytvořili, a **název balíčku**.
1. Klikněte na tlačítko **Konfigurovat** .

Azure Portal pro vás vygeneruje identifikátor URI pro přesměrování a zobrazí ho v poli **URI pro přesměrování** konfiguračního podokna v **Androidu** .

Další informace o podepsání aplikace najdete v tématu [podepsání aplikace](https://developer.android.com/studio/publish/app-signing) v Android Studio uživatelské příručce.

> [!IMPORTANT]
> Pro produkční verzi vaší aplikace použijte svůj produkční podpisový klíč.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurace MSAL pro použití zprostředkovatele

Pokud chcete ve své aplikaci použít zprostředkovatele, musíte ověřit, že jste nakonfigurovali přesměrování zprostředkovatele. Můžete například zahrnout jak identifikátor URI přesměrování s povoleným zprostředkovatelem, tak i to, že jste ho zaregistrovali – zahrnutím následujících nastavení do konfiguračního souboru MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Výjimky související se zprostředkovatelem

MSAL komunikuje se zprostředkovatelem dvěma způsoby:

- Služba vázaná na zprostředkovatele
- Android ke správci účtů

MSAL nejdřív používá službu vázanou na zprostředkovatele, protože volání této služby nevyžaduje žádná oprávnění Androidu. Pokud se vazba na vázanou službu nezdaří, MSAL použije rozhraní Android ke správci účtů API. MSAL to dělá jenom v případě, že vaše aplikace už má udělené `"READ_CONTACTS"` oprávnění.

Pokud `MsalClientException` se zobrazí kód chyby `"BROKER_BIND_FAILURE"` , jsou k dispozici dvě možnosti:

- Požádejte uživatele, aby zakázal optimalizaci výkonu pro aplikaci Microsoft Authenticator a Portál společnosti Intune.
- Požádat uživatele o udělení `"READ_CONTACTS"` oprávnění

## <a name="verifying-broker-integration"></a>Ověřování integrace zprostředkovatele

Nemusí být okamžitě jasné, že integrace zprostředkovatele funguje, ale můžete použít následující kroky a ověřit:

1. Na zařízení s Androidem dokončete požadavek pomocí zprostředkovatele.
1. V nastavení zařízení s Androidem vyhledejte nově vytvořený účet odpovídající účtu, pomocí kterého jste ověřili. Účet by měl být typu *pracovní účet*.

Účet můžete odebrat z nastavení, pokud chcete opakovat test.

## <a name="next-steps"></a>Další kroky

[Režim sdíleného zařízení pro zařízení s Androidem](msal-android-shared-devices.md) umožňuje nakonfigurovat zařízení se systémem Android tak, aby ho bylo možné snadno sdílet s více zaměstnanci.
