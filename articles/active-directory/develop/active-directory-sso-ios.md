---
title: Postup povolení jednotného přihlašování napříč aplikacemi v systému iOS pomocí ADAL | Microsoft Docs
description: 'Jak používat funkce sady ADAL SDK povolit jednotné přihlašování v rámci vaší aplikace. '
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
ms.openlocfilehash: b0179e217a23d612ddcdc219f589a5d1fbca644e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Postup povolení jednotného přihlašování napříč aplikacemi v systému iOS pomocí ADAL
Pokud jednotné přihlašování (SSO), aby uživatelé stačí jednou zadat své přihlašovací údaje a mají tyto přihlašovací údaje automaticky fungovat na všech aplikací nyní očekává zákazníků. Problémy se zadáním uživatelského jména a hesla na malou obrazovku, často časy v kombinaci s další faktor (2FA) jako telefonní hovor nebo kód zasílání zpráv SMS, má za následek rychlé nespokojenosti, pokud uživatel má k tomu více než jednou pro svůj produkt.

Kromě toho pokud použijete identity platforma, která mohou používat jiné aplikace například Accounts Microsoft nebo pracovní účet z Office 365, zákazníci očekávají, že ty pověření být k dispozici pro použití na všechny svoje aplikace bez ohledu na dodavatele.

Do platformy Microsoft Identity, společně s naše sady SDK Identity Microsoft nepodporuje tento pevný pracovní pro vás a vám dává možnost delight zákazníků pomocí jednotného přihlašování, buď v rámci vlastní sada aplikací nebo, stejně jako u našich schopností zprostředkovatele a ověřovací aplikacemi, v rámci celého zařízení.

Tento návod popisuje postup konfigurace naše sady SDK v rámci aplikace umožní vašim zákazníkům poskytovat této výhody.

Tento postup platí pro:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory s B2B
* Podmíněný přístup k Azure Active Directory

Předchozí dokument předpokládá, že víte, jak [zřídit aplikace na portálu pro starší verze pro Azure Active Directory](active-directory-how-to-integrate.md) a integrované aplikace s [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Koncepty jednotné přihlašování v platformě Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Zprostředkovatelé Microsoft Identity
Společnost Microsoft poskytuje aplikace pro každou platformu mobilních umožňujících přemostění přihlašovacích údajů napříč aplikacemi od různých dodavatelů a umožňuje pro speciální rozšířené funkce, které vyžadují jeden bezpečné místo, odkud se ověřit přihlašovací údaje. Tyto říkáme **makléřům**. Na iOS a Android tyto zprostředkovatelé jsou k dispozici ke stažení aplikace, aby zákazníci nainstalovat nezávisle na, nebo můžete nabídnutých do zařízení ve společnosti, který spravuje některých nebo všech zařízení pro své zaměstnance. Správa zabezpečení těchto zprostředkovatelé podporují jenom pro některé aplikace nebo ze zařízení podle potřeby co správci IT. V systému Windows je tato funkce poskytuje výběru účtu, který je součástí operačního systému, známé technicky jako zprostředkovatele webového ověření.

Další informace o tom, použijeme tyto zprostředkovatelé a jak vaši zákazníci mohou zobrazit je v jejich toku přihlášení pro čtení na platformu Microsoft Identity.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Vzory pro přihlašování na mobilních zařízeních
Přístup k přihlašovacím údajům v zařízení podle dva základní vzory pro platformu Microsoft Identity:

* Přihlášení odbornou bez zprostředkovatele
* Zprostředkovatel odbornou přihlášení

#### <a name="non-broker-assisted-logins"></a>Přihlášení odbornou bez zprostředkovatele
Přihlášení odbornou non-broker jsou možností přihlášení, které dojít vložené s aplikací a použít místní úložiště na zařízení pro tuto aplikaci. Toto úložiště může být sdíleny s aplikací, ale přihlašovací údaje jsou úzce vázaný aplikace nebo sadu aplikací pomocí tohoto pověření. Jste s největší pravděpodobností došlo k to v mnoha mobilních aplikacích. když zadáte uživatelské jméno a heslo v rámci vlastní aplikace.

Tyto přihlášení mají následující výhody:

* Činnost koncového uživatele zcela v aplikaci existuje.
* Přihlašovací údaje můžete sdílet mezi aplikací, které jsou podepsány stejný certifikát, poskytování jeden přihlašování pro vaše sada aplikací.
* Ovládací prvek kolem možností přihlášení je k dispozici do aplikace před a po přihlášení.

Tyto přihlášení mají tyto nevýhody:

* Uživatele nelze jednotného přihlašování napříč všechny aplikace, které používají Microsoft Identity pouze v rámci těchto Identities Microsoft, které má vaše aplikace nakonfigurovaná.
* Aplikaci nelze použít s dalších pokročilých funkcí firmy, jako je například podmíněný přístup, nebo použijte produktů sady InTune.
* Aplikace nepodporuje ověřování pomocí certifikátů pro uživatele.

Zde je reprezentace fungování sadami SDK služby Microsoft Identity se sdíleným úložištěm aplikací k povolení přihlášení SSO:

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

#### <a name="broker-assisted-logins"></a>Zprostředkovatel odbornou přihlášení
S pomocí zprostředkovatele přihlášení jsou přihlášení prostředí, v rámci zprostředkovatele aplikace, které používají úložiště a security zprostředkovatele sdílet přihlašovací údaje ve všech aplikací v zařízení, které se vztahují na platformu Microsoft Identity. To znamená, že vaše aplikace závisí na broker k přihlášení uživatele. Na iOS a Android tyto zprostředkovatelé jsou k dispozici ke stažení aplikace, aby zákazníci nainstalovat nezávisle na, nebo můžete nabídnutých do zařízení ve společnosti, který spravuje zařízení pro svoje uživatele. Příkladem tento typ aplikace je aplikace Microsoft Authenticator v systému iOS. V systému Windows je tato funkce poskytuje výběru účtu, který je součástí operačního systému, známé technicky jako zprostředkovatele webového ověření.
Možnosti se liší podle platformy a v některých případech můžou narušovat běh produktu uživatelům není správně spravovat. Jste pravděpodobně nejvíce obeznámeni s tento vzor, pokud máte nainstalovanou aplikací služby Facebook a použijete Facebook připojit z jiné aplikace. Platforma Microsoft Identity používá stejného vzoru.

Pro iOS, které to vede k "přechodu" animace, kdy se vaše aplikace odesílají na pozadí při aplikace Microsoft Authenticator obsahuje popředí pro uživatele k výběru účtu, který se chcete přihlásit. 

Pro Android a Windows výběru účtu, zobrazí se na aplikace, což je méně rušivý uživateli.

#### <a name="how-the-broker-gets-invoked"></a>Jak se zprostředkovatel volán
Pokud je kompatibilní zprostředkovatel nainstalovaný na zařízení, jako je aplikace Microsoft Authenticator sadami SDK služby Microsoft Identity automaticky provede práci při vyvolání zprostředkovatele pro vás, když uživatel označuje, že se chcete přihlásit pomocí libovolného účtu z platformy Microsoft Identity. Tento účet může být osobní Account Microsoft, pracovní nebo školní účet, nebo účtu, který zadáte a hostitele v Azure pomocí našich produktů B2C a B2B. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Jakým způsobem je zajištěno aplikace je platný
 
 Je třeba zajistit identity volání aplikace, které je zásadní zabezpečení, které poskytujeme ve zprostředkovateli zprostředkovatele s asistencí přihlášení. IOS ani Android vynucuje jedinečné identifikátory, které jsou platné pouze pro danou aplikaci, tak, aby škodlivé aplikace může "zfalšovat" identifikátor legitimní aplikace a přijímat tokeny určená pro oprávněné aplikaci. Ujistěte se, že jsme vždy komunikují pomocí správné aplikace za běhu, požádáme vývojáři poskytnout vlastní redirectURI při registraci své aplikace se společností Microsoft. **Jak vývojáři měli vytvořit tento identifikátor URI pro přesměrování je podrobněji níže.** Tato vlastní redirectURI obsahuje ID sady prostředků aplikace a je zajištěna jedinečnost k aplikaci Apple App Storu. Pokud aplikace zavolá zprostředkovatele, požádá zprostředkovatele operačního systému iOS poskytnout ID sady, který volá zprostředkovatele. Zprostředkovatel poskytuje toto ID sady prostředků společnosti Microsoft ve volání naše systém identit. Pokud ID sady prostředků aplikace neodpovídá ID sady, které vývojář poskytuje nám během registrace, jsme odepře přístup k tokeny pro prostředek aplikace požaduje. Tato kontrola zajistí, že pouze aplikace, které jsou zaregistrované vývojáře přijímá tokeny.

**Vývojář musí volba, pokud sada SDK Microsoft Identity volá zprostředkovatele nebo používá odbornou toku bez zprostředkovatele.** Ale pokud vývojář vybere nepoužívat toku s asistencí služby broker nich došlo ke ztrátě výhodou použití jednotného přihlašování k přihlašovací údaje, které uživatel může na zařízení již přidali a zabrání jejich aplikaci z používaný s funkcí business, které společnost Microsoft poskytuje svým zákazníkům, jako je například podmíněný přístup, možnosti správy Intune a ověřování pomocí certifikátů.

Tyto přihlášení mají následující výhody:

* Uživatel vyskytne jednotného přihlašování na všechny svoje aplikace bez ohledu na dodavatele.
* Aplikace můžete použít dalších pokročilých funkcí firmy, jako je například podmíněný přístup nebo používat sadu InTune produktů.
* Aplikace může podporovat ověřování pomocí certifikátů pro uživatele.
* Mnohem bezpečnější přihlašovat jako identita aplikace a uživatel se ověřit pomocí zprostředkovatele aplikace s další bezpečnostní algoritmů hash a šifrování.

Tyto přihlášení mají tyto nevýhody:

* V iOS převedena uživatele mimo prostředí vaší aplikace, když je možné zvolit přihlašovací údaje.
* Ztráta schopnost spravovat přihlašování pro vaši zákazníci v rámci vaší aplikace.

Zde je reprezentace fungování sadami SDK služby Microsoft Identity s aplikacemi zprostředkovatele k povolení přihlášení SSO:

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

Díky této základní informace, které byste měli mít lépe pochopit a implementovat jednotné přihlašování v rámci vaší aplikace pomocí platformy Microsoft Identity a sady SDK.

## <a name="enabling-cross-app-sso-using-adal"></a>Povolení jednotného přihlašování napříč aplikacemi pomocí ADAL
Tady používáme ADAL iOS SDK na:

* Zapněte bez zprostředkovatele s pomocí jednotného přihlašování pro vaše sada aplikací
* Zapnutí podpory pro jednotné přihlašování s asistencí zprostředkovatele

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Zapnout jednotné přihlašování pro bez zprostředkovatele s pomocí jednotného přihlašování
Bez zprostředkovatele odbornou jednotné přihlašování napříč aplikacemi spravovat sadami SDK služby Microsoft Identity velkou část složitosti jednotného přihlašování pro vás. To zahrnuje správné uživatelské hledání v mezipaměti a udržování seznam přihlášeného uživatele pro vás k dotazování.

K povolení jednotného přihlašování napříč aplikacemi, které vlastníte, že musíte udělat následující:

1. Zkontrolujte všechny uživatelské aplikace stejné ID klienta nebo ID aplikace.
2. Ujistěte se, že všechny aplikace sdílet stejný podpisový certifikát od společnosti Apple tak, že můžete sdílet řetězce klíčů
3. Požádat o stejné nárocích řetězce klíčů pro každou z vašich aplikací.
4. Chcete, abychom mohli používat, informujte o sdíleném řetězci klíčů sadami SDK služby Microsoft Identity.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Pomocí stejné ID klienta / ID aplikace pro všechny aplikace ve vaší sadě aplikací
V pořadí pro platformu Microsoft Identity vědět, že má povolené sdílet tokeny ve vašich aplikací každý z vašich aplikací bude nutné sdílejí stejné ID klienta nebo ID aplikace. Toto je jedinečný identifikátor, který jste získali při registraci vaší první aplikace v portálu.

Asi vás zajímá, jak bude identifikujete různé aplikace ke službě Microsoft Identity, pokud používá stejné ID aplikace Je odpověď **identifikátory URI přesměrování**. Každá aplikace může mít několik přesměrování identifikátory URI registrován v portálu registrace. Každá aplikace ve vaší sadě bude mít na jiný identifikátor URI přesměrování. Zde je příklad, jak to vypadá:

Identifikátor URI přesměrování app1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identifikátor URI přesměrování počítači App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identifikátor URI přesměrování App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Tyto jsou vnořeny pod stejným ID klienta nebo ID aplikace a vyhledávat podle identifikátor URI návratu do us ve vaší konfiguraci SDK přesměrování.

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


*Všimněte si, že formát tyto identifikátory URI přesměrování jsou vysvětleny níže. Můžete použít všechny URI přesměrování, pokud chcete pro podporu zprostředkovatele, v takovém případě se musí vypadat podobně jako výše*

#### <a name="create-keychain-sharing-between-applications"></a>Vytvořte sdílení mezi aplikacemi řetězce klíčů
Povolení sdílení řetězce klíčů je nad rámec tohoto dokumentu a jejich dokument nezabývá společností Apple [přidání schopností](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Co je důležité se rozhodnout, co chcete klíčů a k volání a přidejte tuto funkci u všech aplikací.

Pokud máte oprávnění nastavit správně jste měli vidět soubor v adresáři projektu s názvem `entitlements.plist` obsahující objekt, který vypadá takto:

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

Jakmile máte nárok řetězce klíčů povolené v každé z vašich aplikací a jste připraveni používat jednotné přihlašování, říct sadě SDK, Identity Microsoft o vaší řetězce klíčů pomocí následující nastavení v vaší `ADAuthenticationSettings` s následujícím nastavením:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Když sdílíte mezi vaší aplikace řetězce klíčů všechny aplikace můžete odstranit uživatele nebo horší odstranit všechny tokeny napříč aplikací. To je zvlášť katastrofální, pokud máte aplikace, které jsou závislé na tokeny pro práci na pozadí. Sdílení řetězce klíčů odebrat znamená, že musí být velmi opatrní při všech operací prostřednictvím sady SDK Microsoft Identity.
> 
> 

A to je vše! Sada SDK Microsoft Identity bude nyní sdílet přihlašovací údaje ve všech aplikací. Seznam uživatelů bude také sdílet mezi instancemi aplikace.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Zapnout jednotné přihlašování pro zprostředkovatele s pomocí jednotného přihlašování
Možnost pro aplikace pro použití žádné zprostředkovatele, který je nainstalován na zařízení je **ve výchozím nastavení vypnuté**. Chcete-li použít vaší aplikace pomocí zprostředkovatele musí provést některé další konfiguraci a přidejte nějaký kód do vaší aplikace.

Jak postupovat, jsou:

1. Povolte režim zprostředkovatele v kódu aplikace volání sady SDK MS.
2. Vytvořit nový identifikátor URI přesměrování a stanovit, že aplikace a registrace aplikace.
3. Probíhá registrace schéma adresy URL.
4. Podpora iOS9: přidejte oprávnění do souboru info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1: Povolení režimu zprostředkovatele v aplikaci
Možnosti pro aplikace pomocí zprostředkovatele zapnutý, při vytváření "context" nebo počáteční nastavení ověřování objektu. To provedete nastavením typ vaše přihlašovací údaje ve vašem kódu:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` Nastavení umožní Microsoft Identity SDK můžete vyzkoušet vyvolávající pro zprostředkovatele, `AD_CREDENTIALS_EMBEDDED` Microsoft Identity SDK zabrání volání pro zprostředkovatele.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2: Registrace schéma adresy URL
Platforma Microsoft Identity adresy URL používá k vyvolání zprostředkovatele a pak se vraťte řízení zpět do vaší aplikace. K dokončení této odezvy musíte zaregistrovat pro vaše aplikace, která bude platformou Microsoft Identity vědět o schéma adres URL. To může být kromě jiných aplikace režimů, které mají může dříve registrován u vaší aplikace.

> [!WARNING]
> Doporučujeme schématu adresy URL poměrně jedinečný, chcete-li minimalizovat pravděpodobnost jiné aplikaci pomocí stejné schéma adresy URL. Apple nevynucuje jedinečnost schémata URL, které jsou zaregistrovány v app storu.
> 
> 

Níže je příklad, jak se objeví v konfigurace projektu. Také to lze provést v prostředí XCode také:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3: Stanovení na nový identifikátor URI s vaše schéma adresy URL přesměrování
Aby se zajistilo, že vrátíme vždy tokeny přihlašovacích údajů pro správnou aplikaci, musíme Ujistěte se, že jsme zpětné volání do vaší aplikace tak, aby operační systém iOS můžete ověřit. Operační systém iOS sestav ID sady prostředků aplikace volání ho zprostředkovatele aplikací společnosti Microsoft. To nelze maskování podvodný aplikace. Proto jsme využít to společně se identifikátor URI aplikace zprostředkovatele k zajištění, že tokeny jsou vráceny správné aplikace. Je nutné vytvořit tento jedinečný identifikátor URI pro přesměrování oba ve vaší aplikaci a nastavit jako identifikátor URI přesměrování v našem portál pro vývojáře.

Váš identifikátor URI pro přesměrování musí být ve správném formátu:

`<app-scheme>://<your.bundle.id>`

například: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Tento identifikátor URI pro přesměrování musí být zadána v registrace vaší aplikace pomocí [portál Azure](https://portal.azure.com/). Další informace o registraci aplikace Azure AD najdete v tématu [integraci s Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: přidejte identifikátor URI přesměrování vaší aplikace a vývojářů portálu pro podporu ověřování na základě certifikátů
Pro podporu ověřování na základě certifikátu druhý "msauth" musí být zaregistrovaný ve vaší aplikaci a [portál Azure](https://portal.azure.com/) pro zpracování ověření certifikátu, pokud chcete přidat podporující ve vaší aplikaci.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

například: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Krok 4: iOS9: Přidání konfiguračního parametru do aplikace
ADAL používá – canOpenURL: Zkontrolujte, zda je na zařízení nainstalován zprostředkovatel. V systému iOS 9 Apple uzamčené co schémata aplikaci dotázat. Budete muset přidat do sekce LSApplicationQueriesSchemes "msauth" vaší `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Jednotné přihlašování jste nakonfigurovali!
Nyní Microsoft Identity SDK budou automaticky sdílet přihlašovací údaje v rámci aplikace i vyvolání zprostředkovatele, pokud je k dispozici na svém zařízení.

