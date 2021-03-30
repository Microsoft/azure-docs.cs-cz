---
title: Jak povolit jednotné přihlašování mezi aplikacemi v iOS pomocí knihovny ADAL | Microsoft Docs
description: Použití funkcí sady ADAL SDK pro povolení jednotného přihlašování napříč aplikacemi.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383695"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Postupy: povolení jednotného přihlašování mezi aplikacemi v iOS pomocí ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednotné přihlašování (SSO) umožňuje uživatelům zadat přihlašovací údaje jenom jednou a nechat tyto přihlašovací údaje automaticky fungovat napříč aplikacemi a na různých platformách, které můžou používat jiné aplikace (třeba účty Microsoft nebo pracovní účet z Microsoft 365) bez ohledu na vydavatele.

Platforma pro identitu od Microsoftu, společně se sadami SDK, usnadňuje povolení jednotného přihlašování v rámci vlastní sady aplikací nebo funkcí zprostředkovatele a ověřovacích aplikací v celém zařízení.

V tomto postupu se naučíte, jak v rámci aplikace nakonfigurovat sadu SDK, abyste svým zákazníkům mohli poskytovat jednotné přihlašování.

Tento postup se týká:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory podmíněný přístup

## <a name="prerequisites"></a>Požadavky

Tento postup předpokládá, že víte, jak:

* Zřiďte aplikaci pomocí starší verze portálu pro Azure AD. Další informace najdete v tématu [Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) .
* Integrujte svou aplikaci se sadou [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Koncepce jednotného přihlašování

### <a name="identity-brokers"></a>Zprostředkovatelé identity

Společnost Microsoft poskytuje aplikace pro každou mobilní platformu, která umožňuje přemostění přihlašovacích údajů napříč aplikacemi od různých dodavatelů a pro rozšířené funkce, které vyžadují jediné zabezpečené místo, ze kterého se ověřují přihlašovací údaje. Nazývají se **Zprostředkovatelé**.

V systémech iOS a Android jsou zprostředkovatelé poskytovány prostřednictvím aplikací ke stažení, které zákazníci nainstalují nezávisle nebo na zařízení prostřednictvím společnosti, která spravuje některé nebo všechna zařízení pro své zaměstnance. Zprostředkovatelé podporují správu zabezpečení pouze pro některé aplikace nebo celé zařízení v závislosti na konfiguraci správce IT. V systému Windows je tato funkce poskytována výběrem možnosti účtu, který je součástí operačního systému, který je technicky známý jako zprostředkovatel webového ověřování.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Vzory přihlášení na mobilních zařízeních

Přístup k přihlašovacím údajům na zařízeních se řídí dvěma základními vzory:

* Přihlášení s asistencí bez zprostředkovatele
* Zprostředkovatelé přihlašování s asistencí

#### <a name="non-broker-assisted-logins"></a>Přihlášení s asistencí bez zprostředkovatele

Nezprostředkovatelské přihlašování s asistencí jsou přihlašovací prostředí, která se připravují do aplikace a používají místní úložiště pro danou aplikaci. Toto úložiště může být sdíleno mezi aplikacemi, ale přihlašovací údaje jsou pevně vázány na aplikaci nebo sadu aplikací, které používají tyto přihlašovací údaje. Pravděpodobně jste to udělali v mnoha mobilních aplikacích, když zadáte uživatelské jméno a heslo v rámci samotné aplikace.

Tato přihlášení mají následující výhody:

* Činnost koncového uživatele se nachází zcela v rámci aplikace.
* Přihlašovací údaje se dají sdílet mezi aplikacemi, které jsou podepsané stejným certifikátem, a poskytují pro sadu aplikací jednotné přihlašování.
* Kontrola nad prostředím přihlášení se poskytuje aplikaci před a po přihlášení.

Tato přihlášení mají následující nevýhody:

* Uživatelé nemůžou používat jednotné přihlašování ve všech aplikacích, které používají identitu Microsoftu, jenom v rámci identit Microsoftu, které vaše aplikace nakonfigurovala.
* Vaše aplikace se nedá používat s pokročilejšími obchodními funkcemi, jako je podmíněný přístup, nebo s využitím sady Intune pro produkty.
* Vaše aplikace nemůže podporovat ověřování na základě certifikátů pro obchodní uživatele.

Tady je reprezentace způsobu, jakým sady SDK pracují se sdíleným úložištěm vašich aplikací pro povolení jednotného přihlašování:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Zprostředkovatelé přihlašování s asistencí

Zprostředkovatelé přihlašování s asistencí jsou přihlašovací prostředí, ke kterým dochází v rámci aplikace zprostředkovatele, a používají úložiště a zabezpečení služby Broker ke sdílení přihlašovacích údajů napříč všemi aplikacemi v zařízení, které používají platformu identity. To znamená, že vaše aplikace závisí na zprostředkovateli k podepisování uživatelů v. V systémech iOS a Android jsou tito zprostředkovatelé k dispozici prostřednictvím aplikací ke stažení, které si zákazníci můžou nainstalovat nezávisle nebo na zařízení prostřednictvím společnosti, která spravuje zařízení pro uživatele. Příkladem tohoto typu aplikace je Microsoft Authenticator aplikace v iOS. V systému Windows je tato funkce poskytována výběrem možnosti účtu, který je součástí operačního systému, který je technicky známý jako zprostředkovatel webového ověřování.

Prostředí se liší podle platformy a v případě, že se nespravuje správně, může být v některých případech rušivé pro uživatele. Tento model je pravděpodobně nejčastěji známý, pokud máte nainstalovanou aplikaci Facebook a používáte Facebook Connect z jiné aplikace. Platforma identity používá stejný vzor.

Pro iOS to vede k animaci "přechod", kde se vaše aplikace pošle na pozadí, zatímco Microsoft Authenticator aplikace přicházejí do popředí, aby si uživatel mohl vybrat účet, se kterým se chce přihlásit.

V případě Androidu a Windows se výběr účtu zobrazuje na vaší aplikaci, což pro uživatele méně nenarušilo.

#### <a name="how-the-broker-gets-invoked"></a>Způsob vyvolání zprostředkovatele

Pokud je na zařízení nainstalovaný kompatibilní zprostředkovatel, jako je Microsoft Authenticator aplikace, sady SDK automaticky provede práci vyvoláním zprostředkovatele za vás, když uživatel označí, že se chce přihlásit pomocí libovolného účtu z platformy identity. Tímto účtem může být osobní účet Microsoft, pracovní nebo školní účet nebo účet, který poskytujete a hostujte v Azure s využitím našich produktů B2C a B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak zajišťujeme platnost aplikace

Potřeba zajistit, aby identita aplikace, která volá zprostředkovatele, byla zásadní pro zabezpečení poskytnuté v přihlašování s asistencí pro zprostředkovatele. IOS ani Android vynucuje jedinečné identifikátory, které jsou platné jenom pro danou aplikaci, takže škodlivé aplikace můžou mít falešný identifikátor aplikace a obdrží tokeny určené pro legitimní aplikaci. Abychom zajistili, že vždycky komunikujeme se správnou aplikací za běhu, požádáme vývojáře, aby při registraci aplikace do Microsoftu poskytl vlastní redirectURI. Jak by měli vývojáři vytvořit tento identifikátor URI přesměrování, je podrobněji popsán níže. Tento vlastní redirectURI obsahuje ID sady prostředků aplikace a zajišťuje, aby aplikace byla pro Apple App Store jedinečná. Když aplikace volá zprostředkovatele, vyžádá si operační systém iOS, aby mu poskytl ID sady, které volalo zprostředkovatele. Zprostředkovatel poskytuje toto ID sady prostředků Microsoftu ve volání našeho systému identity. Pokud ID sady prostředků aplikace neodpovídá ID sady, které nám poskytla vývojář během registrace, odepřeme přístup k tokenům pro prostředek, který aplikace požaduje. Tato kontrolu zajistí, že tokeny obdrží jenom aplikace zaregistrovaná vývojářem.

**Vývojář má možnost zvolit, zda sada SDK volá zprostředkovatele nebo používá tok, který není s asistencí zprostředkovatelem.** Pokud se ale vývojář rozhodne nepoužívat tok s asistencí pro zprostředkovatele, ztratí výhody použití přihlašovacích údajů jednotného přihlašování, které uživatel už mohl přidat do zařízení, a zabrání jeho použití u podnikových funkcí, jako je podmíněný přístup, funkce správy Intune a ověřování na základě certifikátů.

Tato přihlášení mají následující výhody:

* Jednotné přihlašování uživatelů napříč všemi svými aplikacemi bez ohledu na dodavatele.
* Vaše aplikace může používat pokročilejší obchodní funkce, jako je podmíněný přístup, nebo používat sadu produktů Intune.
* Vaše aplikace může podporovat ověřování na základě certifikátů pro obchodní uživatele.
* Mnohem bezpečnější přihlašovací prostředí jako identita aplikace a uživatel ověřuje aplikace zprostředkovatele s dalšími algoritmy zabezpečení a šifrováním.

Tato přihlášení mají následující nevýhody:

* V systému iOS je uživatel při výběru přihlašovacích údajů přepnut z prostředí vaší aplikace.
* Ztráta schopnosti spravovat přihlašovací údaje pro zákazníky v rámci vaší aplikace.

Tady je reprezentace způsobu, jakým sady SDK pracují s aplikacemi zprostředkovatele pro povolení jednotného přihlašování:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>Povolení jednotného přihlašování mezi aplikacemi pomocí ADAL

Pomocí sady ADAL iOS SDK pro:

* Zapněte pro vaši sadu aplikací pomocná přihlášení SSO bez zprostředkovatele.
* Zapnutí podpory jednotného přihlašování (SSO) s asistencí pro zprostředkovatele

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro jednotné přihlašování bez zprostředkovatele

V případě jednotného přihlašování, které není v rámci aplikací přivedené k zprostředkovateli, budou sady SDK spravovat většinu složitosti jednotného přihlašování za vás. To zahrnuje hledání správného uživatele v mezipaměti a udržování seznamu přihlášených uživatelů, na které se můžete dotazovat.

Pokud chcete povolit jednotné přihlašování napříč aplikacemi, které vlastníte, musíte udělat toto:

1. Zajistěte, aby všechny aplikace používaly stejné ID klienta nebo ID aplikace.
2. Zajistěte, aby všechny vaše aplikace sdílely stejný podpisový certifikát od společnosti Apple, takže můžete sdílet řetězce klíčů.
3. Pro každou aplikaci si vyžádejte stejnou nárok na řetězec klíčů.
4. Řekněte sadám SDK o sdíleném řetězci klíčů, které chceme použít.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Použití stejného ID klienta nebo ID aplikace pro všechny aplikace v sadě aplikací

Aby platforma identity věděla, že je povoleno sdílet tokeny napříč vašimi aplikacemi, každá z vašich aplikací bude muset sdílet stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který vám byl poskytnut při registraci první aplikace na portálu.

Identifikátory URI pro přesměrování umožňují identifikovat různé aplikace služby Microsoft identity, pokud používá stejné ID aplikace. Každá aplikace může obsahovat více identifikátorů URI pro přesměrování v portálu pro registraci. Každá aplikace v sadě bude mít jiný identifikátor URI pro přesměrování. Příklad toho, jak se toto vypadá níže:

Identifikátor URI pro přesměrování app1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identifikátor URI pro přesměrování app2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identifikátor URI pro přesměrování APP3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Jsou vnořené pod stejným ID klienta/ID aplikace a vyhledaly se na základě identifikátoru URI přesměrování, který v konfiguraci sady SDK vrátíte do nás.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Formát těchto identifikátorů URI pro přesměrování je vysvětlen níže. Můžete použít libovolný identifikátor URI pro přesměrování, pokud nechcete podporovat zprostředkovatele. v takovém případě musí vypadat přibližně takto *

#### <a name="create-keychain-sharing-between-applications"></a>Vytvoření sdílení řetězce klíčů mezi aplikacemi

Povolení sdílení řetězce klíčů je nad rámec tohoto dokumentu a je pokryté společností Apple v dokumentu [Přidání možností](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Důležité je, abyste se rozhodli, co chcete volat do řetězce klíčů, a přidat tuto schopnost napříč všemi aplikacemi.

Pokud máte oprávnění nastavené správně, měli byste zobrazit soubor v adresáři projektu `entitlements.plist` s názvem, který obsahuje něco, co vypadá takto:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Jakmile v každé z vašich aplikací povolíte nárok na zadání řetězce klíčů a jste připraveni použít jednotné přihlašování, řekněte sadě SDK identity o vašem řetězci klíčů pomocí následujícího nastavení v následujících nastaveních `ADAuthenticationSettings` :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Když ve svých aplikacích sdílíte řetězce klíčů, můžou aplikace odstraňovat uživatele nebo horším odstranit všechny tokeny v rámci vaší aplikace. To je obzvláště katastrofální důsledky, pokud máte aplikace, které na tokenech spoléhají na práci na pozadí. Sdílení řetězce klíčů znamená, že musíte být velmi opatrní v jakékoli a všech operacích odebrání prostřednictvím sad identity identity.

A to je vše! Sada SDK teď bude sdílet přihlašovací údaje napříč všemi vašimi aplikacemi. Seznam uživatelů bude také sdílen napříč instancemi aplikace.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro zprostředkovatele s asistencí pro jednotné přihlašování

Možnost použití libovolného zprostředkovatele nainstalovaného v zařízení je **ve výchozím nastavení vypnutá**. Aby bylo možné aplikaci použít s zprostředkovatelem, je nutné provést nějakou další konfiguraci a přidat do aplikace nějaký kód.

Postup je následující:

1. Povolí režim zprostředkovatele ve volání sady MS SDK v kódu aplikace.
2. Vytvořte nový identifikátor URI pro přesměrování a poskytněte aplikaci i registraci vaší aplikace.
3. Registrace schématu adresy URL.
4. Přidejte oprávnění do souboru info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: povolení režimu zprostředkovatele ve vaší aplikaci

Schopnost vaší aplikace používat zprostředkovatele je zapnutá, když vytvoříte kontext nebo počáteční nastavení objektu ověřování. To provedete tak, že ve svém kódu nastavíte typ přihlašovacích údajů:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO`Nastavení umožní sadě SDK, aby se pokusila zavolat službě Broker, `AD_CREDENTIALS_EMBEDDED` zabrání sadě SDK v volání do zprostředkovatele.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: registrace schématu adresy URL

Platforma identity používá adresy URL k vyvolání zprostředkovatele a pak vrátí řízení zpět do vaší aplikace. K dokončení této zpáteční cesty potřebujete schéma URL zaregistrované pro vaši aplikaci, o které bude platforma identity znát. To může být kromě všech dalších schémat aplikací, které jste předtím zaregistrovali v aplikaci.

> [!WARNING]
> Doporučujeme, aby schéma URL bylo poměrně jedinečné, aby se minimalizovala pravděpodobnost jiné aplikace pomocí stejného schématu URL. Apple neuplatňuje jedinečnost schémat adres URL, která jsou registrovaná v App Storu.

Níže je uveden příklad toho, jak se to zobrazí v konfiguraci projektu. Můžete to také provést v XCode i:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: vytvoření nového identifikátoru URI pro přesměrování pomocí schématu URL

Aby bylo zajištěno, že vždycky vrátíme tokeny přihlašovacích údajů ke správné aplikaci, musíme se ujistit, že budeme volat zpět do vaší aplikace způsobem, který může operační systém iOS ověřit. Operační systém iOS oznamuje aplikacím Microsoft Broker ID sady prostředků aplikace, která ji volá. Toto nelze podléhat podvodné aplikaci. Proto to využijeme společně s identifikátorem URI naší aplikace zprostředkovatele, aby se zajistilo, že se tokeny vrátí do správné aplikace. Pro náš portál pro vývojáře vyžadujeme, abyste nastavili tento jedinečný identifikátor URI pro přesměrování jak v aplikaci, tak jako identifikátor URI přesměrování.

Identifikátor URI pro přesměrování musí být ve správném formátu:

`<app-scheme>://<your.bundle.id>`

např.: *x-msauth-mytestiosapp://com.myapp.MyTestApp*

Tento identifikátor URI pro přesměrování musí být zadaný v registraci vaší aplikace pomocí [Azure Portal](https://portal.azure.com/). Další informace o registraci aplikace služby Azure AD najdete v tématu [integrace s Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Přidání identifikátoru URI přesměrování do aplikace a vývojářského portálu pro podporu ověřování na základě certifikátů

Aby bylo možné podporovat ověřování na základě certifikátu, musí být v aplikaci zaregistrována druhá "msauth" a [Azure Portal](https://portal.azure.com/) pro zpracování ověřování certifikátů, pokud chcete přidat tuto podporu do aplikace.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

např.: *msauth://Code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.MyTestApp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Krok 4: přidejte do své aplikace parametr konfigurace.

ADAL používá – canOpenURL: ke kontrole, jestli je zprostředkovatel nainstalovaný na zařízení. V systému iOS 9 on se společnost Apple zablokuje v tom, v jakých schématech může aplikace dotazovat. V části LSApplicationQueriesSchemes budete muset přidat "msauth" `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Nakonfigurovali jste jednotné přihlašování.

Sada identity SDK teď automaticky sdílí přihlašovací údaje napříč vašimi aplikacemi a vyvolá zprostředkovatele, pokud se nachází na svém zařízení.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolu SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
