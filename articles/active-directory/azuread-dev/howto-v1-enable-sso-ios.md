---
title: Jak povolit vzájemné přijatí v iOS pomocí ADAL napříč aplikacemi | Dokumenty společnosti Microsoft
description: Jak používat funkce sady ADAL SDK k povolení jednotného přihlášení ve všech aplikacích.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154776"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Postup: Povolení přijmutí služby SSO mezi aplikacemi v iOS pomocí ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Jednotné přihlašování (SSO) umožňuje uživatelům zadat jejich přihlašovací údaje pouze jednou a mají tyto přihlašovací údaje automaticky pracovat napříč aplikacemi a napříč platformami, které mohou používat jiné aplikace (například účty Microsoft nebo pracovní účet od Microsoft 365) ne záležitost vydavatele.

Platforma identit společnosti Microsoft spolu s sadami SDK usnadňuje povolení vlastního přihlašování v rámci vlastní sady aplikací nebo s možností zprostředkovatele a aplikacemi Authenticator v celém zařízení.

V tomto návodu se dozvíte, jak nakonfigurovat sdk v rámci vaší aplikace poskytovat přihlašující služby pro zákazníky.

Tento návod se vztahuje na:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Podmíněný přístup služby Azure Active Directory

## <a name="prerequisites"></a>Požadavky

Tento návod předpokládá, že víte, jak:

* Zřídit aplikaci pomocí staršího portálu pro Azure AD. Další informace najdete [v tématu Registrace aplikace](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integrujte svou aplikaci s [sadou Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Koncepce jednotného přihlášení

### <a name="identity-brokers"></a>Zprostředkovatelé identit

Společnost Microsoft poskytuje aplikace pro každou mobilní platformu, které umožňují přemostění pověření mezi aplikacemi od různých dodavatelů a pro rozšířené funkce, které vyžadují jediné zabezpečené místo, odkud chcete ověřit pověření. Tyto se nazývají **makléři**.

V systémech iOS a Android jsou zprostředkovatelé poskytováni prostřednictvím aplikací ke stažení, které zákazníci nainstalují nezávisle nebo zasouvají do zařízení společností, která spravuje některá nebo všechna zařízení pro své zaměstnance. Makléři podporují správu zabezpečení jen pro některé aplikace nebo celé zařízení založené na konfiguraci správce IT. V systému Windows je tato funkce poskytována výběrem účtu integrovaným do operačního systému, který je technicky známý jako Zprostředkovatel webového ověřování.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Vzory pro přihlášení na mobilních zařízeních

Přístup k přihlašovacím údajům na zařízeních se řídí dvěma základními vzory:

* Nezprostředkovatelasované přihlášení
* Broker asistované přihlášení

#### <a name="non-broker-assisted-logins"></a>Nezprostředkovatelasované přihlášení

Non-broker asistované přihlášení jsou přihlašovací prostředí, které se dějí v souladu s aplikací a používat místní úložiště na zařízení pro tuto aplikaci. Toto úložiště může být sdíleno mezi aplikacemi, ale přihlašovací údaje jsou pevně vázány na aplikaci nebo sadu aplikací, které používají toto pověření. S největší pravděpodobností jste to zažili v mnoha mobilních aplikacích, když zadáte uživatelské jméno a heslo v samotné aplikaci.

Tato přihlášení mají následující výhody:

* Uživatelské prostředí existuje zcela v rámci aplikace.
* Přihlašovací údaje lze sdílet mezi aplikacemi, které jsou podepsány stejným certifikátem, což poskytuje jediné přihlašovací prostředí pro vaši sadu aplikací.
* Kontrola o prostředí přihlášení je k dispozici do aplikace před a po přihlášení.

Tato přihlášení mají následující nevýhody:

* Uživatelé nemohou zaznamenat jednotné přihlašování ve všech aplikacích, které používají identitu Microsoftu, pouze napříč identitami Microsoftu, které vaše aplikace nakonfigurovala.
* Vaši aplikaci nelze používat s pokročilejšími obchodními funkcemi, jako je podmíněný přístup, ani používat sadu produktů Intune.
* Aplikace nemůže podporovat ověřování na základě certifikátu pro podnikové uživatele.

Tady je znázorněno, jak sady SDK fungují se sdíleným úložištěm vašich aplikací a povolit tak, aby takové bylo:

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

#### <a name="broker-assisted-logins"></a>Broker asistované přihlášení

Broker asistované přihlášení jsou přihlašovací prostředí, ke kterým dochází v rámci aplikace broker a pomocí úložiště a zabezpečení zprostředkovatele sdílet přihlašovací údaje napříč všemi aplikacemi na zařízení, které používají platformu identity. To znamená, že vaše aplikace spoléhají na zprostředkovatele přihlásit uživatele. V systémech iOS a Android jsou tyto zprostředkovatelé poskytovány prostřednictvím aplikací ke stažení, které zákazníci nainstalují nezávisle nebo zasílá do zařízení společností, která spravuje zařízení pro svého uživatele. Příkladem tohoto typu aplikace je aplikace Microsoft Authenticator v systému iOS. V systému Windows je tato funkce poskytována výběrem účtu integrovaným do operačního systému, který je technicky známý jako Zprostředkovatel webového ověřování.

Prostředí se liší podle platformy a může být někdy rušivé pro uživatele, pokud není správně spravováno. Pravděpodobně jste nejvíce obeznámeni s tímto vzorem, pokud máte nainstalovanou aplikaci Facebook a používáte Facebook Connect z jiné aplikace. Platforma identity používá stejný vzor.

Pro iOS to vede k "přechod" animace, kde je vaše aplikace odeslána na pozadí, zatímco aplikace Microsoft Authenticator přijde do popředí pro uživatele vybrat, který účet se chcete přihlásit.

Pro Android a Windows se v horní části aplikace zobrazí výběr účtu, což je pro uživatele méně rušivé.

#### <a name="how-the-broker-gets-invoked"></a>Jak se zprostředkovatel dostane vyvolána

Pokud je v zařízení nainstalován kompatibilní zprostředkovatel, jako je aplikace Microsoft Authenticator, sady SDK automaticky odvedou práci s vyvoláním zprostředkovatele za vás, když uživatel označí, že se chce přihlásit pomocí libovolného účtu z platformy identity. Tento účet může být osobní účet Microsoft, pracovní nebo školní účet nebo účet, který poskytujete a hostujete v Azure pomocí našich produktů B2C a B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak zajistíme platnost aplikace

Potřeba zajistit identitu aplikace, která volá makléře, je zásadní pro zabezpečení, které poskytujeme v makléřských asistovaných přihlášeních. Ani iOS ani Android vynucuje jedinečné identifikátory, které jsou platné pouze pro danou aplikaci, takže škodlivé aplikace mohou "falšovat" identifikátor legitimní aplikace a přijímat tokeny určené pro legitimní aplikaci. Abychom zajistili, že vždy komunikujeme se správnou aplikací za běhu, požádáme vývojáře, aby při registraci své aplikace u společnosti Microsoft poskytl vlastní redirectURI. Jak by vývojáři měli vytvořit tento identifikátor URI přesměrování, je podrobně popsán níže. Tento vlastní redirectURI obsahuje ID balíčku aplikace a je zajištěno, že je jedinečný pro aplikaci Apple App Store. Když aplikace zavolá makléře, makléř požádá operační systém iOS, aby mu poskytl ID balíčku, které volalo makléře. Zprostředkovatel poskytuje toto ID balíčku společnosti Microsoft při volání do našeho systému identit. Pokud ID balíčku aplikace neodpovídá ID balíčku, které nám poskytl vývojář během registrace, odepřeme přístup k tokenům pro prostředek, který aplikace požaduje. Tato kontrola zajišťuje, že pouze aplikace registrovaná vývojářobdrží tokeny.

**Vývojář má na výběr, zda sada SDK volá zprostředkovatele nebo používá asistovaný tok bez brokera.** Pokud se však vývojář rozhodne nepoužívat tok s pomocí zprostředkovatele, ztratí výhodu použití pověření přihlašujícího zabezpečení, které uživatel již přidal v zařízení, a zabrání tomu, aby jejich aplikace byla používána s obchodními funkcemi, které společnost Microsoft poskytuje zákazníkům, jako je podmíněný přístup, možnosti správy Intune a ověřování založené na certifikátech.

Tato přihlášení mají následující výhody:

* Uživatelské prostředí s vlastním dodavatelem ve všech svých aplikacích bez ohledu na dodavatele.
* Vaše aplikace může používat pokročilejší obchodní funkce, jako je podmíněný přístup, nebo používat sadu produktů Intune.
* Vaše aplikace může podporovat ověřování na základě certifikátu pro podnikové uživatele.
* Mnohem bezpečnější prostředí pro přihlášení jako identita aplikace a uživatele jsou ověřeny aplikací broker s další algoritmy zabezpečení a šifrování.

Tato přihlášení mají následující nevýhody:

* V iOS je uživatel převeden z prostředí vaší aplikace, zatímco jsou vybrána pověření.
* Ztráta možnosti spravovat přihlašovací prostředí pro vaše zákazníky v rámci vaší aplikace.

Zde je znázornění, jak sady SDK pracovat s makléřské aplikace povolit přihlašování k zapisování:

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

## <a name="enabling-cross-app-sso-using-adal"></a>Povolení vzájemného přiřazovacího služby mezi aplikacemi pomocí ADAL

Zde používáme ADAL iOS SDK:

* Zapnutí nebrokerského službového přitahovaného služby SSO pro vaši sadu aplikací
* Zapnutí podpory pro zprostředkovatelské služby SSO s asistencí

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Zapnutí spřiby pro nemakléřské asistované služby s připomažení

Pro nezprostředkovatelasované služby s asistencí napříč aplikacemi, sady SDK spravovat velkou část složitosti spřibyste za vás. To zahrnuje nalezení správného uživatele v mezipaměti a udržování seznamu přihlášených uživatelů, na které se můžete dotazovat.

Chcete-li povolit přihlašování k vlastnímu přihlašování napříč aplikacemi, musíte provést následující kroky:

1. Ujistěte se, že všechny vaše aplikace používají stejné ID klienta nebo ID aplikace.
2. Ujistěte se, že všechny vaše aplikace sdílejí stejný podpisový certifikát od společnosti Apple, abyste mohli sdílet klíčenky.
3. Vyžádejte si stejný nárok na klíčenku pro každou z vašich aplikací.
4. Informujte sady SDK o sdílené montovně klíčů, kterou chcete použít.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Použití stejného ID klienta / ID aplikace pro všechny aplikace ve vaší sadě aplikací

Aby platforma identit věděla, že je povoleno sdílet tokeny ve vašich aplikacích, každá z vašich aplikací bude muset sdílet stejné ID klienta nebo ID aplikace. Jedná se o jedinečný identifikátor, který vám byl poskytnut při registraci první aplikace na portálu.

Přesměrování identifikátorů URI umožňuje identifikovat různé aplikace do služby identit microsoftu, pokud používá stejné ID aplikace. Každá aplikace může mít více redirect URI registrované v onboarding portálu. Každá aplikace ve vaší sadě bude mít jiný identifikátor URI přesměrování. Příklad toho, jak to vypadá, je níže:

Identifikátor URI přesměrování aplikace App1:`x-msauth-mytestiosapp://com.myapp.mytestapp`

Identifikátor URI přesměrování aplikace 2:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identifikátor URI přesměrování aplikace App3:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Ty jsou vnořeny pod stejné ID klienta / ID aplikace a vyhledány na základě identifikátoru URI přesměrování, který nám vrátíte v konfiguraci sady SDK.

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

Formát těchto identifikátorů URI přesměrování je vysvětlen níže. Můžete použít libovolný identifikátor URI přesměrování, pokud nechcete podporovat makléře, v takovém případě musí vypadat podobně jako výše*

#### <a name="create-keychain-sharing-between-applications"></a>Vytvoření sdílení klíčenky mezi aplikacemi

Povolení sdílení klíčenky je nad rámec tohoto dokumentu a vztahuje se na ně společnost Apple v dokumentu [Přidání funkcí](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Důležité je, abyste se rozhodli, jak chcete, aby byl váš řetězec klíčů volán, a tuto funkci přidejte ve všech aplikacích.

Pokud máte správně nastavené nároky, měli byste vidět `entitlements.plist` soubor v adresáři projektu s názvem obsahující něco, co vypadá takto:

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

Jakmile máte oprávnění řetězce klíčů povolena v každé z vašich aplikací a jste připraveni k použití spři přihlašování, informujte identity SDK o klíčenku pomocí následujícího nastavení v `ADAuthenticationSettings` následujícím nastavení:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Při sdílení klíčenky napříč aplikacemi může každá aplikace odstranit uživatele nebo horší odstranit všechny tokeny v celé vaší aplikaci. To je obzvláště katastrofální, pokud máte aplikace, které spoléhají na tokeny k práci na pozadí. Sdílení řetězce klíčů znamená, že musíte být velmi opatrní při všech a všechny operace odebrání prostřednictvím sad SDK identity.

A to je vše! Sada SDK bude nyní sdílet přihlašovací údaje ve všech vašich aplikacích. Seznam uživatelů bude také sdílen mezi instancemi aplikace.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí spřiby pro zprostředkovatele asistované spřibyt

Možnost aplikace používat libovolného zprostředkovatele, který je nainstalován v zařízení, je **ve výchozím nastavení vypnuta**. Chcete-li použít aplikaci s brokerem, musíte provést nějakou další konfiguraci a přidat nějaký kód do aplikace.

Postupujte takto:

1. Povolte režim brokeru v volání kódu aplikace k ms sdk.
2. Vytvořte nový identifikátor URI přesměrování a zadejte to jak pro registraci aplikace, tak pro registraci aplikace.
3. Registrace schématu adres URL.
4. Přidejte oprávnění do souboru info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Povolit režim broker ve vaší aplikaci

Možnost aplikace používat zprostředkovatele je zapnuta při vytváření "kontextu" nebo počáteční nastavení objektu ověřování. To můžete provést nastavením typu pověření v kódu:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Nastavení `AD_CREDENTIALS_AUTO` umožní sdk pokusit se zavolat na `AD_CREDENTIALS_EMBEDDED` makléře, zabrání SDK volání zprostředkovatele.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Registrace schématu adres URL

Platforma identit používá adresy URL k vyvolání zprostředkovatele a potom vrátit řízení zpět do vaší aplikace. Chcete-li dokončit tuto zpáteční cestu, potřebujete schéma URL registrované pro vaši aplikaci, o které bude platforma identity vědět. Může se to podařit k dalším schématům aplikací, která jste si dříve zaregistrovali u vaší aplikace.

> [!WARNING]
> Doporučujeme, aby schéma adres URL bylo poměrně jedinečné, aby se minimalizovala pravděpodobnost, že jiná aplikace používá stejné schéma adres URL. Společnost Apple nevynucuje jedinečnost schémat adres URL, která jsou registrována v obchodě s aplikacemi.

Níže je uveden příklad toho, jak se to zobrazí v konfiguraci projektu. Můžete to také udělat v XCode také:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Vytvoření nového identifikátoru URI přesměrování pomocí schématu adres URL

Abychom zajistili, že tokeny přihlašovacích údajů vždy vrátíme do správné aplikace, musíme se ujistit, že voláme zpět do vaší aplikace způsobem, který může ověřit operační systém iOS. Operační systém iOS hlásí aplikacím microsoft broker u sady ID aplikace, která ji volá. To nemůže být zfalšováno neautorizovaný maješ. Proto jsme využít spolu s URI naší aplikace zprostředkovatele zajistit, že tokeny jsou vráceny do správné aplikace. Požadujeme, abyste vytvořili tento jedinečný identifikátor URI přesměrování ve vaší aplikaci a nastavili jako identifikátor URI přesměrování na našem portálu pro vývojáře.

Identifikátor URI přesměrování musí být ve správné podobě:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Tento identifikátor URI přesměrování je třeba zadat v registraci aplikace pomocí [portálu Azure](https://portal.azure.com/). Další informace o registraci aplikací Azure AD najdete [v tématu Integrace s Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Přidání identifikátoru URI přesměrování do aplikace a portálu pro dev pro podporu ověřování na základě certifikátů

Pro podporu ověřování na základě certifikátu musí být ve vaší aplikaci zaregistrovándruhý "msauth" a [na portálu Azure,](https://portal.azure.com/) který bude zpracovávat ověřování certifikátů, pokud chcete přidat tuto podporu ve vaší aplikaci.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Krok 4: Přidání parametru konfigurace do aplikace

ADAL používá -canOpenURL: ke kontrole, zda je v zařízení nainstalován zprostředkovatel. V iOS 9 na Apple uzamkl, jaké schémata aplikace může dotaz pro. Budete muset přidat "msauth" do oddílu LSApplicationQueriesSchemes vašeho `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Nakonfigurovali jste spřijit sazí!

Nyní identity SDK automaticky obě sdílet přihlašovací údaje ve vašich aplikacích a vyvolat zprostředkovatele, pokud je k dispozici na jejich zařízení.

## <a name="next-steps"></a>Další kroky

* Další informace o [protokolu SAML jednotného přihlášení](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
