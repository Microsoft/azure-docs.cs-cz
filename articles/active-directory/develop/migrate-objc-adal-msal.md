---
title: Průvodce migrací ADAL to MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s rozdíly mezi MSAL pro iOS/macOS a Azure AD Ověřování knihovny pro ObjectiveC (ADAL. ObjC) a jak migrovat do MSAL pro iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 6050bdc8c2600998b9804b04b62102e74612719f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085173"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrace aplikací do MSAL pro iOS a macOS

Azure Active Directory Authentication Library[(ADAL Objective-C)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)byla vytvořena pro práci s účty Služby Azure Active Directory prostřednictvím koncového bodu v1.0.

Knihovna Microsoft Authentication Library pro iOS a macOS (MSAL) je vytvořená tak, aby fungovala se všemi identitami Microsoftu, jako jsou účty Azure Active Directory (Azure AD), osobní účty Microsoft a Účty Azure AD B2C prostřednictvím platformy identit Microsoftu (formálně koncový bod Azure AD v2.0).

Platforma identit Microsoftu má několik klíčových rozdílů ve službě Azure Active Directory v1.0. Tento článek zdůrazňuje tyto rozdíly a poskytuje pokyny pro migraci aplikace z ADAL do MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Rozdíly ve schopnostech aplikací ADAL a MSAL

### <a name="who-can-sign-in"></a>Kdo se může přihlásit

* ADAL podporuje jenom pracovní a školní účty – označované také jako účty Azure AD.
* MSAL podporuje osobní účty Microsoft (účty MSA), jako jsou Hotmail.com, Outlook.com a Live.com.
* MSAL podporuje pracovní a školní účty a účty Azure AD B2C.

### <a name="standards-compliance"></a>Dodržování norem

* Koncový bod platformy Identity Platform microsoftu se řídí standardy OAuth 2.0 a OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Přírůstkový a dynamický souhlas

* Koncový bod služby Azure Active Directory v1.0 vyžaduje, aby všechna oprávnění byla deklarována předem během registrace aplikace. To znamená, že tato oprávnění jsou statická.
* Platforma identit společnosti Microsoft umožňuje dynamicky požadovat oprávnění. Aplikace můžou žádat o oprávnění pouze podle potřeby a žádat více, jak je aplikace potřebuje.

Další informace o rozdílech mezi službou Azure Active Directory v1.0 a platformou microsoftových identit najdete v tématu [Proč aktualizovat na platformu identit Microsoftu (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="adal-and-msal-library-differences"></a>Rozdíly v knihovně ADAL a MSAL

Veřejné rozhraní API MSAL odráží několik klíčových rozdílů mezi Azure AD v1.0 a platformou identit Microsoftu.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>Aplikace MSALPublicClientApplication místo ADAuthenticationContext

`ADAuthenticationContext`je první objekt, který aplikace ADAL vytvoří. Představuje inkonkaci ADAL. Aplikace vytvoří novou `ADAuthenticationContext` instanci pro každou kombinaci cloudu a tenanta (autority) Služby Azure Active Directory. Totéž `ADAuthenticationContext` lze získat tokeny pro více veřejných klientských aplikací.

V MSAL, hlavní interakce `MSALPublicClientApplication` je prostřednictvím objektu, který je modelován podle [OAuth 2.0 Public Client](https://tools.ietf.org/html/rfc6749#section-2.1). Jedna instance `MSALPublicClientApplication` lze použít k interakci s více aad cloudy a tenanty, aniž by bylo nutné vytvořit novou instanci pro každou autoritu. Pro většinu aplikací `MSALPublicClientApplication` stačí jedna instance.

### <a name="scopes-instead-of-resources"></a>Obory namísto zdrojů

V ADAL aplikace musela poskytnout identifikátor `https://graph.microsoft.com` *prostředku,* jako je získání tokenů z koncového bodu Azure Active Directory v1.0. Prostředek můžete definovat počet oborů nebo oAuth2Permissions v manifestu aplikace, který rozumí. To umožnilo klientským aplikacím požadovat tokeny z tohoto prostředku pro určitou sadu oborů předdefinovaných během registrace aplikace.

V MSAL, namísto jednoho identifikátoru prostředku, aplikace poskytují sadu oborů na požadavek. Obor je identifikátor prostředku následovaný názvem oprávnění ve formuláři prostředku nebo oprávnění. Například `https://graph.microsoft.com/user.read`.

Existují dva způsoby, jak poskytnout obory v MSAL:

* Zadejte seznam všech oprávnění, která vaše aplikace potřebují. Například: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    V takovém případě aplikace požaduje `directory.read` `directory.write` a oprávnění. Uživatel bude požádán o souhlas s těmito oprávněními, pokud s nimi předtím nesouhlasil pro tuto aplikaci. Aplikace může také získat další oprávnění, která uživatel již souhlasil pro aplikaci. Uživatel bude vyzván pouze k souhlasu s novými oprávněními nebo oprávněními, která nebyla udělena.

* Obor. `/.default`

Toto je předdefinovaný obor pro každou aplikaci. Odkazuje na statický seznam oprávnění nakonfigurovaných při registraci aplikace. Jeho chování je podobné `resource`chování . To může být užitečné při migraci, aby bylo zajištěno, že je zachována podobná sada oborů a uživatelské hodování.

Chcete-li `/.default` použít obor, připojte `/.default` identifikátor prostředku. Například: `https://graph.microsoft.com/.default`. Pokud prostředek končí lomítkem (`/`), `/.default`měli byste stále připojit , včetně úvodníložek lomítko, výsledkem obor, který má dvojité lomítko (`//`) v něm.

Další informace o použití oboru "/.default" naleznete [zde](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Podpora různých typů WebView & prohlížečích

ADAL podporuje jenom UIWebView/WKWebView pro iOS a WebView pro macOS. MSAL pro iOS podporuje více možností pro zobrazení webového obsahu při `UIWebView`požadování autorizačního kódu a již podporuje ; které mohou zlepšit uživatelskou zkušenost a bezpečnost.

Ve výchozím nastavení používá MSAL v systému iOS [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), což je webová komponenta, kterou společnost Apple doporučuje pro ověřování na zařízeních se systémem iOS 12+. Poskytuje výhody jednotného přihlašování (SSO) prostřednictvím sdílení souborů cookie mezi aplikacemi a prohlížečem Safari.

V závislosti na požadavcích na aplikaci a požadovaném prostředí pro koncového uživatele můžete použít jinou webovou komponentu. Další možnosti najdete v [podporovaných typech webových zobrazení.](customize-webviews.md)

Při migraci z ADAL do `WKWebView` MSAL poskytuje uživatelské prostředí nejvíce podobné ADAL v iOS a macOS. Pokud je to `ASWebAuthenticationSession` možné, doporučujeme vám migrovat na iOS. Pro macOS doporučujeme používat `WKWebView`.

### <a name="account-management-api-differences"></a>Rozdíly rozhraní API pro správu účtů

Při volání `acquireToken()` Metody ADAL `acquireTokenSilent()`nebo , `ADUserInformation` obdržíte objekt obsahující seznam `id_token` deklarací z, který představuje účet, který je ověřován. Navíc `ADUserInformation` vrátí na `userId` základě `upn` deklarace. Po počáteční masy interaktivní token ADAL očekává, že vývojář poskytnout `userId` ve všech tichých volání.

ADAL neposkytuje rozhraní API pro načtení známých identit uživatelů. Spoléhá se na aplikaci pro uložení a správu těchto účtů.

MSAL poskytuje sadu api pro seznam všech účtů známých MSAL bez nutnosti získat token.

Podobně jako ADAL vrátí msal informace o účtu, který obsahuje seznam deklarací z `id_token`. Je součástí objektu `MSALAccount` uvnitř `MSALResult` objektu.

MSAL poskytuje sadu api pro odebrání účtů, takže odebrané účty nepřístupné pro aplikaci. Po odebrání účtu vyzve pozdější volání pořízení tokenu uživatele k interaktivnímu získání tokenu. Odebrání účtu se vztahuje pouze na klientskou aplikaci, která jej spustila, a neodebere účet z ostatních aplikací spuštěných v zařízení nebo z prohlížeče systému. Tím je zajištěno, že uživatel i nadále mít prostředí jednotného přihlašování na zařízení i po odhlášení z jednotlivé aplikace.

Kromě toho MSAL také vrátí identifikátor účtu, který lze použít k vyžádání tokenu tiše později. Identifikátor účtu (přístupný prostřednictvím `identifier` vlastnosti v objektu) `MSALAccount` však není zobrazitelný a nemůžete předpokládat, v jakém formátu je, ani se jej nepokoušíte interpretovat nebo analyzovat.

### <a name="migrating-the-account-cache"></a>Migrace mezipaměti účtů

Při migraci z ADAL aplikace obvykle ukládají ADAL `userId`, `identifier` který nemá požadované MSAL. Jako jednorázový krok migrace aplikace můžete dotaz ovat účet MSAL pomocí UserId ADAL s následujícím rozhraním API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Toto rozhraní API čte mezipaměti MSAL i ADAL, aby nalezlo účet podle UserId (UPN).

Pokud je účet nalezen, vývojář by měl použít účet k tomu, aby se zhodnocuje získání tokenu. První silent token akvizice bude efektivně upgradovat účet a vývojář získá identifikátor účtu kompatibilní s`identifier`MSAL ve výsledku MSAL ( ). Poté by `identifier` měl být použit pouze pro vyhledávání účtů pomocí následujícího rozhraní API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

I když je možné pokračovat v `userId` používání ADAL pro všechny `userId` operace v MSAL, protože je založen na upn, podléhá několika omezením, která mají za následek špatné uživatelské prostředí. Například pokud se změní hlavní upn, uživatel se musí znovu přihlásit. Doporučujeme, aby všechny aplikace používaly nezobrazitelný účet `identifier` pro všechny operace.

Přečtěte si další informace o [migraci stavu mezipaměti](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Změny pořízení tokenu

MSAL zavádí některé změny volání tokenu:

* Stejně jako `acquireTokenSilent` ADAL, vždy vede k tiché žádosti.
* Na rozdíl `acquireToken` od ADAL, vždy výsledky v uživatelském rozhraní použitelné ho buď prostřednictvím webového zobrazení nebo microsoft authenticator aplikace. V závislosti na stavu spřimit u zajistěte přístup k webovému zobrazení/Microsoft Authenticator, může být uživatel vyzván k zadání svých přihlašovacích údajů.
* V `acquireToken` ADAL, `AD_PROMPT_AUTO` s první pokusí silent token akvizice a zobrazí pouze uI, pokud tichý požadavek selže. V MSAL této logiky lze `acquireTokenSilent` dosáhnout prvním `acquireToken` voláním a pouze volání, pokud selže silent akvizice. To umožňuje vývojářům přizpůsobit uživatelské prostředí před spuštěním interaktivního získávání tokenů.

### <a name="error-handling-differences"></a>Zpracování rozdílů při chybách

MSAL poskytuje větší přehled mezi chybami, které mohou být zpracovány vaší aplikací a ty, které vyžadují zásah uživatelem. Existuje omezený počet chyb, které musí vývojář zpracovat:

* `MSALErrorInteractionRequired`: Uživatel musí provést interaktivní požadavek. To může být způsobeno z různých důvodů, jako je například vypršela platnost relace ověřování, zásady podmíněného přístupu se změnily, obnovovací token vypršela nebo byl odvolán, nejsou žádné platné tokeny v mezipaměti a tak dále.
* `MSALErrorServerDeclinedScopes`: Požadavek nebyl zcela dokončen a některým oborům nebyl udělen přístup. To může být způsobeno tím, že uživatel odmítl souhlas s jedním nebo více obory.

Zpracování všech ostatních chyb v [ `MSALError` seznamu](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) je volitelné. Informace v těchto chybách můžete použít ke zlepšení uživatelského prostředí.

Další informace o zpracování chyb MSAL naleznete [v tématu Zpracování výjimek a chyb pomocí služby MSAL.](msal-handling-exceptions.md)

### <a name="broker-support"></a>Podpora brokerů

MSAL, počínaje verzí 0.3.0, poskytuje podporu pro zprostředkované ověřování pomocí aplikace Microsoft Authenticator. Microsoft Authenticator také umožňuje podporu pro scénáře podmíněného přístupu. Příklady scénářů podmíněného přístupu zahrnují zásady dodržování předpisů zařízení, které vyžadují, aby uživatel zaregistroval zařízení prostřednictvím Intune nebo se zaregistroval u AAD, aby získal token. Zásady podmíněného přístupu pro správu mobilních aplikací (MAM), které vyžadují důkaz o dodržování předpisů, než vaše aplikace získá token.

Povolení zprostředkovatele pro vaši aplikaci:

1. Zaregistrujte formát IDENTIFIKÁTORURI kompatibilní s brokerem pro aplikaci. Formát identifikátoru URI kompatibilního s brokerem je `msauth.<app.bundle.id>://auth`. Nahraďte `<app.bundle.id>` id sady sady. Pokud migrujete z ADAL a vaše aplikace byla již schopna makléře, není nic navíc, co musíte udělat. Předchozí identifikátor URI přesměrování je plně kompatibilní s msal, takže můžete přeskočit na krok 3.

2. Přidejte schéma URI přesměrování aplikace do souboru info.plist. Pro výchozí identifikátor URI přesměrování msal `msauth.<app.bundle.id>`je formát . Například:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Přidejte následující schémata do aplikace Info.plist v části LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Do souboru AppDelegate.m přidejte následující: Objective-C:
    
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

### <a name="business-to-business-b2b"></a>B2B (Business to business)

V ADAL vytvoříte samostatné `ADAuthenticationContext` instance pro každého klienta, pro kterého aplikace požaduje tokeny. To již není požadavek v MSAL. V MSAL, můžete vytvořit jednu `MSALPublicClientApplication` instanci a použít ji pro všechny AAD cloud a organizace zadáním jiné ho oprávnění pro acquireToken a acquireTokenSilent volání.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO ve spolupráci s ostatními sadami SDK

MSAL pro iOS můžete dosáhnout jednotného přijmutí prostřednictvím jednotné mezipaměti s následujícími sadami SDK:

- ADAL Cíl-C 2.7.x+
- MSAL.NET pro Xamarin 2.4.x+
- ADAL.NET pro Xamarin 4.4.x+

SSO se dosahuje prostřednictvím sdílení klíčů iOS a je k dispozici pouze mezi aplikacemi publikovanými ze stejného účtu Apple Developer.

Přihlašovací akce prostřednictvím sdílení klíčů iOS je jediný tichý typ spři.

V macOS může MSAL dosáhnout sazí s dalšími aplikacemi založenými na MSAL pro iOS a macOS a aplikacích založených na ADAL Objective-C.

MSAL na iOS také podporuje dva další typy sanace:

* Připojto prostřednictvím webového prohlížeče. MSAL pro iOS podporuje `ASWebAuthenticationSession`, který poskytuje automatické přihlašovací služby prostřednictvím souborů cookie sdílených mezi jinými aplikacemi v zařízení a konkrétně prohlížeče Safari.
* Přizpůsobte si služby sazí prostřednictvím zprostředkovatele ověřování. Na iOS zařízení Microsoft Authenticator funguje jako zprostředkovatel ověřování. Může dodržovat zásady podmíněného přístupu, jako je vyžadování kompatibilního zařízení, a poskytuje sociální zabezpečení pro registrovaná zařízení. Sady MSAL SDK začínající verzí 0.3.0 podporují zprostředkovatele ve výchozím nastavení.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[Intune MAM SDK](https://docs.microsoft.com/intune/app-sdk-get-started) podporuje MSAL pro iOS počínaje verzí [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL a ADAL ve stejné aplikaci

ADAL verze 2.7.0 a vyšší, nemůže koexistovat s MSAL ve stejné aplikaci. Hlavním důvodem je z důvodu společného kódu sdíleného podmodulu. Vzhledem k tomu, že Objective-C nepodporuje obory názvů, pokud přidáte rámce ADAL a MSAL do aplikace, budou existovat dvě instance stejné třídy. Neexistuje žádná záruka, pro kterou je jeden vybrán za běhu. Pokud obě sady SDK používají stejnou verzi konfliktní třídy, může aplikace stále fungovat. Pokud se však jedná o jinou verzi, může dojít k neočekávaným chybám, které je obtížné diagnostikovat.

Spuštění ADAL a MSAL ve stejné produkční aplikaci není podporováno. Pokud však právě testujete a migrujete uživatele z ADAL Objective-C na MSAL pro iOS a macOS, můžete pokračovat v používání [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Je to jediná verze, která pracuje s MSAL ve stejné aplikaci. Pro tuto verzi ADAL nebudou k dispozici žádné nové aktualizace funkcí, takže by měla být použita pouze pro účely migrace a testování. Vaše aplikace by neměla spoléhat na ADAL a MSAL koexistence dlouhodobě.

ADAL a MSAL koexistence ve stejné aplikaci není podporována.
ADAL a MSAL koexistence mezi více aplikacemi je plně podporována.

## <a name="practical-migration-steps"></a>Praktické kroky migrace

### <a name="app-registration-migration"></a>Migrace registrace aplikace

Nemusíte měnit stávající aplikaci AAD, abyste mohli přepnout na MSAL a povolit účty AAD. Pokud však vaše aplikace založená na ADAL nepodporuje zprostředkované ověřování, budete muset před přepnutím do msal zaregistrovat nový identifikátor URI přesměrování pro aplikaci.

Identifikátor URI přesměrování by měl `msauth.<app.bundle.id>://auth`být v tomto formátu: . Nahraďte `<app.bundle.id>` id sady sady. Zadejte identifikátor URI přesměrování na [webu Azure Portal](https://aka.ms/MobileAppReg).

Pouze pro iOS, pro podporu ověřování na základě certifikátu, další přesměrování URI musí být registrovány `msauth://code/<broker-redirect-uri-in-url-encoded-form>`ve vaší aplikaci a na portálu Azure v následujícím formátu: . Například `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`.

Doporučujeme, aby všechny aplikace zaregistrovaly jak identifikátory URI přesměrování.

Pokud chcete přidat podporu pro přírůstkový souhlas, vyberte rozhraní API a oprávnění, ke kterým je vaše aplikace nakonfigurovaná, aby požadovala přístup k registraci aplikace na kartě **Oprávnění rozhraní API.**

Pokud migrujete z ADAL a chcete podporovat účty AAD i MSA, je třeba aktualizovat stávající registraci aplikace, aby podporovala obojí. Nedoporučujeme aktualizovat stávající produkční aplikaci tak, aby podporovala aad i MSA hned. Místo toho vytvořte další ID klienta, které podporuje AAD i MSA pro testování a po ověření, že všechny scénáře fungují, aktualizujte stávající aplikaci.

### <a name="add-msal-to-your-app"></a>Přidání msal do aplikace

MSAL SDK můžete přidat do aplikace pomocí upřednostňovaného nástroje pro správu balíčků. Podrobné pokyny naleznete [zde](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aktualizace souboru Info.plist aplikace

Pouze pro iOS přidejte schéma URI přesměrování aplikace do souboru info.plist. Pro adal broker kompatibilní aplikace, měl by být tam už. Výchozí schéma identifikátoru URI přesměrování MSAL `msauth.<app.bundle.id>`bude ve formátu: .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Přidejte následující schémata do seznamu Info.plist aplikace v části `LSApplicationQueriesSchemes`.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aktualizace kódu AppDelegate

Pouze pro iOS přidejte do souboru AppDelegate.m následující:

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

**Pokud používáte Xcode 11**, měli byste místo `SceneDelegate` toho umístit zpětné volání MSAL do souboru.
Pokud podporujete uISceneDelegate a UIApplicationDelegate pro kompatibilitu se starším iOS, bude nutné umístit zpětné volání MSAL do obou souborů.

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

To umožňuje MSAL zpracovat odpovědi z brokera a webové komponenty.
To nebylo nutné v ADAL, protože "swizzled" metody delegáta aplikace automaticky. Ruční přidání je méně náchylné k chybám a poskytuje aplikaci větší kontrolu.

### <a name="enable-token-caching"></a>Povolit ukládání tokenů do mezipaměti

Ve výchozím nastavení msal ukládá tokeny vaší aplikace v řetězci klíčů iOS nebo macOS. 

Povolení ukládání tokenů do mezipaměti:
1. Ujistěte se, že je vaše žádost řádně podepsána
2. Přejděte na kartu > Nastavení projektu Xcode > **možnosti****Povolit sdílení klíčů**
3. Klikněte **+** a zadejte následující položku **Skupiny klíčů:** `com.microsoft.adalcache` 3.a Pro iOS zadejte 3.b Pro macOS zadejte`com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Vytvořit MSALPublicClientApplication a přepnout na jeho acquireToken a získatTokeSilent volání

Můžete vytvořit `MSALPublicClientApplication` pomocí následujícího kódu:

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

Potom zavolejte rozhraní API pro správu účtů a zjistěte, zda v mezipaměti nejsou nějaké účty:

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



Pokud je nalezen účet, zavolejte `acquireTokenSilent` rozhraní MSAL API:

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
