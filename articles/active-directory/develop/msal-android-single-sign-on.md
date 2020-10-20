---
title: Jak v Androidu povolit jednotné přihlašování pro různé aplikace pomocí MSAL | Azure
titleSuffix: Microsoft identity platform
description: Jak používat Microsoft Authentication Library (MSAL) pro Android k povolení jednotného přihlašování napříč aplikacemi.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: 7e53e21b6d929e2f0ba9a2e23e4e8e1b2278f828
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209635"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Postupy: povolení jednotného přihlašování mezi aplikacemi na Androidu pomocí MSAL

Jednotné přihlašování (SSO) umožňuje uživatelům zadat přihlašovací údaje jenom jednou a nechat tyto přihlašovací údaje automaticky fungovat napříč aplikacemi.

[Microsoft Identity Platform](/azure/active-directory/develop/) a Microsoft Authentication Library (MSAL) vám pomůžou povolit jednotné přihlašování napříč vlastní sadou aplikací. Pomocí ověřovacích a ověřovacích aplikací pro zprostředkovatele můžete v celém zařízení rozšířené jednotné přihlašování.

V tomto postupu se naučíte, jak nakonfigurovat sady SDK používané vaší aplikací k poskytování jednotného přihlašování pro vaše zákazníky.

## <a name="prerequisites"></a>Požadavky

Tento postup předpokládá, že máte tyto informace:

- Zřiďte aplikaci pomocí Azure Portal. Další informace o tomto tématu najdete v tématu pokyny k vytvoření aplikace v kurzu pro [Android](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#create-a-project) .
- Integrujte svoji aplikaci s [knihovnou Microsoft Authentication Library pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Metody jednotného přihlašování

Existují dva způsoby, jak aplikace využívající MSAL pro Android k zajištění jednotného přihlašování:

* Prostřednictvím [aplikace zprostředkovatele](#sso-through-brokered-authentication)
* Přes [prohlížeč systému](#sso-through-system-browser)


   Doporučuje se použít aplikaci zprostředkovatele pro výhody, jako je jednotné přihlašování v rámci zařízení, Správa účtů a podmíněný přístup. Nicméně vyžaduje, aby vaši uživatelé stáhli další aplikace.

## <a name="sso-through-brokered-authentication"></a>Jednotné přihlašování prostřednictvím služby zprostředkované ověřování

Pro účast v jednotném přihlašování (SSO) pro jednotlivá zařízení a pro splnění zásad podmíněného přístupu pro organizaci doporučujeme použít jednoho z těchto zprostředkovatelů ověřování od Microsoftu. Integrace se zprostředkovatelem přináší následující výhody:

- Jednotné přihlašování zařízení
- Podmíněný přístup pro:
  - Intune App Protection
  - Registrace zařízení (Workplace Join)
  - Správa mobilních zařízení
- Správa účtů na úrovni zařízení
  -  přes Android ke správci účtů & nastavení účtu
  - Pracovní účet – vlastní typ účtu

V Androidu je zprostředkovatel ověřování Microsoft součástí, který je zahrnutý v aplikacích [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) a [portál společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) .

Následující diagram znázorňuje vztah mezi vaší aplikací, Microsoft Authentication Library (MSAL) a zprostředkovateli ověřování od Microsoftu.

![Diagram znázorňující, jak se aplikace vztahuje k MSAL, zprostředkovatelům, aplikacím a správci účtů Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalace aplikací, které hostují zprostředkovatele

Služby pro hostování aplikací můžou instalovat vlastník zařízení z jejich App Storu (obvykle Obchod Google Play). Některá rozhraní API (prostředky) jsou ale chráněná zásadami podmíněného přístupu, které vyžadují, aby byla zařízení:

- Registrováno (připojeno k pracovišti) a/nebo
- Zaregistrováno ve správě zařízení nebo
- Zaregistrováno v Intune App Protection

Pokud na zařízení ještě není nainstalovaná aplikace zprostředkovatele, MSAL uživateli pokyn, aby ho nainstaloval hned, jakmile se aplikace pokusí o interaktivní získání tokenu. V takovém případě bude muset uživatel provést kroky, aby zařízení dodržovalo požadované zásady.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Účinky instalace a odinstalace zprostředkovatele

#### <a name="when-a-broker-is-installed"></a>Při instalaci zprostředkovatele

Když je v zařízení nainstalován zprostředkovatel, budou všechny následné požadavky na interaktivní tokeny (volání `acquireToken()` ) zpracovávány zprostředkovatelem namísto místně pomocí MSAL. Pro zprostředkovatele není k dispozici jakýkoli stav jednotného přihlašování, který je dřív dostupný pro MSAL. V důsledku toho se uživatel bude muset znovu ověřit nebo vybrat účet ze stávajícího seznamu účtů, které zařízení zná.

Instalace zprostředkovatele nevyžaduje, aby se uživatel znovu přihlásil. Pouze v případě, že uživatel potřebuje vyřešit, `MsalUiRequiredException` bude další požadavek přejít do služby Broker. `MsalUiRequiredException` může být vyvolána z několika důvodů a je třeba je přeložit interaktivně. Například:

- Uživatel změnil heslo přidružené k účtu.
- Uživatelský účet už nesplňuje zásady podmíněného přístupu.
- Uživatel odvolal svůj souhlas s tím, že aplikace bude přidružená ke svému účtu.

**Více zprostředkovatelů** – Pokud je v zařízení nainstalováno více zprostředkovatelů, je zprostředkovatel, který byl nainstalován jako první, vždy aktivním zprostředkovatelem. V zařízení může být aktivní jenom jeden zprostředkovatel.

#### <a name="when-a-broker-is-uninstalled"></a>Při odinstalaci zprostředkovatele

Pokud je nainstalovaná jenom jedna hostující aplikace zprostředkovatele a odebere se, bude se muset uživatel znovu přihlásit. Odinstalace aktivního zprostředkovatele odebere účet a přidružené tokeny ze zařízení.

Pokud je nainstalovaná služba Portál společnosti Intune a pracuje jako aktivní zprostředkovatel a Microsoft Authenticator je taky nainstalovaná, pak se při odinstalaci Portál společnosti Intune (aktivní zprostředkovatel) uživatel bude muset znovu přihlásit. Po opětovném přihlášení se aplikace Microsoft Authenticator stala aktivním zprostředkovatelem.

### <a name="integrating-with-a-broker"></a>Integrace s zprostředkovatelem

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Vygenerovat identifikátor URI pro přesměrování pro zprostředkovatele

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

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a v **Registrace aplikací**vyberte svou aplikaci pro Android.
1. Vyberte **ověřování**  >  **Přidat platformu**  >  **Android**.
1. V podokně **Konfigurovat aplikaci pro Android** , které se otevře, zadejte **hodnotu hash podpisu** , kterou jste dříve vytvořili, a **název balíčku**.
1. Klikněte na tlačítko **Konfigurovat** .

Azure Portal pro vás vygeneruje identifikátor URI pro přesměrování a zobrazí ho v poli **URI pro přesměrování** konfiguračního podokna v **Androidu** .

Další informace o podepsání aplikace najdete v tématu [podepsání aplikace](https://developer.android.com/studio/publish/app-signing) v Android Studio uživatelské příručce.

> [!IMPORTANT]
> Pro produkční verzi vaší aplikace použijte svůj produkční podpisový klíč.

#### <a name="configure-msal-to-use-a-broker"></a>Konfigurace MSAL pro použití zprostředkovatele

Pokud chcete ve své aplikaci použít zprostředkovatele, musíte ověřit, že jste nakonfigurovali přesměrování zprostředkovatele. Můžete například zahrnout jak identifikátor URI přesměrování s povoleným zprostředkovatelem, tak i to, že jste ho zaregistrovali – zahrnutím následujících nastavení do konfiguračního souboru MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Výjimky související se zprostředkovatelem

MSAL komunikuje se zprostředkovatelem dvěma způsoby:

- Služba vázaná na zprostředkovatele
- Android ke správci účtů

MSAL nejdřív používá službu vázanou na zprostředkovatele, protože volání této služby nevyžaduje žádná oprávnění Androidu. Pokud se vazba na vázanou službu nezdaří, MSAL použije rozhraní Android ke správci účtů API. MSAL to dělá jenom v případě, že vaše aplikace už má udělené `"READ_CONTACTS"` oprávnění.

Pokud `MsalClientException` se zobrazí kód chyby `"BROKER_BIND_FAILURE"` , jsou k dispozici dvě možnosti:

- Požádejte uživatele, aby zakázal optimalizaci výkonu pro aplikaci Microsoft Authenticator a Portál společnosti Intune.
- Požádat uživatele o udělení `"READ_CONTACTS"` oprávnění

### <a name="verify-broker-integration"></a>Ověřit integraci zprostředkovatele

Nemusí být okamžitě jasné, že integrace zprostředkovatele funguje, ale můžete použít následující kroky a ověřit:

1. Na zařízení s Androidem dokončete požadavek pomocí zprostředkovatele.
1. V nastavení zařízení s Androidem vyhledejte nově vytvořený účet odpovídající účtu, pomocí kterého jste ověřili. Účet by měl být typu *pracovní účet*.

Účet můžete odebrat z nastavení, pokud chcete opakovat test.

## <a name="sso-through-system-browser"></a>Jednotné přihlašování prostřednictvím systémového prohlížeče

Aplikace pro Android mají možnost použít pro uživatelské prostředí ověřování vlastní karty WebView, System Browser nebo Chrome. Pokud aplikace nepoužívá zprostředkované ověřování, bude muset použít prohlížeč systému místo nativního webového zobrazení, aby bylo možné provést jednotné přihlašování.

### <a name="authorization-agents"></a>Autorizační agenti

Volba konkrétní strategie pro autorizační agenty je volitelná a představuje další funkční aplikace, které můžete přizpůsobit. Většina aplikací bude používat výchozí hodnoty MSAL (informace o různých výchozích nastaveních najdete v tématu [vysvětlení konfiguračního souboru Android MSAL](msal-configuration.md) ).

MSAL podporuje autorizaci pomocí nástroje `WebView` , nebo systémového prohlížeče.  Následující obrázek ukazuje, jak vypadá pomocí nástroje `WebView` , nebo v prohlížeči systému pomocí CustomTabs nebo bez CustomTabs:

![Příklady přihlášení MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Důsledky jednotného přihlašování

Ve výchozím nastavení aplikace integrované s MSAL používají k autorizaci vlastní karty systémového prohlížeče. Na rozdíl od webového zobrazení vlastní karty sdílí JAR se souborem cookie s výchozím systémovým prohlížečem, který umožňuje menší počet přihlášení pomocí webových nebo jiných nativních aplikací, které jsou integrované s vlastními kartami.

Pokud aplikace používá `WebView` strategii bez integrace Microsoft Authenticator nebo portál společnosti podporu do své aplikace, uživatelé nebudou mít k dispozici jednotné přihlašování přes zařízení nebo mezi nativními aplikacemi a webovými aplikacemi.

Pokud aplikace používá MSAL s zprostředkovatelem, jako je Microsoft Authenticator nebo Portál společnosti Intune, můžou mít uživatelé v aplikacích prostředí jednotného přihlašování, pokud mají aktivní přihlášení pomocí některé z aplikací.

### <a name="webview"></a>WebView

Pokud chcete použít webzobrazení v aplikaci, vložte do kódu JSON konfigurace aplikace následující řádek, který se předává do MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Při použití v aplikaci se `WebView` uživatel přihlásí přímo k aplikaci. Tokeny se uchovávají v izolovaném prostoru aplikace a nejsou dostupné mimo JAR souborů cookie aplikace. V důsledku toho uživatel nemůže mít v aplikacích možnosti jednotného přihlašování, pokud se aplikace neintegrují s ověřovatelem nebo Portál společnosti.

Nicméně `WebView` poskytuje možnost přizpůsobit si vzhled a chování uživatelského rozhraní pro přihlašování. Další informace o tom, jak toto přizpůsobení provést, najdete v tématu věnovaném [zobrazením v Androidu](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Výchozí prohlížeč a vlastní karty

Ve výchozím nastavení MSAL používá strategii prohlížeče a [vlastních karet](https://developer.chrome.com/multidevice/android/customtabs) . Tuto strategii můžete výslovně označit tak, aby v budoucích verzích nedocházelo ke změnám `DEFAULT` pomocí následující konfigurace JSON v souboru vlastního konfigurace:

```json
"authorization_user_agent" : "BROWSER"
```

Tento přístup slouží k zajištění jednotného přihlašování přes prohlížeč zařízení. MSAL používá sdílený soubor cookie, který umožňuje ostatním nativním aplikacím nebo webovým aplikacím dosáhnout jednotného přihlašování na zařízení pomocí souborů cookie trvalé relace nastavených pomocí MSAL.

### <a name="browser-selection-heuristic"></a>Heuristika výběru prohlížeče

Vzhledem k tomu, že MSAL není možné zadat přesný balíček prohlížeče, který se má použít pro každé široké spektrum telefonů s Androidem, MSAL implementuje heuristickou volbu pro výběr prohlížeče, která se pokusí poskytnout nejlepší jednotné přihlašování mezi zařízeními.

MSAL primárně načte výchozí prohlížeč ze Správce balíčků a zkontroluje, jestli se nachází v testovaném seznamu bezpečných prohlížečů. V takovém případě se MSAL vrátí pomocí webového zobrazení místo spuštění jiného prohlížeče, který není výchozí, ze seznamu bezpečných. Výchozí prohlížeč se vybere bez ohledu na to, jestli podporuje vlastní karty. Pokud prohlížeč podporuje vlastní karty, MSAL spustí vlastní kartu. vlastní karty mají vzhled a chování blíž k v aplikaci `WebView` a umožňují základní přizpůsobení uživatelského rozhraní. Další informace najdete [v tématu vlastní karty v Androidu](https://developer.chrome.com/multidevice/android/customtabs) .

Pokud na zařízení nejsou žádné balíčky prohlížeče, používá MSAL v aplikaci `WebView` . Pokud se výchozí nastavení zařízení nezmění, měl by se pro každé přihlášení spustit stejný prohlížeč, aby se zajistilo prostředí jednotného přihlašování.

#### <a name="tested-browsers"></a>Testované prohlížeče

Následující prohlížeče byly testovány, aby bylo možné zjistit, zda správně přesměrují na `"redirect_uri"` zadané v konfiguračním souboru:

| Zařízení | Vestavěný prohlížeč | Chrome | Opera  | Microsoft Edge | Prohlížeč UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (rozhraní API 17) | dána | dána |nelze použít |nelze použít |nelze použít |nelze použít |
| Samsung S7 (rozhraní API 25) | průchod<sup>1</sup> | dána | dána | dána | proběhne |dána |
| Huawei (rozhraní API 26) |průchod<sup>2</sup> | dána | proběhne | dána | dána |dána |
| Vivo (API 26) |dána|dána|dána|dána|dána|proběhne|
| Pixel 2 (rozhraní API 26) |dána | dána | dána | dána | proběhne |dána |
| OPPO | dána | nepoužitelné<sup>3</sup>|nelze použít  |nelze použít |nelze použít | nelze použít|
| OnePlus (rozhraní API 25) |dána | dána | dána | dána | proběhne |dána |
| Nexus (rozhraní API 28) |dána | dána | dána | dána | proběhne |dána |
|MI | dána | dána | dána | dána | proběhne |dána |

<sup>1</sup> Integrovaný Prohlížeč Samsung je Samsung Internet.<br/>
<sup>2</sup> . Huawei je vestavěný prohlížeč Huawei.<br/>
<sup>3</sup> . Výchozí prohlížeč nejde změnit v nastavení zařízení OPPO.

## <a name="next-steps"></a>Další kroky

[Režim sdíleného zařízení pro zařízení s Androidem](msal-android-shared-devices.md) umožňuje nakonfigurovat zařízení se systémem Android tak, aby ho bylo možné snadno sdílet s více zaměstnanci.
