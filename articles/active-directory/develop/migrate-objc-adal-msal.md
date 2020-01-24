---
title: Příručka k migraci ADAL do MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi MSAL pro iOS/macOS a s knihovnou ověřování Azure AD pro ObjectiveC (ADAL. ObjC) a jak migrovat na MSAL pro iOS/macOS.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 311d6ed988777e94f5dd3fde8ac6e9aff1fb39fe
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696669"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrace aplikací do MSAL pro iOS a macOS

Pro práci s účty Azure Active Directory prostřednictvím koncového bodu v 1.0 byla vytvořena knihovna ověřování Azure Active Directory ([ADAL cíl-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)).

Knihovna Microsoft Authentication Library pro iOS a macOS (MSAL) je sestavená tak, aby fungovala se všemi identitami Microsoftu, jako jsou účty Azure Active Directory (Azure AD), osobní účty Microsoft a účty Azure AD B2C prostřednictvím platformy Microsoft identity (formálně koncový bod Azure AD v 2.0.

Platforma Microsoft identity má několik klíčových rozdílů s Azure Active Directory v 1.0. Tento článek popisuje tyto rozdíly a poskytuje pokyny k migraci aplikace z ADAL do MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Rozdíly mezi možnostmi aplikace ADAL a MSAL

### <a name="who-can-sign-in"></a>Kdo se může přihlásit

* ADAL podporuje pouze pracovní a školní účty, označované také jako účty Azure AD.
* MSAL podporuje osobní účty Microsoft (účty MSA), jako jsou Hotmail.com, Outlook.com a Live.com.
* MSAL podporuje pracovní a školní účty a účty Azure AD B2C.

### <a name="standards-compliance"></a>Dodržování standardů

* Koncový bod Microsoft Identity Platform sleduje standardy OAuth 2,0 a OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Přírůstkové a dynamické vyjádření souhlasu

* Koncový bod Azure Active Directory v 1.0 vyžaduje, aby byla při registraci aplikace předem deklarována všechna oprávnění. To znamená, že tato oprávnění jsou statická.
* Platforma Microsoft Identity vám umožňuje dynamicky požádat o oprávnění. Aplikace můžou požádat o oprávnění pouze podle potřeby a požádat o další informace, jak je aplikace potřebuje.

Další informace o rozdílech mezi Azure Active Directory v 1.0 a platformou Microsoft identity najdete v tématu [Proč aktualizovat na Microsoft Identity Platform (v 2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>Rozdíly v knihovně ADAL a MSAL

Veřejné rozhraní API MSAL odráží několik klíčových rozdílů mezi Azure AD v 1.0 a platformou Microsoft identity.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication místo ADAuthenticationContext

`ADAuthenticationContext` je první objekt, který aplikace ADAL vytvoří. Představuje instanci ADAL. Aplikace vytvoří novou instanci `ADAuthenticationContext` pro každou kombinaci Azure Active Directory cloudu a tenanta (Authority). Stejný `ADAuthenticationContext` lze použít k získání tokenů pro více veřejných klientských aplikací.

V MSAL je hlavní interakcí prostřednictvím objektu `MSALPublicClientApplication`, který se modeluje po [veřejném klientovi OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-2.1). Jednu instanci `MSALPublicClientApplication` lze použít k interakci s více cloudy AAD a klienty, aniž byste museli vytvářet novou instanci pro každou autoritu. Pro většinu aplikací je jedna instance `MSALPublicClientApplication` dostatečná.

### <a name="scopes-instead-of-resources"></a>Rozsahy namísto prostředků

V ADAL musela aplikace poskytnout identifikátor *prostředku* , například `https://graph.microsoft.com` získat tokeny z koncového bodu Azure Active Directory v 1.0. Prostředek může definovat počet oborů nebo oAuth2Permissions v manifestu aplikace, který to porozuměl. Tyto povolené klientské aplikace vyžadují tokeny od daného prostředku pro určitou sadu oborů předem definovaných během registrace aplikace.

V MSAL místo jednoho identifikátoru prostředku poskytují aplikace sadu oborů na požadavek. Obor je identifikátor prostředku následovaný názvem oprávnění ve formuláři nebo oprávněním. Například `https://graph.microsoft.com/user.read`.

Existují dva způsoby, jak poskytnout obory v MSAL:

* Zadejte seznam všech oprávnění, která aplikace potřebuje. Například: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    V takovém případě aplikace požaduje `directory.read` a `directory.write` oprávnění. Uživatel bude požádán o souhlas s těmito oprávněními, pokud je před touto aplikací nesouhlasí. Aplikace může také obdržet další oprávnění, která uživatel již souhlasil s aplikací. Uživateli se zobrazí výzva k vyjádření souhlasu s novými oprávněními nebo oprávnění, která nebyla udělena.

* Obor `/.default`.

Toto je vestavěný rozsah pro každou aplikaci. Odkazuje na statický seznam oprávnění nakonfigurovaných při registraci aplikace. Jeho chování je podobné jako `resource`. To může být užitečné při migraci, aby se zajistilo, že bude zachována podobná sada oborů a činnost koncového uživatele.

Chcete-li použít obor `/.default`, přidejte `/.default` k identifikátoru prostředku. Například: `https://graph.microsoft.com/.default`. Pokud váš prostředek končí lomítkem (`/`), měli byste k tomu i nadále připojovat `/.default`, včetně počátečního lomítka, a to v oboru, který obsahuje dvojité lomítko (`//`).

Další informace o používání oboru "/.default" si můžete přečíst [tady](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope) .

### <a name="supporting-different-webview-types--browsers"></a>Podpora různých typů webových zobrazení & prohlížečů

ADAL podporuje pouze UIWebView/WKWebView pro iOS a WebView pro macOS. MSAL pro iOS podporuje další možnosti zobrazení webového obsahu při vyžádání autorizačního kódu a už nepodporuje `UIWebView`; což může zlepšit uživatelské prostředí a zabezpečení.

Ve výchozím nastavení MSAL v systému iOS používá [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), což je webová komponenta, kterou Apple doporučuje pro ověřování na zařízeních s iOS 12 +. Poskytuje výhody jednotného přihlašování (SSO) prostřednictvím sdílení souborů cookie mezi aplikacemi a prohlížečem Safari.

Můžete použít jinou webovou komponentu v závislosti na požadavcích aplikace a na možnosti koncového uživatele, kterou požadujete. Další možnosti najdete v tématu [podporované typy webových zobrazení](customize-webviews.md) .

Při migraci z knihovny ADAL na MSAL `WKWebView` poskytuje činnost koncového uživatele, která se podobá ADAL v iOS a macOS. Pokud je to možné, doporučujeme migrovat na `ASWebAuthenticationSession` v iOS. Pro macOS doporučujeme, abyste používali `WKWebView`.

### <a name="account-management-api-differences"></a>Rozdíly v rozhraní API pro správu účtů

Při volání metod ADAL `acquireToken()` nebo `acquireTokenSilent()`obdržíte objekt `ADUserInformation` obsahující seznam deklarací identity ze `id_token`, které představují ověřovaný účet. Kromě toho `ADUserInformation` vrátí `userId` na základě deklarace `upn`. Po počátečním získání interaktivního tokenu ADAL očekává, že vývojář poskytne `userId` ve všech tichých voláních.

ADAL neposkytuje rozhraní API k načtení známých identit uživatelů. Spoléhá na to, že aplikace ukládá a spravuje tyto účty.

MSAL poskytuje sadu rozhraní API pro výpis všech účtů známých pro MSAL bez nutnosti získat token.

Podobně jako ADAL MSAL vrací informace o účtu, který obsahuje seznam deklarací identity z `id_token`. Je součástí objektu `MSALAccount` uvnitř objektu `MSALResult`.

MSAL poskytuje sadu rozhraní API pro odebrání účtů a tím nepřístupných účtů k aplikacím. Po odebrání účtu se později volání pro získání tokenu vyzve uživatele k provedení interaktivního získání tokenu. Odebrání účtu se vztahuje jenom na klientskou aplikaci, která ji spustila, a neodebere účet z ostatních aplikací spuštěných v zařízení nebo v prohlížeči systému. Tím se zajistí, že uživatel bude i po odhlášení z jednotlivé aplikace i nadále mít v zařízení možnost jednotného přihlašování.

MSAL také vrací identifikátor účtu, který lze použít k tiché vyžádání tokenu později. Identifikátor účtu (přístupný prostřednictvím `identifier` vlastnosti v objektu `MSALAccount`) však nelze zobrazit a nemůžete předpokládat, který formát je v, ani byste se měli pokusit interpretovat nebo analyzovat.

### <a name="migrating-the-account-cache"></a>Migrace mezipaměti účtu

Při migraci z knihovny ADAL aplikace normálně ukládají `userId`ADAL, které nemají `identifier` vyžadované v MSAL. V rámci jednorázového kroku migrace může aplikace zadat dotaz na účet MSAL pomocí ID účtu ADAL s následujícím rozhraním API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Toto rozhraní API přečte mezipaměť MSAL a ADAL, aby našel účet pomocí ADAL userId (UPN).

V případě nalezení účtu by měl vývojář použít účet k tomu, aby se získalo tiché získání tokenu. První pasivní získání tokenu bude efektivně aktualizovat účet a vývojář Získá identifikátor účtu kompatibilního s MSAL ve výsledku MSAL (`identifier`). V takovém případě by se měly pro vyhledávání účtů použít jenom `identifier` pomocí následujícího rozhraní API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

I když je možné i nadále používat `userId` ADAL pro všechny operace v MSAL, protože `userId` je založená na hlavní název uživatele (UPN), je v něm více omezení, která mají za následek špatné uživatelské prostředí. Pokud se například hlavní název uživatele změní, uživatel se musí znovu přihlásit. Pro všechny operace doporučujeme, aby všechny aplikace používaly `identifier` účtu, který neumožňuje nezobrazitelný.

Přečtěte si další informace o [migraci stavu mezipaměti](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Změny získání tokenu

MSAL zavádí některé změny volání pro získání tokenu:

* Podobně jako ADAL `acquireTokenSilent` vždy vede k tichému požadavku.
* Na rozdíl od ADAL `acquireToken` vždy výsledky uživatelského rozhraní, které je možné využít, buď prostřednictvím webového zobrazení, nebo Microsoft Authenticator aplikace. V závislosti na stavu jednotného přihlašování ve službě WebView/Microsoft Authenticator se uživateli může zobrazit výzva k zadání přihlašovacích údajů.
* V ADAL `acquireToken` s `AD_PROMPT_AUTO` nejprve pokusy o získání tichého tokenu a zobrazí pouze uživatelské rozhraní v případě, že se bezobslužný požadavek nezdařil. V MSAL lze tuto logiku dosáhnout prvním voláním `acquireTokenSilent` a pouze volání `acquireToken` v případě, že se nezdařilo tiché získání. To umožňuje vývojářům přizpůsobit uživatelské prostředí před spuštěním interaktivního získání tokenu.

### <a name="error-handling-differences"></a>Rozdíly v manipulaci s chybami

MSAL poskytuje větší přehlednost mezi chybami, které může zpracovat vaše aplikace a které vyžadují zásah uživatele. Existuje omezený počet chyb, které může vývojář zpracovat:

* `MSALErrorInteractionRequired`: uživatel musí provést interaktivní požadavek. To může být způsobeno různými důvody, jako je například relace ověřování s vypršenou platností, zásady podmíněného přístupu se změnily, vypršela platnost obnovovacího tokenu nebo byl odvolán, neexistují žádné platné tokeny v mezipaměti atd.
* `MSALErrorServerDeclinedScopes`: požadavek nebyl zcela dokončen a některému z oborů nebyl udělen přístup. Může to být způsobeno odmítnutím souhlasu uživatele s jedním nebo více obory.

Zpracování všech dalších chyb v [seznamu`MSALError`](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) je volitelné. Pomocí informací v těchto chybách můžete zlepšit činnost koncového uživatele.

Další informace o zpracování chyb MSAL najdete v tématu [zpracování výjimek a chyb pomocí MSAL](msal-handling-exceptions.md) .

### <a name="broker-support"></a>Podpora zprostředkovatele

MSAL, počínaje verzí 0.3.0, poskytuje podporu pro zprostředkované ověřování pomocí aplikace Microsoft Authenticator. Microsoft Authenticator taky umožňuje podporu scénářů podmíněného přístupu. Příklady scénářů podmíněného přístupu zahrnují zásady dodržování předpisů zařízením, které vyžadují, aby uživatel zaregistroval zařízení přes Intune nebo zaregistroval službu AAD, aby získal token. A zásady podmíněného přístupu správy mobilních aplikací (MAM), které vyžadují ověření shody před tím, než aplikace získá token.

Postup povolení zprostředkovatele pro vaši aplikaci:

1. Zaregistrujte pro aplikaci formát identifikátoru URI přesměrování kompatibilního s zprostředkovatelem. Formát identifikátoru URI přesměrování kompatibilního s zprostředkovatelem je `msauth.<app.bundle.id>://auth`. Nahraďte `<app.bundle.id>` IDENTIFIKÁTORem sady prostředků vaší aplikace. Pokud migrujete z ADAL a vaše aplikace už je zavedená, nemusíte nic dalšího dělat. Váš předchozí identifikátor URI pro přesměrování je plně kompatibilní s MSAL, takže můžete přejít na krok 3.

2. Do souboru info. plist přidejte schéma identifikátoru URI pro přesměrování vaší aplikace. Pro výchozí identifikátor URI pro přesměrování MSAL je formát `msauth.<app.bundle.id>`. Například:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Do souboru info. plist vaší aplikace přidejte v rámci LSApplicationQueriesSchemes následující schémata:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Přidejte následující do souboru AppDelegate. m pro zpracování zpětných volání: cíl-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Business to Business (B2B)

V ADAL vytvoříte samostatné instance `ADAuthenticationContext` pro každého tenanta, pro který aplikace požaduje tokeny. Už to není v MSAL požadavek. V MSAL můžete vytvořit jednu instanci `MSALPublicClientApplication` a použít ji pro libovolný Cloud a organizaci AAD, a to tak, že zadáte jinou autoritu pro volání acquireToken a acquireTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>Jednotné přihlašování v partnerství s ostatními sadami SDK

MSAL pro iOS může pomocí sjednocené mezipaměti dosáhnout jednotného přihlašování pomocí následujících sad SDK:

- ADAL – cíl – C 2.7. x +
- MSAL.NET pro Xamarin 2.4. x +
- ADAL.NET pro Xamarin 4.4. x +

Jednotné přihlašování se dosahuje prostřednictvím sdílení řetězce klíčů iOS a je dostupné jenom mezi aplikacemi, které jsou publikované ze stejného účtu Apple Developer.

JEDNOTNÉ přihlašování prostřednictvím iOS pro iOS je jediným typem tichého jednotného přihlašování.

V macOS se MSAL dá dosáhnout jednotného přihlašování s dalšími MSAL pro aplikace založené na iOS a macOS a aplikace založené na aplikacích ADAL založené na jazyce C.

MSAL v iOS taky podporuje dva další typy jednotného přihlašování:

* SSO přes webový prohlížeč. MSAL pro iOS podporuje `ASWebAuthenticationSession`, která poskytuje jednotné přihlašování přes soubory cookie sdílené mezi ostatními aplikacemi na zařízení a speciálně v prohlížeči Safari.
* SSO prostřednictvím zprostředkovatele ověřování. V zařízení se systémem iOS Microsoft Authenticator funguje jako zprostředkovatel ověřování. Může docházet k zásadám podmíněného přístupu, jako je třeba vyžadování vyhovujícího zařízení, a poskytuje jednotné přihlašování pro registrovaná zařízení. MSAL sady SDK počínaje verzí 0.3.0 podporují ve výchozím nastavení zprostředkovatele.

## <a name="intune-mam-sdk"></a>Sada Intune MAM SDK

[Sada Intune mam SDK](https://docs.microsoft.com/intune/app-sdk-get-started) podporuje MSAL pro iOS počínaje verzí [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL a ADAL ve stejné aplikaci

ADAL verze 2.7.0 a vyšší nemůže koexistovat s MSAL ve stejné aplikaci. Hlavním důvodem je kvůli společnému kódu sdíleného dílčího modulu. Vzhledem k tomu, že cíl-C nepodporuje obory názvů, pokud přidáte do své aplikace rozhraní ADAL i MSAL, budou existovat dvě instance stejné třídy. Není nijak zaručeno, které z nich se za běhu vybralo. Pokud obě sady SDK používají stejnou verzi konfliktní třídy, může vaše aplikace pořád fungovat. Pokud se ale jedná o jinou verzi, může dojít k neočekávaným chybám, které se obtížně diagnostikují v aplikaci.

Spouštění ADAL a MSAL ve stejné produkční aplikaci se nepodporuje. Pokud ale jenom testujete a migrujete své uživatele z knihovny ADAL cíl-C do MSAL pro iOS a macOS, můžete pokračovat v používání vlastnosti [ADAL cíl-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Je to jediná verze, která funguje s MSAL ve stejné aplikaci. Pro tuto verzi ADAL nebudou k dispozici žádné nové aktualizace funkcí, takže by se měly použít jenom pro účely migrace a testování. Vaše aplikace by neměla spoléhat na MSAL dlouhodobě a koexistence.

Koexistence ADAL a MSAL ve stejné aplikaci není podporována.
ADAL a MSAL koexistence mezi více aplikacemi je plně podporovaná.

## <a name="practical-migration-steps"></a>Praktické kroky migrace

### <a name="app-registration-migration"></a>Migrace registrace aplikace

Nemusíte měnit stávající aplikaci AAD, abyste přešli na MSAL a povolili účty AAD. Pokud však vaše aplikace založené na knihovně ADAL nepodporuje zprostředkované ověřování, bude nutné před přechodem na MSAL zaregistrovat nový identifikátor URI přesměrování pro aplikaci.

Identifikátor URI přesměrování by měl být v tomto formátu: `msauth.<app.bundle.id>://auth`. Nahraďte `<app.bundle.id>` IDENTIFIKÁTORem sady prostředků vaší aplikace. Zadejte identifikátor URI přesměrování v [Azure Portal](https://aka.ms/MobileAppReg).

Pouze pro iOS: aby bylo možné podporovat ověřování na základě certifikátů, je nutné v aplikaci zaregistrovat Další identifikátor URI pro přesměrování a Azure Portal v následujícím formátu: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Například `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`.

Doporučujeme, aby všechny aplikace registrovaly identifikátory URI pro přesměrování.

Pokud chcete přidat podporu pro přírůstkový souhlas, vyberte rozhraní API a oprávnění, která aplikace je nakonfigurovaná tak, aby požadovala přístup k vaší registraci aplikace v rámci karty **oprávnění rozhraní API** .

Pokud migrujete z ADAL a chcete podporovat účty AAD i MSA, je potřeba aktualizovat existující registraci aplikace, aby podporovala obě. Nedoporučujeme aktualizovat stávající produkční aplikaci, aby podporovala AAD i MSA hned. Místo toho vytvořte jiné ID klienta, které podporuje AAD i MSA pro testování, a až ověříte, že všechny scénáře fungují, aktualizujte existující aplikaci.

### <a name="add-msal-to-your-app"></a>Přidání MSAL do aplikace

Sadu MSAL SDK můžete do své aplikace přidat pomocí preferovaného nástroje pro správu balíčků. [Tady najdete podrobné pokyny](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aktualizujte soubor info. plist vaší aplikace.

Pouze pro iOS přidejte schéma identifikátoru URI přesměrování vaší aplikace do souboru info. plist. Pro aplikace kompatibilní se zprostředkovatelem ADAL by to mělo být již. Výchozí schéma identifikátoru URI přesměrování MSAL bude ve formátu: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Do souboru info. plist vaší aplikace přidejte následující schémata `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aktualizace kódu AppDelegate

Pouze pro iOS přidejte do souboru AppDelegate. m následující:

Cíl-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Pokud používáte Xcode 11**, měli byste místo toho umístit MSAL zpět do souboru `SceneDelegate`.
Pokud podporujete UISceneDelegate i UIApplicationDelegate pro zajištění kompatibility se staršími systémy iOS, je nutné MSAL zpětné volání umístit do obou souborů.

Cíl-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

To umožňuje MSAL zpracovávat odpovědi od služby Broker a webové komponenty.
To není nezbytné v ADAL, protože se automaticky swizzled metody delegáta aplikace. Ruční přidání je méně náchylné k chybám a poskytuje aplikaci větší kontrolu.

### <a name="enable-token-caching"></a>Povolit ukládání tokenů do mezipaměti

Ve výchozím nastavení MSAL ukládá do mezipaměti tokeny vaší aplikace v řetězci klíčů pro iOS nebo macOS. 

Postup povolení ukládání tokenů do mezipaměti:
1. Ujistěte se, že je aplikace správně podepsaná.
2. Přejít na nastavení projektu Xcode > **kartu možnosti** > **Povolení sdílení řetězce klíčů**
3. Klikněte na **+** a zadejte následující položku **skupin klíčů** : 3. a pro iOS zadejte `com.microsoft.adalcache` 3. b pro MacOS ENTER `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Vytvoření MSALPublicClientApplication a přepnutí na jeho acquireToken a acquireTokeSilent volání

`MSALPublicClientApplication` můžete vytvořit pomocí následujícího kódu:

Cíl-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Pak zavolejte rozhraní API pro správu účtů, abyste viděli, jestli v mezipaměti nejsou nějaké účty:

Cíl-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



nebo si přečtěte všechny účty:

Cíl-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Pokud se účet najde, zavolejte rozhraní MSAL `acquireTokenSilent` API:

Cíl-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Další kroky

Další informace o [tocích ověřování a scénářích aplikací](authentication-flows-app-scenarios.md)
