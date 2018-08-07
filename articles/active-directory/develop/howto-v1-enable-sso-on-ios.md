---
title: Postup povolení jednotného přihlašování napříč aplikacemi pro iOS pomocí knihovny ADAL | Dokumentace Microsoftu
description: 'Jak používat funkce knihovny ADAL sady SDK k povolení jednotného přihlašování napříč aplikacemi. '
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 203cb4f57cfa50a17b66a9b70a44568e57ec4835
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581294"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Postup povolení jednotného přihlašování napříč aplikacemi pro iOS pomocí knihovny ADAL

Poskytující jednotné přihlašování (SSO), aby uživatelé pouze zadat své přihlašovací údaje jednou a automaticky mají tyto přihlašovací údaje pracovat napříč aplikace nyní očekávání zákazníků. Potíže při zadávání uživatelského jména a hesla na malé obrazovce, často časy v kombinaci s další faktor (2FA) jako telefonního hovoru nebo chvíle kód, výsledky v rychlé nespokojenost, pokud uživatel má k tomu víc než jednou pro váš produkt.

Navíc pokud použijete platformu identit, která může používat další aplikace, jako je například Accounts Microsoft nebo pracovní účet z Office 365, zákazníci očekávají, že tyto přihlašovací údaje být k dispozici pro použití ve všech svých aplikacích bez ohledu na dodavatele.

Microsoft Identity platform, spolu s naše sady SDK Microsoft Identity tento těžkou práci udělá za vás a dává vám možnost potěšte své zákazníky s jednotným Přihlašováním, buď v rámci vlastní sadu aplikací nebo, stejně jako u naši schopnost zprostředkovatele a Authenticator aplikacemi, v rámci celého zařízení.

Tento názorný postup zjistíte, jak nakonfigurovat naší sady SDK v rámci vaší aplikace k vašim zákazníkům poskytovat tuto výhodu.

Tento názorný postup platí pro:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory s B2B
* Podmíněný přístup k Azure Active Directory

Předchozí dokument předpokládá, že víte, jak [zřídit aplikace na starší verzi portálu pro Azure Active Directory](active-directory-how-to-integrate.md) a integrované aplikace s [sada SDK pro iOS Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Jednotné přihlašování koncepty v Microsoft Identity Platform

### <a name="microsoft-identity-brokers"></a>Zprostředkovatelé identit Microsoft

Microsoft poskytuje pro každou mobilní platformu aplikace, které umožňují přemostění přihlašovacích údajů napříč aplikacemi od různých dodavatelů a umožňuje speciálních rozšířených funkcí, které vyžadují jedno zabezpečené místo, odkud se ověřují přihlašovací údaje. Označujeme **zprostředkovatelů**. V Iosu a Androidu tyto poskytují zprostředkovatele aplikace ke stažení, že zákazníci nainstalovat nezávisle na sobě, nebo můžete přidat do zařízení ve společnosti, který spravuje některá nebo všechna zařízení pro svoje zaměstnance. Správa zabezpečení zprostředkovatele podporují jenom pro některé aplikace nebo celé zařízení podle potřeby co správce IT. Ve Windows tato funkce poskytuje výběru účtu vestavěné do operačního systému, technicky označuje jako zprostředkovatel webového ověření.

Přečtěte si další informace o jak používáme zprostředkovatele a jak vaši zákazníci se mohou zobrazit je v jejich přihlašovacího procesu pro Microsoft Identity platform.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Vzory pro přihlášení na mobilních zařízeních

Přístup k přihlašovacím údajům na zařízení podle dva základní způsoby pro platformu Microsoft Identity:

* Přihlášení s asistencí bez zprostředkovatele
* Zprostředkovatel přihlášení s asistencí

#### <a name="non-broker-assisted-logins"></a>Přihlášení s asistencí bez zprostředkovatele

Přihlášení s asistencí non-broker jsou přihlašovací prostředí pro dojít, vložený s aplikací, které používají místní úložiště na zařízení pro tuto aplikaci. Toto úložiště může být sdílena s aplikací, ale přihlašovací údaje jsou pevně vázány na aplikaci nebo sadu aplikací s využitím tyto přihlašovací údaje. Jste pravděpodobně zaznamenal/zaznamenala jste to v mnoha mobilních aplikacích. když zadáte uživatelské jméno a heslo v rámci vlastní aplikace.

Tyto přihlašovací údaje nabízí tyto výhody:

* Činnost koncového uživatele existuje jenom v rámci aplikace.
* Přihlašovací údaje mohou být sdílena aplikace, které jsou podepsané stejným certifikátem, jednotné přihlašování prostředí pro vaši sadu aplikací.
* Poskytuje řízení kolem možnosti protokolování aplikaci před a po přihlášení.

Tyto přihlašovací údaje mají následující nevýhody:

* Uživatele nelze prostředí jednotného přihlašování napříč všemi aplikacemi, které používají Microsoft Identity pouze v rámci Identities Microsoftu, které vaše aplikace je nakonfigurovaná.
* Aplikaci nelze použít s obchodní funkce, jako je například podmíněný přístup nebo používají sadu Intune produktů.
* Aplikace nepodporuje ověřování pomocí certifikátů pro podnikové uživatele.

Je zde reprezentace práci se sadami SDK Microsoft Identity se sdíleným úložištěm aplikací k povolení jednotného přihlašování:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Zprostředkovatel přihlášení s asistencí

S pomocí zprostředkovatele přihlášení jsou přihlašovací prostředí, ke kterým dojde v aplikaci zprostředkovatele, které používají úložiště a zabezpečení zprostředkovatele sdílet přihlašovací údaje na všechny aplikace na zařízení, které se vztahují platformě Microsoft Identity. To znamená, že vaše aplikace využívají Service broker k přihlášení uživatelů. V Iosu a Androidu tyto poskytují zprostředkovatele aplikace ke stažení, že zákazníci nainstalovat nezávisle na sobě, nebo přidat do zařízení ve společnosti, který spravuje zařízení pro svoje uživatele. Příkladem takové aplikace je aplikace Microsoft Authenticator v Iosu. Ve Windows je tato funkce poskytuje výběru účtu vestavěné do operačního systému, technicky označuje jako zprostředkovatel webového ověření.
Prostředí se liší podle platformy a může být někdy rušivá pro uživatele, pokud nebude správně spravovat. Jste pravděpodobně nejvíce obeznámeni s tímto modelem stačí Pokud máte nainstalovanou aplikací služby Facebook a použijete připojení k Facebooku z jiné aplikace. Platforma Microsoft Identity používá stejný vzor.

Animace odešle aplikace na pozadí při aplikace Microsoft Authenticator pro iOS, které to vede k "přechodu" obsahuje aktivní. k výběru účtu, který se chcete přihlásit s uživatelem. 

Pro Android a Windows výběr účtu se zobrazí nad vaší aplikace, což je méně rušivá pro uživatele.

#### <a name="how-the-broker-gets-invoked"></a>Jak se zprostředkovatel volán

Pokud kompatibilní Service broker je nainstalovaný na zařízeních, jako je aplikace Microsoft Authenticator se sadami SDK Microsoft Identity automaticky provede práci při volání zprostředkovatele pro vás, když uživatel označuje, že se chcete přihlásit pomocí libovolného účtu od Microsoftu Platforma identit. Tento účet může být osobní Account Microsoft, pracovní nebo školní účet nebo účet, který zadáte a hostovat v Azure pomocí našich produktů B2C a B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak zajišťujeme aplikace je platný
 
 Je potřeba zajistit identity aplikace volání zprostředkovatele je velmi důležité pro zabezpečení, kterou poskytujeme ve Service broker s asistencí přihlášení. IOS ani Android vynucuje jedinečné identifikátory, které jsou platné pouze pro danou aplikaci, aby škodlivé aplikace může "zfalšovat" identifikátor oprávněné aplikace a přijímat tokeny určená pro oprávněné aplikace. K zajištění, že jsme vždy komunikují správné aplikace za běhu, můžeme požádat vývojáři poskytnout vlastní redirectURI, při registraci své aplikace s Microsoftem. **Jak by měly vývojářů si vytvořte tento identifikátor URI pro přesměrování je podrobněji popsána níže.** Tato vlastní identifikátor URI pro přesměrování obsahuje ID sady prostředků aplikace a je zajištěno bude jedinečný pro aplikaci App Store společnosti Apple. Když aplikace volá zprostředkovatele, zprostředkovatel se vás zeptá verzi operačního systému iOS poskytnout ID sady prostředků, který volá zprostředkovatele. Zprostředkovatel poskytuje toto ID sady prostředků společnosti Microsoft ve volání náš systém identit. Pokud ID sady prostředků aplikace se neshoduje s ID sady prostředků, které nám poskytnete vývojářem během registrace, jsme odmítne žádá o přístup k tokeny pro prostředek aplikace. Tato kontrola se zajistí, že pouze registrovaný vývojář aplikace obdrží tokeny.

**Vývojář má možnost, pokud sada SDK Microsoft Identity volá zprostředkovatele nebo používá flow s asistencí bez zprostředkovatele.** Nicméně pokud vývojář rozhodne nepoužívat flow s asistencí zprostředkovatele mohly být ztraceny výhodou použití jednotného přihlašování přihlašovací údaje, že uživatel může na zařízení už přidali a zabrání jejich aplikaci z používán pomocí funkce pro společnost Microsoft poskytuje jeho Zákazníci, jako je například podmíněný přístup, funkce pro správu Intune a ověřování pomocí certifikátů.

Tyto přihlašovací údaje nabízí tyto výhody:

* Uživatelské prostředí jednotného přihlašování ve všech svých aplikacích bez ohledu na dodavatele.
* Aplikace můžete použít obchodní funkce, jako je například podmíněný přístup nebo sadou Intune produktů.
* Vaše aplikace může podporovat ověřování prostřednictvím certifikátu pro obchodní uživatele.
* Mnohem bezpečnější přihlašování s identitou aplikace a uživatele byly ověřeny pomocí zprostředkovatele aplikace s využitím algoritmů zvýšení zabezpečení a šifrování.

Tyto přihlašovací údaje mají následující nevýhody:

* V Iosu videoobsahem uživatele mimo prostředí vaší aplikace, zatímco je možné zvolit přihlašovací údaje.
* Ke ztrátě schopnosti spravovat přihlašovací prostředí pro zákazníky v rámci vaší aplikace.

Je zde reprezentace způsob práce se sadami SDK Microsoft Identity s zprostředkovatele aplikace, které chcete povolit jednotné přihlašování:

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

Ozbrojené pomocí těchto informací na pozadí, musí být schopen lépe pochopit a implementovat jednotného přihlašování v rámci vaší aplikace pomocí Microsoft Identity platform a sady SDK.

## <a name="enabling-cross-app-sso-using-adal"></a>Povolení jednotného přihlašování napříč aplikacemi pomocí ADAL

Používá ADAL pro iOS SDK pro:

* Zapnout bez zprostředkovatele s asistencí jednotné přihlašování pro vaše sadě aplikací
* Zapnutí podpory s asistencí zprostředkovatele jednotného přihlašování

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro bez zprostředkovatele s asistencí jednotného přihlašování

Pro jiné zprostředkovatele s asistencí jednotné přihlašování napříč aplikacemi se sadami SDK Microsoft Identity spravovat mnoho složitostí jednotného přihlašování za vás. To zahrnuje správné uživatelské hledání v mezipaměti a udržovat seznam přihlášeným uživatelům umožní dotazovat.

Pokud chcete povolit jednotné přihlašování napříč aplikacemi, které vlastníte, že budete muset provést následující:

1. Zajistěte, aby všechny uživatelské aplikace stejné ID klienta nebo ID aplikace.
2. Ujistěte se, že všechny vaše aplikace sdílet stejný podpisový certifikát od společnosti Apple tak, aby vám sdílení řetězce klíčů
3. Požádat o stejné nároků řetězce klíčů pro každou z vašich aplikací.
4. Řekněte se sadami SDK Microsoft Identity o sdíleném řetězci klíčů, že je vhodné, abyste mohli používat.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Pomocí stejné ID klienta nebo ID aplikace pro všechny aplikace ve vaší sadě aplikací

Aby Microsoft Identity platform vědět, že má povolené sdílet tokeny mezi aplikacemi každý z vašich aplikací bude nutné sdílejí stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který jste obdrželi při registraci vaší první aplikace na portálu.

Vás může zajímat, jak bude identifikovat různé aplikace ke službě Microsoft Identity, pokud se používá stejné ID aplikace. Odpověď je **identifikátory URI přesměrování**. Každá aplikace může obsahovat více identifikátory URI přesměrování registrovaný na portálu registrace. Každá aplikace ve vaší sadě bude mít na jiný identifikátor URI přesměrování. Níže je příklad toho, jak to vypadat:

Počítač app1 identifikátor URI pro přesměrování: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identifikátor URI přesměrování počítači App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identifikátor URI přesměrování App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Tyto jsou vnořené pod stejné ID klienta nebo ID aplikace a vyhledávat podle identifikátor URI se vrátit k nám v konfiguraci sady SDK pro přesměrování.

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


*Všimněte si, že formát tyto identifikátory URI přesměrování je popsán níže. Můžete použít libovolný identifikátor URI pro přesměrování Pokud si přejete podporovat broker, v takovém případě se musí vypadat podobně jako výše*

#### <a name="create-keychain-sharing-between-applications"></a>Vytvořit sdílení mezi aplikacemi řetězce klíčů

Povolení sdílení řetězce klíčů je nad rámec tohoto dokumentu a které pokrývá společností Apple v dokumentu [přidání schopností](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Co je důležité se rozhodnout, co chcete své klíčenky volat a přidat funkci napříč všemi aplikacemi.

Až budete mít oprávnění nastavit správně jste měli vidět soubor v adresáři projektu s názvem `entitlements.plist` , který obsahuje objekt, který vypadá nějak takto:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

Jakmile budete mít přístupové oprávnění povolená v každé z vašich aplikací a jste připraveni používat jednotné přihlašování, říct sadě SDK Microsoft Identity klíčenky pomocí následující nastavení v vaše `ADAuthenticationSettings` s následujícím nastavením:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Při sdílení řetězce klíčů mezi aplikacemi jakékoli aplikace můžete odstranit uživatele nebo horší odstranit všechny tokeny ve vaší aplikaci. To je zvláště katastrofální důsledky, pokud máte aplikace, které spoléhají na tokeny na pozadí pracovní. Sdílení řetězce klíčů odebrat prostředky, musí být velmi opatrní při všechny operace, ať už se sadami SDK Microsoft Identity.

A to je vše! Sada SDK Microsoft Identity teď bude sdílet přihlašovací údaje mezi všemi aplikacemi. Seznam uživatelů se také sdílet mezi instancemi aplikace.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnutí jednotného přihlašování pro zprostředkovatele s asistencí jednotného přihlašování

Možnost pro aplikace pro použití jakékoli zprostředkovatele, který je nainstalovaný na zařízení je **ve výchozím nastavení vypnuta**. Chcete-li použít aplikace pomocí zprostředkovatele musí provést další konfiguraci a přidejte nějaký kód do vaší aplikace.

Jak postupovat, jsou:

1. Povolte režim zprostředkovatele v kódu aplikace volání sady SDK MS.
2. Vytvořit nový identifikátor URI přesměrování a stanoví, že aplikace a registrace vaší aplikace.
3. Registruje se schéma adresy URL.
4. Podpora iOS9: přidejte oprávnění do souboru info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Povolení režimu zprostředkovatele v aplikaci

Možnost pro aplikace pomocí zprostředkovatele zapnutý, při vytváření "kontext" nebo počáteční nastavení ověřování objektu. To provedete tak, že nastavíte typ přihlašovacích údajů ve vašem kódu:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` Nastavení vám umožní Microsoft Identity SDK se pokouší vyžadují ke zprostředkovateli, `AD_CREDENTIALS_EMBEDDED` Microsoft Identity SDK zabrání volání do zprostředkovatele.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Registrace schéma adresy URL

Platforma Microsoft Identity používá adresy URL pro vyvolání zprostředkovatele a potom vrátit řízení zpět do aplikace. K dokončení této odezvy je třeba schéma adresy URL pro vaše aplikace, která bude znát platformě Microsoft Identity. To může být kromě jiná aplikace schémata, kterou jste pravděpodobně dříve zaregistrovali s vaší aplikací.

> [!WARNING]
> Doporučujeme, aby schéma adresy URL poměrně jedinečné, chcete-li minimalizovat pravděpodobnost jiné aplikace s použitím stejné schéma adresy URL. Apple nevynucuje jedinečnost schémata URL, která jsou zaregistrována v app storu.
> 
> 

Níže je příklad toho, jak to se zobrazuje v konfiguraci projektu. Také to lze provést i v prostředí XCode také:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Stanovení nový identifikátor URI se schématem vaše adresa URL přesměrování

Aby se zajistilo, že jsme vrátí do správné aplikace vždy tokeny přihlašovacích údajů, potřebujeme Ujistěte se, že jsme zpětné volání do vaší aplikace tak, aby operační systém iOS můžete ověřit. Operační systém iOS hlásí aplikacím zprostředkovatele Microsoft ID sady prostředků aplikace volání. To nelze falešné neautorizovaný aplikací. Proto jsme využít to spolu s identifikátorem URI naší aplikace zprostředkovatele k zajištění, že tokeny jsou vráceny do správné aplikace. Požadujeme, aby vám určit tento jedinečný identifikátor URI pro přesměrování i ve vaší aplikaci a nastavit jako identifikátor URI přesměrování naše portálu pro vývojáře.

Váš identifikátor URI pro přesměrování musí být ve správné formu:

`<app-scheme>://<your.bundle.id>`

Příklad: *x-msauth mytestiosapp://com.myapp.mytestapp*

Tento identifikátor URI pro přesměrování musí být zadaná pomocí registrace aplikace [webu Azure portal](https://portal.azure.com/). Další informace o registraci aplikace Azure AD najdete v tématu [integraci se službou Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Přidat identifikátor URI přesměrování vaší aplikace a vývojářského portálu pro podporu ověřování pomocí certifikátů

Pro podporu certifikátu ověřování na základě druhý "msauth" musí být zaregistrované ve vaší aplikaci a [webu Azure portal](https://portal.azure.com/) zpracovávat ověřování certifikátu, pokud chcete přidat, které podporují ve vaší aplikaci.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Příklad: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Krok 4: iOS9: Přidání konfiguračního parametru do vaší aplikace

ADAL využívá – canOpenURL: Zkontrolujte, jestli je na zařízení nainstalovaný zprostředkovatel. V Iosu 9 Apple uzamčené co schémata aplikaci dotázat. Budete muset přidat "msauth" LSApplicationQueriesSchemes část vašeho `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Dokončení konfigurace jednotného přihlašování!

Nyní sada SDK Microsoft Identity bude automaticky přihlašovací údaje sdílené složky mezi aplikacemi i zprostředkovatel vyvolat, pokud je k dispozici na svém zařízení.